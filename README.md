# iOS-perf

[READ IN ENGLISH](./README_EN.md)

這是一款iOS性能監控工具，支持Mac以及Windows端運行，電腦通過USB連接手機後運行腳本即可。

當前支持獲取的性能數據包括GPU、CPU、內存、FPS、功耗、網絡、溫度，以及一系列手機硬件數據，並將根據需求繼續新增。

本項目基於jlintxia開源的iOS測試方案修改而來，增加動態建表，動態增加grafana面板以及docker打包環境等特性。其中iOS性能數據來源於開源工具tidevice和py-ios-device。


注意：本項目依賴MySQL進行性能數據存儲，Grafana進行數據動態展示，也就是說需要在本機或者可達的網絡（比如公司局域網）
上搭建MySQL+Grafana服務，我提供了一份docker-compose.yml文件，可以使用docker快速搭建一套環境。



## 效果展示

![](iOS-perf-3x.gif)

  





## 準備工作

服務端搭建依賴docker以及docker-compose，安裝指南：

>https://dockerdocs.cn/get-docker/
>
>https://dockerdocs.cn/get-started/08_using_compose/

運行測試依賴python3環境，安裝指南：

>https://www.python.org/downloads/


### 服務搭建

cmd執行

```
docker -v && docker-compose -v
```

如果能正常輸出版本，如下，則表示docker環境正常，可以繼續

>Docker version 20.10.8, build 3967b7d
>
>docker-compose version 1.29.2, build 5becea4c

拉取鏡像並啟動服務：

```
docker-compose up -d  
```
**提示：初次打開`Grafana`時，系統會提示你修改密碼，為了方便建議不修改，即保持賬號密碼均為`admin`，否則在python運行指令中將要進行對應的傳參。 **



### 本地環境配置

CMD執行

```
pip install -r requirements.txt
```








## 運行命令
命令行執行：
```shell
python run.py --udid=00008110-001A4D483CF2801E \
--bundleid=com.apple.Preferences \
--grafana_host=localhost \
--grafana_port=30000 \
--grafana_user=admin \
--grafana_password=admin \
--mysql_host=localhost \
--mysql_port=33306 \
--mysql_username=root \
--mysql_password=admin \
--mysql_db=iOSPerformance
```


### 運行參數說明



#### 需要修改的參數

>- --bundleid：待测APP的包名，通过`ideviceinstaller -l`获取，默认值为`com.apple.Preferences`
>- --udid iPhone：手机的唯一标识符，通过 `idevice_id -l` 获取，客户端只连接一台手机时不用写



#### Grafana可以設定的參數

> - --grafana_host：Grafana的主机地址，只写ip，不用写Scheme，也就是`http://`或者`https//`，默认值localhost
> - --grafana_port：Grafana的端口号，默认值30000
> - --grafana_user：Grafana的用户名，默认值admin
> - --grafana_password：Grafana的密码，默认值admin



#### MySQL參數

> - --mysql_host：MySQL的主机地址，不用写Scheme，也就是`http://`或者`https//`，默认值localhost
> - --mysql_port：MySQL的端口号，默认值33306
> - --mysql_user：MySQL的用户名，默认值root
> - --mysql_password：MySQL的密码，默认值admin



## 數據導出

命令執行：
```shell
python mysql.py --runid=iphone6_1008_1532 \
--mysql_host=localhost \
--mysql_port=33306 \
--mysql_username=root \
--mysql_password=admin \
--mysql_db=iOSPerformance
```

其中，`--runid`為必須參數，可以從顯示測試數據的Grafana頁面的左上角找到，通常為手機名稱+月日+時分。其餘Mysql參數均為可選參數，默認值與上方[MySQL參數](#MySQL可選參數)相同。

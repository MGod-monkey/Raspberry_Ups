# 树莓派 UPS

---

描述：

项目参考：[https://github.com/tjohn327/raspberry_pi_ups](https://github.com/tjohn327/raspberry_pi_ups)

  树莓派不间断电源系统，用于为树莓派提供电源。  参考国外大佬的开源项目，并对项目中板子做了调整，修复了原项目中的部分bug，并适配中文版本。树莓派3b和树莓派4b引脚一样，因此本项目适用于树莓派3b和树莓派4b。理论支持所有版本的树莓派，但其余板子需要重新画PCB，将OUTPUT接到树莓派的5V引脚上。

 特性：

- 不间断电源，当板子上的type-c输入接上时，树莓派由外部电源供电，同时给内部电池充电，当输入断开时，由内部电池供电，实测6000mHA锂电池可供电3-4h
- 扩接树莓派GPIO引脚，同时引出两个I2C接口，用于扩展外部设备
- 拥有两个5V风扇接口，可用于散热风扇的使用
- 预留一个2.5x2.5cm的散热风扇放置口，摄像头排线口和屏幕排线口
- 项目内置电压电量检测脚本，可开机自启并通过网页查看到电池电量信息

## - 硬件部分

* Input:  4.5V - 14V DC, 2A - 5A
* Output: 5V, up to 3A
<p></p>

> 说明：两个I2C接口引脚从‘+’符号往后依次为: 5V-->GND->SCL->SDA

<p></p>

<details> <summary>通过项目中的gender文件可以在嘉立创下单平台一键下单，项目用到的部分硬件淘宝链接如下（展开查看）</summary>
<p></p>

-【Type-C母座】[https://item.taobao.com/item.htm?spm=a1z09.2.0.0.14432e8dOjq5sL&id=620536500953&_u=52htfi0p1801](https://item.taobao.com/item.htm?spm=a1z09.2.0.0.14432e8dOjq5sL&id=620536500953&_u=52htfi0p1801)
<p></p>

-【拨动开关】[https://detail.tmall.com/item.htm?id=607540947052&spm=a1z0d.6639537/tb.1997196601.28.481a7484wjLJUf&skuId=4261603081609](https://detail.tmall.com/item.htm?id=607540947052&spm=a1z0d.6639537/tb.1997196601.28.481a7484wjLJUf&skuId=4261603081609)
<p></p>

【1.25mm插座】[https://detail.tmall.com/item.htm?id=626531190062&spm=a1z0d.6639537/tb.1997196601.4.481a7484wjLJUf&skuId=4438509520205](https://detail.tmall.com/item.htm?id=626531190062&spm=a1z0d.6639537/tb.1997196601.4.481a7484wjLJUf&skuId=4438509520205)
<p></p>

【1.25mm接线】[https://detail.tmall.com/item.htm?id=624732974819&spm=a1z0d.6639537/tb.1997196601.18.481a7484wjLJUf&sku_properties=1627207:14415883024](https://detail.tmall.com/item.htm?id=624732974819&spm=a1z0d.6639537/tb.1997196601.18.481a7484wjLJUf&sku_properties=1627207:14415883024)
<p></p>

【双排加长排针】[https://item.taobao.com/item.htm?spm=a1z09.2.0.0.2e4a2e8dPEDjnU&id=634945644359&_u=52htfi0p417e](https://item.taobao.com/item.htm?spm=a1z09.2.0.0.2e4a2e8dPEDjnU&id=634945644359&_u=52htfi0p417e)
<p></p>

> 说明：电源管理芯片[BQ25895](http://www.ti.com/product/BQ25895)和稳压芯片[TPS61236P](http://www.ti.com/product/TPS61236P)可以在立创商城买正品，成本50左右，也可以在淘宝买几块的散装（有不能用的风险），锂电池购买看自己需求，可以用18650也可以用电芯，只要是3.7V输出即可，总成本在80左右
</details>


---

## - 软件部分

### 1. 焊好双排针（也可以只焊上面的5V和GND引脚）和锂电池，然后插到树莓派上，接上板子上的Type-C输入，通电打开开关以启动树莓派


> 注：UPS板子上的Type-C和树莓派上的Type-C不共容，接板子上的Type-C后就不能接树莓派上的，否则容易导致电路损坏


### 2. 打开树莓派4b的I2C和GPIO

> ```shell
> sudo raspi-config
> ```

依次选择pi -> Interface Options -> I2C/GPIO -> enable

![rasp_I2C](https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/rasp_I2C.png)

### 3. 安装树莓派中i2c驱动库smbus

   > ```shell
   > sudo apt-get install -y python-smbus
   > ```

### 4.  安装UPS服务

   克隆仓库到树莓派上，并安装服务

   > ```shell
   > cd ~
   > git clone https://github.com/mgodmonkey/raspberry_ups.git
   > cd raspberry_ups/Software/
   > ```

  > 说明：网络不好时可以下载到本地，再通过sftp传到树莓派上

​      打开power.py修改配置，电池容量一定要改，电池电压实在无法测量也可以不改，就是测出来的值可能不准确

  > sudo nano power.py

​		<img src="https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_config.png" alt="ups_config" style="zoom:60%;" />

ctrl+o保存ctrl+x退出，接下来就一键安装就行

  > ```shell
  > sudo chmod +x install.sh
  > sudo ./install.sh
  > ```

检查ups.server是否在运行，见到如下画面证明你以成功安装，默认情况下ups服务是不输出的，通过40001端口的upd不断传送数据，当断开电源或电量不足时会在终端输出

  > ```shell
  > sudo systemctl status ups.service
  > ```

![ups_success](https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_success.png)

### 5.安装Node-Red控制台，方便在网页上直观的显示数据

在安装node-red之前先放行树莓派端口，以防后面出错

  > ```shell
  > sudo ufw allow 1880
  > ```

安装node-red依赖build-essential

> ```shell
> sudo apt update
> sudo apt install build-essential
> ```

运行下面命令安装node-red，这些包包括Node.js、npm和Node-RED本身

> ```shell
> bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
> ```

> **注：回车没反应就是网络不好，将上面的网站替换为https://raw.fastgit.org/node-red/linux-installers/master/deb/update-nodejs-and-nodered，脚本运行时会有确认选项，默认为No，需要手动输入y回车确认，全部确认后会自动安装，全部安装完成后会提示是否要自定义node-red配置，这时候直接回车或者输入N回车即可，完成之后建议重启**

### 6.部署Node-Red节点

复制配置文件到node-red，并且安装node-red控制台

> ```shell
> cp ups_flow.json ~/.node-red/lib/flows/ups_flow.json
> cd ~/.node-red/
> npm i node-red-dashboard
> ```

启动node-red服务，直接启动或者设置开机自启

> ```shell
> sudo node-red
> sudo systemctl enable nodered.service
> sudo systemctl start nodered.service
> ```

访问网页http://{树莓派IP}:1880，进入后台，然后按照下面顺序依次点击，最后访问http://{树莓派IP}:1880/ui即可看到ups数据

<center class="half">
<img src="https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_1.png" style="zoom:45%;"/>
<img src="https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_2.png" style="zoom:40%;"/>
<p></p>
<img src="https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_3.png" style="zoom:100%;"/>
<img src="https://github.com/MGod-monkey/Raspberry_Ups/tree/master/Images/ups_ui.png" alt="ups_ui" style="zoom:60%;" />
</center>



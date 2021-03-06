# 基于树莓派的智能镜子的设计与实现

​                                                                                                                                     ——HasiyMagicMirror

## 硬件

#### 01：树莓派

![](1.png)
* 使用的树莓派是 1B 512M 版本的
* 断电 开机 自动打开chromium浏览器 （全屏显示主页 ’ hasiymagicmirror.top ‘ ）  (屏幕横竖屏状态调整)
* 识别传感器 读取数据 回报给 web端
* 待机时间 30min 关闭屏幕（检测是否镜子前面有没有人 有则继续亮屏【之后每隔5min检测一次】）
* VNC远程控制（IP：设置静态地址（或者在路由器里找设备ip）屏幕 0号端口  用户名：pi 密码为：zxszxs）

#### 02：DHT22 温度传感器
* 树莓派GPIO驱动  读取当时温度与湿度 动态回报给web端

#### 03：HC-SR501 人体红外传感器 (后期可能替换为GY-9960 红外手势传感器)
* 树莓派GPIO驱动  当有人走近镜子就 让屏幕亮屏显示内容
* 或者是人 挥手 让屏幕亮屏显示内容

#### 04：MAX30102 心率血氧传感器（后期再添加视情况而定） ，
* 树莓派GPIO驱动  人手指放在传感器上识别  数据回报给web端
* 在屏幕上以 波形图显示心率血氧

#### 05：二手显示器 HDMI接口的（供电）  大概14寸
* 最低要求 没有明显的物理损坏
* 外壳后期拆掉 厚度尽量薄点

#### 06：原子镜
*按照屏幕大小来


## 软件（web）

#### 01：天气  （聚合API。本地温度传感器）
* 地点今天 天气  风速  气温 ） 穿衣建议    （自动根据ip获取大致地点）
* 最近三天的天气显示 样式参考 雅虎天气
#### 02：日历（农历） 时间
* demo: 2018-01-12 星期五
     * 丁酉年 【鸡年】
     * 癸丑月 甲辰日

#### 03：个人安排 （生日提醒等）
* demo1: 今天 XX生日记得礼物
* demo2：今天 工作安排
* demo3：今天 有安排旅行地点（祝旅途愉快）
* demo4: 今天 XX纪念日
* 其他等...

#### 04：新闻预览

* 科技新闻 在屏幕低端显示（不干扰视线）

#### 05：室内温度 湿度（DHT22 获取传给网页 JSON格式）
* 显示当前室内温度 湿度 和 01 天气一起显示

#### 06：邮件推送 当天天气 安排 到个人邮箱
* 0601 ：推送 天气 安排等等到 个人邮箱
* 0602 ：推送 树莓派当前运行状况
  *  CPU温度使用情况
  *  内存使用情况

#### 07：周围交通情况（地图显示）

* 后期添加 以图显示  显示主干道交通情况（用聚合API 根据外网ip识别）

#### 08：后台管理内容( 需要账号登录)

* 01 地点

* 02 日期

* 03 个人安排录入（最好可以手机上设置）

* 04 新闻喜好类型调整

* 06 是否推送邮箱

* 07 是否开启交通情况




## TODO 用openVC+ csi 摄像头 人脸识别
* 能够在识别人物后 调整显示内容
  * demo1：站在镜子前能显示 XX先生早上好或XX女生早上好
  * demo2：女主人在镜子前 替换成女主人喜欢的新闻源
  * demo3：个人安排的显示 对应人物的内容
  * 

# 安装 

安装 Nginx 和 PHP7
在终端运行以下命令

```sh
sudo apt-get update
sudo apt-get install nginx php7.0-fpm php7.0-cli php7.0-curl php7.0-gd php7.0-mcrypt php7.0-cgi
sudo service nginx start
sudo service php7.0-fpm restart
```

如果安装成功，可通过 http://树莓派IP 访问到 Nginx 的默认页。

Nginx 的根目录在 cd /var/www/html。
进行以下操作来让 Nginx 能处理 PHP

```shell
sudo vim /etc/nginx/sites-available/default
```


```shell
location / {
# First attempt to serve request as file, then
# as directory, then fall back to displaying a 404.
try_files $uri $uri/ =404;
}
```
替换为
```shell
location / {
index  index.html index.htm index.php default.html default.htm default.php;
}
```
```shell
location ~\.php$ {
fastcgi_pass unix:/run/php/php7.0-fpm.sock;fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
include fastcgi_params;
}
```
```shell
sudo service nginx restart
```

最后重启 Nginx 即可，以上步骤在树莓派3B + Raspbian Stretch 系统版本上测试通过。


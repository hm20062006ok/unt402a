# 湖南移动 九联UNT402A 刷入Android9(MTV) 安装TV-BOX 连接Alist-tvbox
## 目的
废物利用. 希望能实现在盒子上安装一个干净的系统, 连接小雅alist的资源, 抓取配置好本地IPTV直播源. 
盒子开机后能傻瓜式进入播放器
## 刷入Android 9
UNT402A自带的Android系统版本太低(应该是4.4), 较新版本的TV-BOX不支持安装, 虽然也能找到支持4.4的 

### 准备:
1. 一条双公头USB线
2. USB_Burning_tool_v2.1.7(刷入img的工具 )[来源](https://androidmtk.com/download-amlogic-usb-burning-tool)
3. android_tv_unt402a_s905l3.tar.gz(Android 9系统, 查看Release页面)[来源](https://github.com/ophub/kernel/releases/tag/tools)
 
USB_Burning_tool较新的版本(比如3.2)读取系统img时会报错, 2.2xx的版本在win10下出现过缺少dll库, 2.1.7可以顺利安装使用, 请自行尝试不同版本
### 步骤
1. 安装好USB_Burning_tool后, 导入.img格式的系统包
2. 双公头线连接好电脑和盒子, (a)在USB_Burning_tool点击开始, (b)短接一下盒子主板下图位置. (a,b 步骤或许可以重复/调转, 请自行尝试) 
   ![主板](/board.png "board.png") 
3. 如果出现错误可以尝试重新插拔USB线连接,再次执行刷机

### 安装TV-BOX
找个U盘放入TVBox_xxx.apk, 插入盒子, 开机, 文件管理, 安装
### 连接Alist-tvbox
参考: https://github.com/power721/alist-tvbox/blob/master/doc/README_zh.md

### 打开网络adb调试
通过重新解包/打包img， 修改build.prop实现
尝试了三个工具[CustomizationTool](https://rdsoftwaregineering.wordpress.com/customization-tool/), [linux-amlogic-toolkit](https://github.com/natinusala/linux-amlogic-toolkit),  [AmlogicKitchen](https://github.com/althafvly/AmlogicKitchen). 前两个打包出来的ROM刷机安装后会卡， 不知道为啥。 AmlogicKitchen好使。 
#### 使用AmlogicKitchen解包、修改build.prop, 打包

```
// 下载
git clone https://github.com/xKern/AmlogicKitchen.git
cd AmlogicKitchen
git submodule update --init --recursive

// 将要解包的.img 复制到工作目录
# pwd
//root/AmlogicKitchen
# mkdir in
# cp ~/android_tv_unt402a_s905l3.img ./in

// 运行解包脚本
# ./unpack.sh

// 修改level2/system/build.prop
# vim build.prop
打开 service.adb.tcp.port=5555 的注释
打开 ro.adb.secure=0 的注释

// 修改level2/system/etc/prop.default
# vim prop.default
ro.secure=0
ro.adb.secure=0
persist.sys.usb.config=adb

// 运行重新打包脚本
# pwd
// /root/AmlogicKitchen
# ./pack.sh
提示Select level 1,2,3 or q/Q to exit 时，依次选择3,2,1然后q
打包完成的img在out目录
```

### TODO
* 获取直播源
* 更改.img 自启动指定APP (参考: https://dpkg123.site/2023/12/24/android%20boot.img%20%E6%96%87%E4%BB%B6%E7%9A%84%E8%A7%A3%E5%8C%85%E3%80%81%E4%BF%AE%E6%94%B9%E4%B8%8E%E9%87%8D%E6%89%93%E5%8C%85%E5%B0%8F%E8%AE%B0/)
* u盘安装Armbian(参考:https://github.com/ophub/amlogic-s9xxx-armbian/issues/2347)



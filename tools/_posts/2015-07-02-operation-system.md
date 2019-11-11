---
title: 操作系统常见操作
---

# mac查看本机ip

```
ipconfig getifaddr en0
```

# mac微信多开

```
open -n /Applications/WeChat.app/Contents/MacOS/WeChat
```




# msdn我告诉你

[http://msdn.itellyou.cn/][6]下载各种资源, 如纯净版windows操作系统

[6]: http://msdn.itellyou.cn/

# mac在finder中打开终端所在目录命令

```
open .
```

[1]: ed2k://|file|cn_windows_7_ultimate_with_sp1_x64_dvd_u_677408.iso|3420557312|B58548681854236C7939003B583A8078|/
[2]: http://ftp-idc.pconline.com.cn/6340a847d4c3bd4591696b047a7411f0/5100000046556988845/uiso9_cn/pub/download/201010/maldner.exe
[3]: https://cloud.githubusercontent.com/assets/5894015/9020973/5d737118-385e-11e5-8da3-66f1db17cdf0.jpg
[4]: http://www.irradiatedsoftware.com/sizeup/
[5]: http://manytricks.com/moom/

## mac查看端口占用程序并关闭

```
Failed to listen on 127.0.0.1:9001 (reason: Address already in use)
```

本机启动服务器时报错, 需要找出程序pid然后关闭. 方法如下:

找出程序

```
lsof -i :9001
```

关闭程序

```
kill -9 <PID>
```

# mac 添加环境变量

```
export PATH=/opt/apache-maven-3.5.0/bin:$PATH
```

mima: `a`




## mac程序全屏切换快捷键

`Cmd + ctrl + f`

## mac工具软件,设置

1. 全键盘模式: System Preferences --> Keyboard --> Shortcuts --> All controls
2. 快速锁定屏幕:
  1. 快捷键: `option + Cmd + Power`
  2. 鼠标屏幕角事件: System Preferences -> Desktop & Screen Saver -> Screen Saver -> Hot Corners... 在需要定义事件的角选择: Put Display to Sleep
3. 比Tab + Cmd更方便的程序切换方法: [Manico][http://manico.im/]将程序编号option + key即可快速切换

### 调整窗口位置工具

1. [sizeup][4]: 通过快捷键调整窗口位置
2. [moom][5]: 鼠标移入右上角按钮处弹出调整窗口选项


- http://support.alfredapp.com/

## 联想电脑输入文字时光标乱跳

触摸板太灵活,去官网下载对应电脑触摸板驱动,设置灵敏度

## 如何使用U盘重装win7

包括以下步骤:

1. 制作启动U盘
2. 设置电脑从U盘启动
3. 重启电脑从U盘安装

以下为详细操作

### 制作启动U盘

准备一个大于6G的U盘

1. 下载微软官方纯净win7 ISO安装文件如[cn_windows_7_ultimate_with_sp1_x64][1]
2. 下载[UltraISO][2]并安装
3. U盘插到电脑
4. 运行UltraISO, 菜单选择: 文件 --> 打开. 选择ISO文件,点击打开
5. 菜单选择: 启动 --> 写入磁盘映像...
6. 对话框中选择硬盘驱动器为U盘, 检查映像文件路径, 分别点击: **格式化**, **写入**
7. 写入完成后U盘会显示安装盘图标

完成以上步骤就成功制作了启动U盘

### 设置电脑从U盘启动

1. 查询电脑如何设置BIOS启动. 此处以联想Y430P为例
2. 启动电脑时不停按F2, 打开BIOS设置
3. 按左右键将最顶部导航到Boot
4. 设置Boot Mode为**[Legacy Support]**
5. 设置Boot Priority为**[Legacy First]**
6. 设置USB Boot为**[Enabled]**
7. 在底部Legacy列表中选择U盘对应项, 上移到第一行
8. 按F10保存并重启

![][3]

### 启动安装

插上制作成启动盘的U盘并设置系统启动到U盘后,启动电脑会进入U盘进行系统安装

1. 设置好,重启之后安装类型选**自定义(高级)**
2. 选择安装到C盘, 安装完成后电脑重启, 此时拔出U盘
3. 此时系统会进入刚安装的系统并进行必要的初始化


## mac下文件大于4G无法拷贝到U盘

U盘文件格式不对, 应该格式化为exFAT, 步骤

1. 点击屏幕右上角的搜索图标
2. 搜索并打开磁盘工具
3. 左侧选择U盘, 右侧选择抹掉,格式选择exFAT

完成之后就可以复制了


## 文本对比工具

- http://www.scootersoftware.com/v4help/index.html?using_text_merge.html

## linux删除文件夹命令

```
rm -rf dir
```

## linux为文件创建软链接(快捷方式)

```
ln -s srcFile targetFile
```

在targetFile处为srcFile创建软连接, 常见应用

## 在项目目录下创建host文件的快捷方式, 方便直接绑定host

```
ln -s /etc/hosts /Users/qiudeqing/Desktop/projects/hosts
```

执行以上命令之后projects目录下就有一个hosts文件, 对它的修改会自动映射到`/etc/hosts`方便操作

## 在项目目录下创建nginx配置文件快捷方式,方便配置

```
ln -s /usr/local/etc/nginx/nginx.conf  /Users/qiudeqing/Desktop/projects/nginx.conf
```

## mac如何让Finder显示隐藏文件和文件夹

终端执行:
```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```

## mac如何让Fider隐藏特殊文件和文件夹

终端执行:
```
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```


# mac工具

manico

sizeup

视频格式转换工具:

- wondershare video converter(收费)
- handBrake(免费)

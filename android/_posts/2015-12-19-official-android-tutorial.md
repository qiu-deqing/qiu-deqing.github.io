---
title: android官方教程-简化版
---

从[http://developer.android.com/training/index.html][1]教程摘录的部分.

# 创建你的第一个app


开始学习之前要先搭建开发环境:

1. 下载并安装[Android Studio][2]
2. 使用[SDK Manager][3]下载最新SDK工具

教程大部分使用Android Studio, 有的步骤也需要从命令行使用SDK工具.

## 创建一个Android项目


### 使用Android Studio新建项目

1. 在Android Studio中选择新建项目

  - 如果当前没有打开项目, 在欢迎页点击 **New Project**
  - 如果当前有打开项目, 依次在菜单选择: **File** -> **New** -> **New Project**

2. 在新建对话框中填写基本信息, 点击 **Next**

  - **Application Name**是显示给用户的app名字. 例如"My First Aapp"
  - **Company domain**提供包名
  - **Package name**通常根据Company domain自动生成, 也可以编辑
  - **Project location**是项目保存目录

3. 在**Select the form factors your app will run on**中选择**Phone and Tablet**
4. 在**Minimum SDK**中选择**API 8: Android 2.2(Froyo)**
5. 其他复选框留空, 点击**Next**
6. 在**Add an activity to Mobile**中选择, **Blank Activity**, 点击**Next**
7. 在**Customize the Activity**中将**Activity Name**改为MyActivity. **Layout Name**改为activity_my, **Title**改为MyActivity, **Menu Resource Name**改为menu_my
8. 点击**Finish**完成项目创建

此时一个简单的"Hello World" app创建好了, 其中包含一些默认文件:

`app/src/main/res/layout/activity_my.xml` : 这是新建项目时创建的activity的布局文件.

`app/src/main/res/layout/content_my.xml` : 这个布局文件在`activity_my.xml`中被引用

`app/src/main/java/com.mycompany.myfirstapp/MyActivity.java` : activity的定义. 当编译运行app时, 系统启动这个activity并且加载对应的布局文件

`app/src/main/AndroidManifest.xml` : 描述app的基本特性并且定义组件.

`app/build.gradle` : Android Studio 使用Gradle编译生成app. 项目的每一个模块都有一个`build.gradle`文件, 项目本身也有一个`build.gradle`文件. 通常只需要关注模块里面的配置文件. 在这里保存了app生成所依赖的组件, 也包含`defaultConfig`设置:

- `compiledSdkVersion` 指定app编译的目标平台. 默认只为SDK最新版本.
- `applicationId` 创建项目时指定的包名
- `minSdkVersion` 创建项目时指定的最低支持的SDK版本
- `targetSdkVersion` app所测试的最高Android版本. 当新版本Android出来的时候, 你应该在上面测试app然后尝试使用最新的API获取更强功能.

查看[Building Your Project with Gradle][4]了解Gradle更多信息.

`/res`目录下包含app所需的资源

`drawable-<density>/` : 可绘制资源

`layout/` : 包含布局文件

`menu/` : 包含app菜单项

`mipmap/` : 启动icon, 包含`ic_launcher.png`

`values/` : 包含字符串, 颜色资源定义文件


## 运行App

### 在真机上运行

1. 使用USB将手机连接到电脑.
2. 启动**USB 调试**功能.
3. 选择项目, 点击工具栏的**Run**按钮
4. 在**Choose Device**窗口中选择**Choose a running device**, 选择连接的手机. 点击**OK**

此时Android Studio会将app安装到设备并启动.

### 在模拟器上运行

1. 如果没有创建过虚拟设备, 需要创建一个[Adnroid Virtual Device][5]

  1. 启动Android Virtual Device Manager: 菜单依次选择 **Tools** -> **Android** -> **AVD Manager**
  2. 点击**Create Virtual Device**
  3. 选择一个设备, 点击**Next**
  4. 选择系统版本, 点击**Next**
  5. 检查配置, 点击**Next**

2. 在Android Studio中点击工具栏绿色**Run**
3. **Choose Device**处选择**Launch Emulator**
4. 下拉**Android virtual device**选择一个模拟器, 点击**OK**

启动过程可能需要几分钟.


[5]: http://developer.android.com/tools/devices/index.html
[4]: http://developer.android.com/sdk/installing/studio-build.html
[3]: http://developer.android.com/tools/help/sdk-manager.html
[2]: http://developer.android.com/sdk/index.html
[1]: http://developer.android.com/training/index.html

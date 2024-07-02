---
title: IntelliJ IDEA基础
---



# 快捷键

- `command + d`: 复制一行
- `command + F4`: 关闭当前tab
- `command + /`: 注释
- `command + shift + t`: 提示自动生成单元测试
- `alt + enter`: 提示导入类



# 设置快捷键

菜单栏选择:**Intellij IDEA -> Preference -> Keymap**右侧选择功能, 然后自定义快捷键

- `command + option + 2`: 编辑器分屏为竖直方向
- `command + option + shift + 2`: 编辑器分屏为水平方向

# 破解

注册时选择“License server”输入“http://idea.lanyus.com/”点击“OK”，不用打补丁直接激活
备用激活服务器地址：
http://0.idea.lanyus.com
http://1.idea.lanyus.com
http://2.idea.lanyus.com

# 设置maven

# maven创建的java文件夹不识别为source 目录

导航栏选中文件夹, 右键**Mark Directory As -> Sources Root**


# 创建maven管理的web app


1. 打开idea欢迎界面, 选择**create New Project**
2. 左侧选择**Maven**右侧选择sdk版本, 勾选**Create from archetype**
3.  列表中选择`org.apache.maven.archetypes:maven-archetype-webapp`点next
4. 输入**GroupId**, **ArtifactId**, **version**点击next
5. 本页显示之前配置的本机maven信息, 如安装目录, 本地仓库目录, 设置等. 确认无误点next
6. 选择项目保存路径, 输入项目名字, 点击finish创建成功

# 启动maven创建的web app

1. 点击工具栏的**Project Structure**, 如果没有显示工具栏, 勾选菜单**View -> toolbar**
2. 选择左侧的**Facets**, 点击中间的加号, 选择**Web**, 自动添加项目对应的`web.xml`配置文件,修改**Web Resource Directory**到项目`src/main/webapp`目录
3. 点击左侧**Artifacts**, 选择中间**项目名:war exploded**, 右侧**Output directory**为`项目路径+/target/项目名`, 点击确认
4. 配置tomcat默认设置
    1. 点击菜单栏**启动 -> Edit Configurations**, 在左侧打开**Defaults -> Tomcat Server -> Local**,
    2. 点击右侧**Server**tab下的**Application server**右侧的configure, 选择tomcat在本地的安装目录
    3. 在**on frame deactivation**下拉框选择`update classes and resources`
    4. 在**On Update action**下拉框中选择`Update classes and resources`
5. 新建项目启动配置
    1. 点击**左上角加号 -> Tomcat Server -> local**, 在右侧命名
    2. 点击**Deployment** tab, 点击**Deploy at the server startup**下面的**加号 -> Artifact**, 在选择项目对应的**:war exploded**项
6. 添加完成回到项目目录, 会看到运行和debug都变成绿色, 点击debug启动服务器, 浏览器`http://localhost:8080/`显示`Hello World!`

# idea从archetype创建maven项目慢

原因: 默认从远程下载`http://repo1.maven.org/maven2/archetype-catalog.xml`很慢

解决办法: 从本地读取catalog文件

1. 进入idea欢迎页面点击右下角**Configure -> Preference**
2. 左侧选择**Maven -> Runner**右侧**VM Options**填写: `-DarchetypeCatalog=internal`

这样设置以后新建项目从本地读取就快了


100131201205002094
1001342012002572

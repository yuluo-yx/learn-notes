---

---

# Navicat Premium 与MySQL的连接

## Navicat Premium 的安装

[Navicat Premium [官网下载地址]]([https://www.navicat.com.cn/download/navicat-premium](https://defcon.cn/go.php?url=https://www.navicat.com.cn/download/navicat-premium))

## 安装

选择安装目录，建议新手不要安装到C盘，将路径更改为其他位置。

![SetPath](C:/Users/Administrator/Desktop/SetPath.PNG)

**安装完成后不要打开软件。点击完成退出安装程序**

## 注册机

[注册机下载地址](http://pan.defcon.cn/file/1855579-449147144)

<font size = 5, color = red>**注意事项：**</font>

- .运行注册机时最好关闭电脑的杀毒软件；
- **运行注册机请断网**，无需将注册机放到Navicat Premium安装目录下；
- 请选择对各个版本，Products那块；
- **安装完成后不要运行软件，然后打开注册机。**

## 1，解压下载的压缩包

得到Navicat Keygen Patch v5.6.0 DFoX.exe

## 2，运行注册机

![注册机](C:/Users/Administrator/Desktop/%E6%B3%A8%E5%86%8C%E6%9C%BA.PNG)

勾选Backup、Host和Navicat v15，如图所示。

**然后点击Patch按钮**，找到Navicat Premium 15安装路径下的navicat.exe，选中并点击打开,

![破解](C:/Users/Administrator/Desktop/%E7%A0%B4%E8%A7%A3.PNG)

提示已破解。

## 3，生成Serial Keygen（即注册码）

![捕e](C:/Users/Administrator/Desktop/%E6%8D%95e.PNG)

此时，打开Navicat Premium，输入注册码

![获](C:/Users/Administrator/Desktop/%E8%8E%B7.PNG)

## 4，手动激活

点击激活之后，出现

![手动](C:/Users/Administrator/Desktop/%E6%89%8B%E5%8A%A8.PNG)

点击手动激活，将Navicat手动激活窗口的请求码框中内容复制到注册机Request Code框中，点击Activation Code下面的Generate按钮。

![success](C:/Users/Administrator/Desktop/success.PNG)

将激活码复制到Navicat Premium中，破解成功！

![ful](C:/Users/Administrator/Desktop/ful.PNG)

## 进入Navicat Premium查看

帮助 --> 注册 -->

![激活](C:/Users/Administrator/Desktop/%E6%BF%80%E6%B4%BB.PNG)

## 测试: 连接MySQL

以管理员身份运行cmd，输入`net start mysql`,启动本地的MySQL服务.

用Navicat Premium连接:



![测试连接](C:/Users/Administrator/Desktop/%E6%B5%8B%E8%AF%95%E8%BF%9E%E6%8E%A5.PNG)

![连接成功](C:/Users/Administrator/Desktop/%E8%BF%9E%E6%8E%A5%E6%88%90%E5%8A%9F.PNG)

## 失败的解决方法

### 卸载Navicat Premium。

cmd中输入regedt32或者win+R 输入regedit

在里面找到 HKEY_CURRENT_USER\Software\PremiumSoft  这个路径下的 Navicat 的相关注册表删除即可。

### 重新进行上述步骤

**关闭网络连接**

**关闭防护软件**

**安装完成之后不要打开软件**

## 注册表已被管理员禁用的解决方法

在运行框中输入gpedit.msc

组策略编辑器的页面就出现了，点击左侧的“用户配置”，下方有“管理模板”，要点击“系统”这个项目。

“系统”对话框打开之后，我们往下拉，需要找到一个特别的编辑工具，为“组织防卫注册表编辑工具”

选择的项目如果已经被打开，所以造成了无法打开注册表的现象。

设置“阻止访问注册表编辑器工具”，将“未配置”改成“确定”，即可。

![455](C:/Users/Administrator/Desktop/455.PNG)

![0877获](C:/Users/Administrator/Desktop/0877%E8%8E%B7.PNG)

重新打开修改即可！
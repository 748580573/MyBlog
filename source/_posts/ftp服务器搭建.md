---
title: ftp服务器搭建
date: 2018-06-12 09:51:30
tags:
categories: windows
---
#  ftp服务器搭建
#### (注)转自(https://www.cnblogs.com/popfisher/p/7992036.html)

## 第一步：配置IIS Web服务器
### 1.1 控制面板中找到“程序”并打开
![图1-1](/image/build_ftp/1-1.jpg)

### 1.2 程序界面找到“启用或关闭Windows功能”并打开
![图1-2](/image/build_ftp/1-2.jpg)

### 1.3 上面两步也可以简化为一步：按【Win + R】快捷键打开运行对话框，输入“optionalfeatures”后，按回车键
![图1-3](/image/build_ftp/1-3.png)

### 1.4 从“启用或关闭Windows功能”弹窗中找到Internet Information Services(或者中文版Internet信息服务)并打开
![图1-4](/image/build_ftp/1-4.jpg)

### 1.5 配置IIS并点击确定
![图1-5](/image/build_ftp/1-5.png)

## 第二步：配置IIS Web站点
### 2.1 开始菜单搜索“IIS”并点击进入IIS管理器
![图2-1](/image/build_ftp/2-1.png)

### 2.2 新建FTP站点
#### 2.2.1 新建FTP服务器根目录文件夹
![图2-2-1](/image/build_ftp/2-2-1.png)

#### 2.2.2 查看本机ip地址，后续访问Ftp地址需要用到（打开cmd输入ipconfig）
![图2-2-2](/image/build_ftp/2-2-2.png)

#### 2.2.3 IIS网站管理器界面左边导航栏找到“网站”，右键弹出菜单
![图2-2-3](/image/build_ftp/2-2-3.png)

#### 2.2.4 IIS网站管理器“网站”右键弹出菜单点击“添加FTP站点”
![图2-2-4](/image/build_ftp/2-2-4.png)

#### 2.2.5 配置网站（网站名称：FtpSite 物理路径：E:\ftpserver 本机IP地址(从下拉菜单选择)：192.168.0.105）
 <p style="font-size:18px">Ftp站点名称和物理路径设置</p>
![图2-2-5](/image/build_ftp/2-2-5.png)
 <p style="font-size:18px">IP 端口号 SSL设置</p>
![图2-2-5-1](/image/build_ftp/2-2-5-1.png)
 <p style="font-size:18px">身份验证和授权信息设置</p>
 ![图2-2-5-2](/image/build_ftp/2-2-5-2.png)

## 第三步：测试FTP站点（先在物理路径：E:\ftpserver随便放一个文件）
### 3.1 浏览器或者文件管理器地址栏输入ftp地址（ftp://192.168.0.105）
![图3-1](/image/build_ftp/3-1.png)
  <p style="font-size:18px">输入FTP地址时发现需要用户和密码（这个看情况，有些默认就可以直接访问了），可是配置的过程中好像没有看到设置用户和密码的步骤，没关系，我们可以自己设置。<p>
  
### 3.2 IIS管理器中的FTP身份验证里面配置启用匿名身份认证（无密码）
![图3-2](/image/build_ftp/3-2.png)
![图3-2-1](/image/build_ftp/3-2-1.png)

### 3.3 再次测试，浏览器或者文件管理器地址栏输入ftp地址（ftp://192.168.0.105）
![图3-3](/image/build_ftp/3-3.png)

### 3.4 配置FTP站点用户名和密码
#### 3.4.1 IIS管理器中的FTP身份验证里面配置禁用匿名身份认证同时启用基本身份认证（再次访问就会要求输入用户名和密码）
![图3-4-1](/image/build_ftp/3-4-1.png)

#### 3.4.2 此电脑（桌面计算机图标右键）—>管理->本地用户和组->用户->新建一个用户，并设置密码
![图3-4-2](/image/build_ftp/3-4-2.png)
![图3-4-2-1](/image/build_ftp/3-4-2-1.png)
![图3-4-2-2](/image/build_ftp/3-4-2-2.png)
![图3-4-2-3](/image/build_ftp/3-4-2-3.png)
 <p style="font-size:18px"><b>备注：</b>细心的你可以能已经发现这里的账户就是我们计算机的账户，所以说我们应该可以使用自己登录电脑的用户名和密码来登录FTP站点，不用新建这个test用户都可以。

### 3.4.3 再次测试，浏览器或者文件管理器地址栏输入ftp地址，输入用户名:test，密码:test（ftp://192.168.0.105）
![图3-4-2-3](/image/build_ftp/3-4-3.png)
![图3-4-3-1](/image/build_ftp/3-4-3.png)
<p style="font-size:18px">到此一个简单的FTP服务器搭建成功，同一个局域网内其他人可以访问到你的电脑了，可以相互传输文件。传输文件是一个用途，本文主要是想用来当做一个java 上传下载项目的测试服务器。</b>
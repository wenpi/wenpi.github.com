---
comments: true
date: 2006-02-07 11:09:00
layout: post
slug: wordpress%e5%85%a5%e9%97%a8%e6%8c%87%e5%8d%97%ef%bc%8dwordpress%e5%ae%89%e8%a3%85%e4%bd%bf%e7%94%a8
title: WordPress入门指南－wordpress安装使用
wordpress_id: 717532
categories:
- 未分类
---

　　参考了Qiantu's Weblog的WordPress的架设指南及奔行狗日记 的全新安装WORDPRESS1.5-alpha-6 指南（版本0.3)及网络上的相关文章、资源。   
  
　　1、什么是WordPress？   
　　WordPress是一款基于PHP和MySQL的Blog软件，通过它可以快速而简便的搭建属于你自己的Blog平台，目前的最新版本是1.5.1.2版，版本代号是"Strayhorn"。可以看一下我装的[http://www.nieqiang.com/wp/](http://www.nieqiang.com/wp/)   
  
　　2、安装WordPress的准备工作   
　　A、首先需要有一个支持PHP和MYSQL的空间，而且根据WordPress的说明文档（readme.html），要求PHP 的版本在4.1以上， MySQL的版本在3.23.23 以上。至于空间的大小，可以根据个人需要来定，WordPress的安装文件是1M左右。   
　　B、下载WordPress的最新安装包，可以去这里下载：WordPress下载页面（英文版）。可以在网上搜索"WordPress 中文版 "或直接去中文WordPress 下载。   
　　C、解压缩，然后将wp-config-sample.php更名为wp-config.php，并使用你所喜欢的文本编辑器（操作系统自带的写字板、记事本等）来进行设置，修改它的前几句，按照你的数据库配置来定义。   
　　define('DB_NAME', '数据库名'); // The name of the database   
　　define('DB_USER', 'MYSQL用户名'); // Your MySQL username   
　　define('DB_PASSWORD', '密码'); // …and password   
　　define('DB_HOST', 'localhost'); // 99% chance you won't need to change this value   
　　（这些参数可以从你的主机提供商那里取得，如果自己安装需要先自己建立一个空库，名字任意)。里面的table_prefix选项是设置MYSQL 数据库里面的数据表的前缀，define ('WPLANG', '')则是用来设置语言支持，英文版不变，中文版为('WPLANG','zh_CN'）。   
　　D、设置修改完成之后，保存文件，这样前期的准备工具就OK了。   
  
　　3、WordPress的安装进程   
　　A、使用你所喜爱的FTP客户端软件把WordPress的安装文件上传到你的主机。   
　　B、在浏览器输入:你的网址/WordPress安装文件夹/wp-admin/install.php，比如你的WordPress是直接上传到根目录的，那么就只要输入http://www.yourdomainname.com/wp-admin/install.php就可以了。程序会自动检测并安装。   
　　C、安装完成后，系统会给你一个随机生成的管理员密码，记住这个密码，并使用admin和那个密码登录管理界面，在管理界面的Profile选项里修改密码、并修改设置详细的个性信息。如果你能够到这一步，那么恭喜你，你的WordPress就安装完成了。   
　　D、更多的安装帮助请参考WordPress安装包里面的Readme.html，以及访问官方网站：WordPress.org。   
  
　　４、修改界面（主题theme，或者称为wordpress样式)　   
　A、下载主题：可以在WordPress Themes、WordPress Reference Centre或Alexking和Blogging Pro下载到一些漂亮的主题。   
　　B、安装主题：将下载来的主题解压，并保留目录结构,将整个文件夹上传到/wordpress/wp-content/themes/目录下，然后进入管理界面，在Presentation里（如果你安装了中文版，就是"表现"项）选择上传好的主题。   
　　C、修改主题：要修改主题需要先将主题文件夹及里面的文件属性改为"777"，在FTP中右键点击，使用chmod选项修改。然后在Presentation里（如果你安装了中文版，就是"表现"项）里的Theme Editor（主题编辑）项就可以直接修改了。   
  
　　５、安装插件   
　　A、常用的：桑椹做的WordPress 评论插件，可以显示最近评论。其他可到WordPress Plugins和WordPress Plugin Repository 或Wordpress Plugin Database下载。   
　　B、安装插件：将下载来的插件解压，并保留目录结构,将整个文件夹上传到/wordpress/wp-content/plugins/目录下，然后进入管理界面，在Plugins里（如果你安装了中文版，就是"插件"项）激活上传好的插件。   
　　C、在需要显示的地方加入调用的语言，如桑椹做的WordPress 评论插件需要在sidebar.php或index.php里加入一行类示< ?php get_recent_comments(); ?>的文字。

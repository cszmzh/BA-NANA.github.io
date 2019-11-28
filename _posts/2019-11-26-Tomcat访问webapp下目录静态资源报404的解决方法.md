---
layout: post
title: 'Tomcat访问webapp下目录静态资源报404的解决方法'
subtitle: '现象：页面访问服务器资源出现404报错'
date: 2019-11-26
categories: 技术
tags: 技巧与报错总结
comments: true
toc: true
---

* **错误出现原因**：在更换开发环境中，由于更换了Tomcat版本，不知道是不是版本问题，导致网站内修改头像模块图片能上传至服务器，但读取时会返回404错误。

* **解决方法**：搜集网上资料后，用以下方法得以解决

  * 在Tomcat目录/conf/web.xml中修改参数：

    ~~~ 
    <init-param>
    
    <param-name>listings</param-name>
    
    <param-value>false</param-value>
    
    </init-param>
    ~~~

  * 将上述value值改为true，并在idea的Tomcat配置中勾选：

    Deploy applications configured in Tomcat instance


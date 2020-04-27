---
layout:     post
title:      "Jekyll: 添加Valine评论(邮件通知、评论列表头像)"
date:       2020-4-27 23:37:00
author:     "Zhao Wang"
tags:
    - Jeklly
    - Valine
    - 评论功能
---

# 关于 Valine 模块
之所以选择 Valine 模块，一方面因为 Valine 评论模块延续了Jekyll和Hexo模板简洁的风格，两者匹配得可谓天衣无缝，另一方面国内可选的评论模块有些已经不再支持，有些模块的UI不太喜欢。
Valine 诞生于2017年8月7日，是一款基于 Leancloud 的快速、简洁且高效的无后端评论系统，支持但不限于静态博客。具有以下特性：

- 快速
- 安全
- Emoji
- 无后端实现
- MarkDown 全语法支持
- 轻量易用(~15kb gzipped)
- 文章阅读量统计 v1.2.0+
# Valine 部署
Valine 是基于 leancloud国际版的评论模块，评论数据都存储在 Leancloud 平台，因此需要先在 leancloud 申请帐号。（国内版无域名的，将停止服务）。

1. 申请 Leancloud 帐号
申请 Leancloud 步骤比较简单，首先进行 Leancloud 控制台创建应用(个人使用开发版)，之后获取应用的 App ID 和 App Key，后面配置博客的主题配置文件会用到。

2. 配置安全域名
登录leancloud控制台，选择 设置 -> 安全中心 -> Web 安全域名，这里设置成博客的域名地址和本地地址即可，如图:

# 配置Jekyll主题配置文件
修改博客主题配置文件 _config.yml，方便灵活的enable/disable评论功能。配置 Valine 的 enable、appid、appkey 等参数，添加代码如下:

	# Valine评论系统开关
	# Valine.
	# You can get your appid and appkey from https://leancloud.app
	# more info please open https://valine.js.org
	valine:
	 	enable: true
	  	appid:  xxxxxxxx # your leancloud app id
	  	appkey: xxxxxxxx # your leancloud app key
	  	notify: true # mail notifier , https://github.com/xCss/Valine/wiki，v1.4.0+ 已废弃
	  	verify: true # Verification code，v1.4.0+ 已废弃
	  	placeholder: 非Github帐号登录用户，在此处留言 # comment box placeholder
	  	avatar:   # gravatar style
	 	guest_info: 昵称,邮件,网址 # custom comment header
	  	pageSize: 10 # pagination size
	  	# path: window.location.pathname #  v1.4.0+不要使用参数“app_key: 'LHvLGcwcauE5B2JpMxJ3lbtF',”
	  	recordIP: true # 是否记录评论者IP
	  	enableQQ: true # 是否启用昵称框自动获取QQ昵称和QQ头像, 默认关闭
以上的 appid和appkey为本文开始在Leancloud创建应用的 App ID 和 App Key。

在Jekyll站点的_includes目录下创建valine_comments.html文件。文件内容如下([]替换为{})：

	<br>
	<h4 align="left">「游客及非Github用户留言」：</h4>    
	<div id="comments"></div>
	    <!--Leancloud 操作库:-->
	    <script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
	    <!--Valine 的核心代码库:-->
	    <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
	    <script>
		 new Valine([
		    av: AV,
		    el: '#comments',
		    app_id: '[[ site.valine.appid ]]',
		    app_key: '[[ site.valine.appkey ]]',
		    placeholder: '[[ site.valine.placeholder ]]',
		    notify: '[[ site.valine.notify ]]',
		    verify: '[[ site.valine.verify ]]',
		    recordIP: 'true',
		    enableQQ: 'true',
		])
	    </script>
在post.html文件末尾后面（或你认为合适的位置）添加代码引用valine_comments.html来显示评论框([]替换为{})：

	<!-- Valine 评论框 start -->
	[% if site.valine.enable %]
	[% include valine_comments.html %]
	[% endif %]
	<!-- Valine 评论框 end -->

以上就是 Valine 模块的基本部署过程。

# 开启 Valine 评论邮件通知
以上使用 Valine 模块实现了最基本的评论功能，但功能还需要完善，例如评论邮件通知、评论管理等功能，正好 Valine Admin 可以满足需求，Valine Admin 是Valine评论模块的扩展和增强。

这块内容主要参考 valine-admin-document。

近期更改了Github用户名请留意，仓库链接更新为https://github.com/DesertsP/Valine-Admin.git

1. 云引擎”一键”部署
在Leancloud云引擎设置界面，填写代码库并保存：https://github.com/DesertsP/Valine-Admin.git

2. 在设置页面，设置环境变量以及 Web 二级域名。

环境变量

变量	示例	说明
|SITE_NAME	|Deserts	|[必填]博客名称
|SITE_URL	|https://panjunwen.com	|[必填]首页地址
|SMTP_SERVICE	|QQ	|[新版支持]邮件服务提供商，支持 QQ、163、126、Gmail 以及 更多
|SMTP_USER	|xxxxxx@qq.com	|[必填]SMTP登录用户
|SMTP_PASS	|ccxxxxxxxxch	|[必填]SMTP登录密码（QQ邮箱需要获取独立密码）
|SENDER_NAME	|Deserts	|[必填]发件人
|SENDER_EMAIL	|xxxxxx@qq.com	|[必填]发件邮箱
|ADMIN_URL	|https://xxx.leanapp.cn/	|[建议]Web主机二级域名，用于自动唤醒
|BLOGGER_EMAIL	|xxxxx@gmail.com	|[可选]博主通知收件地址，默认使用SENDER_EMAIL
|AKISMET_KEY	|xxxxxxxxxxxx	|[可选]Akismet Key 用于垃圾评论检测，设为MANUAL_REVIEW开启人工审核，留空不使用反垃圾
以上必填参数请务必正确设置。

二级域名用于评论后台管理，如https://deserts.leanapp.us 。

二级域名

3. 切换到部署标签页，分支使用master，点击部署即可

第一次部署需要花点时间。

4. 评论管理。访问设置的二级域名https://二级域名.leanapp.us/sign-up，注册管理员登录信息，如：https://deserts.leanapp.us/sign-up
注：使用原版Valine如果遇到注册页面不显示直接跳转至登录页的情况，请手动删除_User表中的全部数据。

此后，可以通过https://二级域名.leanapp.us/管理评论。

5. 定时任务设置

目前实现了两种云函数定时任务：(1)自动唤醒，定时访问Web APP二级域名防止云引擎休眠；(2)每天定时检查24小时内漏发的邮件通知。

进入云引擎-定时任务中，创建定时器，创建两个定时任务。

选择self-wake云函数，Cron表达式为0 0/30 7-23 * * ?，表示每天早6点到晚23点每隔30分钟访问云引擎，ADMIN_URL环境变量务必设置正确：

选择resend-mails云函数，Cron表达式为0 0 8 * * ?，表示每天早8点检查过去24小时内漏发的通知邮件并补发：

添加定时器后记得点击启动方可生效。

至此，Valine Admin 已经可以正常工作，更多以下是可选的进阶配置。

邮件通知模板在云引擎环境变量中设定，可自定义通知邮件标题及内容模板。

环境变量	示例	说明
|MAIL_SUBJECT	|${PARENT\_NICK}，您在${SITE\_NAME}上的评论收到了回复	|[可选]@通知邮件主题（标题）模板
|MAIL_TEMPLATE	|见下文	|[可选]@通知邮件内容模板
|MAIL_SUBJECT_ADMIN	|${SITE\_NAME}上有新评论了	|[可选]博主邮件通知主题模板
|MAIL_TEMPLATE_ADMIN	|见下文	|[可选]博主邮件通知内容模板
邮件通知包含两种，分别是被@通知和博主通知，这两种模板都可以完全自定义。默认使用经典的蓝色风格模板（样式来源未知）。

默认被@通知邮件内容模板如下：

`<div style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;"><h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;">您在<a style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank">            ${SITE_NAME}</a>上的评论有了新的回复</h2> ${PARENT_NICK} 同学，您曾发表评论：<div style="padding:0 12px 0 12px;margin-top:18px"><div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;">            ${PARENT_COMMENT}</div><p><strong>${NICK}</strong>回复说：</p><div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div><p>您可以点击<a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">查看回复的完整內容</a>，欢迎再次光临<a style="text-decoration:none; color:#12addb" href="${SITE_URL}" target="_blank">${SITE_NAME}</a>。<br></p></div></div>`

@通知模板中的可用变量如下（注，这是邮件模板变量，是指嵌入到HTML邮件模板中的变量，请勿与云引擎环境变量混淆）：

模板变量	说明
|SITE_NAME	|博客名称
|SITE_URL	|博客首页地址
|POST_URL	|文章地址（完整路径）
|PARENT_NICK	|收件人昵称（被@者，父级评论人）
|PARENT_COMMENT	|父级评论内容
|NICK	|新评论者昵称
|COMMENT	|新评论内容
默认博主通知邮件内容模板如下：

`<div style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;"><h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;">您在<a style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank">${SITE_NAME}</a>上的文章有了新的评论</h2><p><strong>${NICK}</strong>回复说：</p><div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div><p>您可以点击<a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">查看回复的完整內容</a><br></p></div></div>`

博主通知邮件模板中的可用变量与@通知中的基本一致，`PARENT_NICK` 和 `PARENT_COMMENT` 变量不再可用。

这里还提供一个彩虹风格的@通知邮件模板代码：

`<div style="border-radius: 10px 10px 10px 10px;font-size:13px;    color: #555555;width: 666px;font-family:'Century Gothic','Trebuchet MS','Hiragino Sans GB',微软雅黑,'Microsoft Yahei',Tahoma,Helvetica,Arial,'SimSun',sans-serif;margin:50px auto;border:1px solid #eee;max-width:100%;background: #ffffff repeating-linear-gradient(-45deg,#fff,#fff 1.125rem,transparent 1.125rem,transparent 2.25rem);box-shadow: 0 1px 5px rgba(0, 0, 0, 0.15);"><div style="width:100%;background:#49BDAD;color:#ffffff;border-radius: 10px 10px 0 0;background-image: -moz-linear-gradient(0deg, rgb(67, 198, 184), rgb(255, 209, 244));background-image: -webkit-linear-gradient(0deg, rgb(67, 198, 184), rgb(255, 209, 244));height: 66px;"><p style="font-size:15px;word-break:break-all;padding: 23px 32px;margin:0;background-color: hsla(0,0%,100%,.4);border-radius: 10px 10px 0 0;">您在<a style="text-decoration:none;color: #ffffff;" href="${SITE_URL}"> ${SITE_NAME}</a>上的留言有新回复啦！</p></div><div style="margin:40px auto;width:90%"><p>${PARENT_NICK} 同学，您曾在文章上发表评论：</p><div style="background: #fafafa repeating-linear-gradient(-45deg,#fff,#fff 1.125rem,transparent 1.125rem,transparent 2.25rem);box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15);margin:20px 0px;padding:15px;border-radius:5px;font-size:14px;color:#555555;">${PARENT_COMMENT}</div><p>${NICK} 给您的回复如下：</p><div style="background: #fafafa repeating-linear-gradient(-45deg,#fff,#fff 1.125rem,transparent 1.125rem,transparent 2.25rem);box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15);margin:20px 0px;padding:15px;border-radius:5px;font-size:14px;color:#555555;">${COMMENT}</div><p>您可以点击<a style="text-decoration:none; color:#12addb" href="${POST_URL}#comments">查看回复的完整內容</a>，欢迎再次光临<a style="text-decoration:none; color:#12addb"                href="${SITE_URL}"> ${SITE_NAME}</a>。</p><style type="text/css">a:link{text-decoration:none}a:visited{text-decoration:none}a:hover{text-decoration:none}a:active{text-decoration:none}</style></div></div>`





BlackHole
=========

####1. 简介

BlackHole是一个Java编写的DNS服务器。BlackHole可以进行DNS缓存，也支持自定义域名配置，并可以防止DNS污染。比起老牌的DNS软件pdnsd、BIND，BlackHole更容易使用，性能也更好。

BlackHole还包含一个Web管理模块[**Hostd**](https://github.com/code4craft/hostd)，可以让每个用户管理自己的域名配置，并且彼此之间不冲突。

####2. 用途

BlackHole具有DNS缓存以及持久化的功能，可以作为一个DNS缓存服务器使用，以加速DNS访问。BlackHole缓存性能优秀，可以支持每秒50000次随机查询，平均响应时间0.3ms，高于pdnsd及BIND([测试报告](https://github.com/code4craft/blackhole/blob/master/server/benchmark-other-dns-server))。

BlackHole也支持修改域名配置，配置域名的方式非常简单，与hosts文件一致，并且支持通配符(目前仅支持A记录)。

例如：

	127.0.0.1	*.codecraft.us
	
表示将所有以.codecraft.us形式结尾的域名全部指向127.0.0.1。

BlackHole还可以通过UDP特征判断的方式防止DNS污染攻击，对于某些无法访问的网站可以起到作用。BlackHole防止DNS的方式参见：[http://code4craft.github.com/blog/2013/02/25/blackhole-anti-dns-poison/](http://code4craft.github.com/blog/2013/02/25/blackhole-anti-dns-poison/)


####3. 安装及配置

BlackHole的编译后版本保存在https://github.com/code4craft/blackhole-bin，直接clone这个项目到某一目录即可。

	git clone https://github.com/code4craft/blackhole-bin.git /usr/local/blackhole

你也可以使用自动脚本进行安装：

	curl http://code4craft.github.io/blackhole/install.sh | sh
	
然后通过sudo /usr/local/blackhole/blackhole.sh start可以启动。

Windows系统可将文件保存到任意目录，并运行start.bat(Win7下无需用管理员权限启动)，若弹出终端界面并且持续运行，则启动成功。

具体的设置请看[Blackhole Server Readme](https://github.com/code4craft/blackhole/blob/master/server/README.md)。

####4. 原理：

BlackHole存在两种工作模式："拦截"和"转发"。

* #####拦截


	当DNS客户端的请求在BlackHole中有对应配置时，则进入拦截模式。拦截模式使用正则表达式匹配域名并拦截。同时支持PTR反解。

* #####转发

	当DNS客户端的请求在BlackHole中没有对应zones配置时，则进入转发模式。转发模式下，BlackHole会将UDP请求转发给另外的DNS服务器，并将该DNS服务器的响应转发回客户端。Blackhole支持多个外部DNS同时转发，并使用最先响应的作为结果，以提高响应速度。
		
#####基准测试

在基准测试中，拦截模式下不开启cache，qps为BIND的50%，为17000，如果开启cache，对于有缓存的数据达到40000qps，优于BIND，已经能满足企业内网需要。

基准测试结果见：
[BlackHole vs BIND benchmark](https://github.com/flashsword20/blackhole/blob/master/benchmark)

####5. 稳定性

目前BlackHole已经比较稳定，有支持企业内部几百人使用的案例。

####6. 协议

BlackHole的连接部分参考了EagleDNS的代码，遵守LGPLv3协议。

作者邮箱：
code4crafter@gmail.com
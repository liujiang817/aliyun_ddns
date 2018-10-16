## aliyun_ddns --基于阿里云解析的动态域名解析

精简下内容：
替代花生壳，当公网ip变动时，自动将IP地址解析到一个二级域名上。

### 为何要写这个
为了达到使用域名即可访问家里服务的目的，需要两个条件，外网ip和域名。

域名这个简单，在阿里云购买一个即可，麻烦的是ip，由于目前家庭使用的宽带基本上都是服务商动态分配的ip(有的甚至只分配内网ip，这个需要打电话给服务商要回外网ip！)这样的话，域名解析就没法玩了，此时需要动态域名解析，能够在家里ip变化的时候更新解析记录。

当然可以使用花生壳，但是开头已经提到了两个蛋疼的原因，所以，这个工具脚本需求就来了！

### 实现方法
定时检测自家外网ip地址，有变化就调用阿里云解析的api修改域名解析。

### 前提条件
1. 确保自己拥有外网ip
2. 使用阿里云解析，申请access_key
3. 首先在阿里云解析中新增域名的解析记录 (重要！！！因为本脚本只是修改，所以前提得有可修改的解析记录！！！)

### 脚本依赖
1. python环境 (安装python2.7, 不支持python3)
2. pip install requests

### 使用说明
1. 修改aliyun_settings.json中的access_key、access_secret为自己申请的accesskey
2. 修改aliyun_settings.json中的domain为自己要解析的域名，比如 "baidu.com''
3. 定时执行 python aliyun_ddns.py   (windows和linux环境下的定时任务就不提了)
4. 最新的ip会保存在同级目录的ip.txt中，方便查看
5. 脚本的执行步骤有print日志，如有问题，先分析下输出内容

### 注意
修改完解析记录后并不会马上生效，因为dns服务都有缓存，所以得等，阿里云的解析ttl可以设置的最小值为10分钟，所以有时候得等一会才能生效。
如果迟迟不生效，首先登录阿里云控制台查看解析记录是否成功修改，如果已经修改，那么就是dns服务的缓存问题了，这个基本就是死等。。。



### Centos7实际部署：

1 安装pip

yum -y install epel-release

yum install python-pip

pip install --upgrade pip

2 安装python库

pip install Utils

pip install IpGetter

pip install bs4

pip install requests

3 安装ali SDK

pip install aliyun-python-sdk-core

pip install aliyun-python-sdk-alidns

4 修改aliyun_settings.json

"domain"要写一级域名，如 baidu.com

{
​	"access_key": "access_key",
​	"access_secret": "access_secret",
​	"domain": "domain"
}

5 修改 aliyun_ddns.py

下面这段，要修改'RRKeyWord': 'temp'，temp为二级域名记录，即 temp.baidu.com

	#首先获取解析列表
	get_params = get_signed_params('GET', {
		'Action': 'DescribeDomainRecords',
		'DomainName': settings['domain'],
		'TypeKeyWord': 'A',
		'RRKeyWord': 'temp'
	}, settings)

6 如何获取AccessKeyId和AccessKeySecret

操作步骤：

登录阿里云控制台。

将鼠标放在右上方的用户名区域，在弹出的快捷菜单中选择accesskeys。

系统弹出安全提示对话框，单击继续使用AccessKey。

页面显示Access Key ID和Access Key Secret。

不要子AccessKey

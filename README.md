# CentOS-Hue
记录CentOS安装Hue3.9的过程和遇到问题

# CentOS 6.5 安装hue 3.9.0
## 1. 环境准备
- python 2.7
参考[Centos 6.4 python 2.6 升级到 2.7](http://blog.csdn.net/jcjc918/article/details/11022345)
- jdk 1.7
- OS Centos 6.5 64 bit
- mvn 3.4.1

## 2.安装配置
- 安装依赖
```shell
	yum install krb5-devel cyrus-sasl-gssapi cyrus-sasl-deve libxml2-devel libxslt-devel mysql mysql-devel openldap-devel python-devel python-simplejson sqlite-devel gcc g++
```
- 下载hue构建,我使用的是[hue 3.9](http://gethue.com/hue-3-9-with-all-its-improvements-is-out/),也可以在GitHub下载
- 参照官网`make apps`即可,如果遇到错误，确认依赖安装是否有问题
- 配置文件desktop/conf/hue.ini,配置 `server_user=hadoop,server_group=hadoop`,hadoop换成你的用户名，确保其对hue文件夹有读写权限
- 启动`build/env/bin/supervisor &`,所在主机ip:8888，就可以访问登陆界面

## 3.配置
- 现在还不能监控各种集群，各种配置参考http://cloudera.github.io/hue/docs-3.9.0/manual.html
- 配置HBase监控: http://www.cnblogs.com/liuchangchun/p/4769730.html, http://share.zhbor.com/article/13882.html

## 4.插件
- 配置Storm: https://github.com/keedio/storm-hue
- 配置Kafka：https://github.com/chenyuanjin/kafka-hue


## 错误
1. 如果遇到错误，可以先到这个[页面](http://gethue.com/hue-3-9-with-all-its-improvements-is-out/)的问答看有没有人遇到过
2. 启动hue web端 报错误：OperationalError: attempt to write a readonly database
解决：
启动hue server的用户没有权限去写入默认sqlite DB，同时确保安装目录下所有文件的owner都是hadoop用户
```shell
 chown -R hadoop:hadoop hue-3.9.0 #这里hadoop换成你的用户名
```
3. 忘记管理员用户名密码
```shell
build/env/bin/hue  shell
>>> from django.contrib.auth.models import User
>>> a = User.objects.get(username='admin')
>>> a.is_staff = True
>>> a.is_superuser = True
>>> a.set_password('admin')
>>> a.save()
>>> quit()
```


## 参考
1. http://cloudera.github.io/hue/docs-3.9.0/manual.html#_install_hue
2. http://my.oschina.net/aibati2008/blog/647493
3. http://gethue.com/hue-3-9-with-all-its-improvements-is-out/
4. https://yq.aliyun.com/articles/27876
5. http://blog.csdn.net/wzq294328238/article/details/47810907
6. http://www.cloudera.com/content/www/zh-CN/documentation/enterprise/5-3-x/topics/cdh_ig_hue_config.html#topic_15_6_2_unique_1





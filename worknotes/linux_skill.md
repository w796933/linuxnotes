##linux_skill##

###前言###
记录平时见到的一些技巧，以备不时之需

***
####网络类

>查看机器公网IP：

	curl ifconfig.me

>查看网站使用服务器：

	curl -I  website

####编辑类
>替换输出：

	echo $a | tr . ' '

>awk
	
	#使用环境变量
	a=q.w.e
	b=2
	echo $a | awk -F . '{print $2}'
	echo $a | awk -F . "{print $"${b}" }"  
	echo $a | awk  -v r=$b -F. '{print $r}'

	#从file文件中找出长度大于80的行
	awk 'length>80' file
	 
	#按连接数查看客户端IP
	netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr
	 
	#打印99乘法表
	seq 9 | sed 'H;g' | awk -v RS='' '{for(i=1;i<=NF;i++)printf("%dx%d=%d%s", i, NR, i*NR, i==NR?"\n":"\t")}'

	

####加密类

>随机密码

	pwgen 10 1  （10位长度，一个）
	makepasswd --char 50 --count 7

>加密
	
	mkpasswd mytest -s tt  （指定加密使用文）

>对称加密

	echo Tecmint-is-a-Linux-Community | openssl enc -aes-256-cbc -a -salt -pass pass:tecmint
	>U2FsdGVkX18Zgoc+dfAdpIK58JbcEYFdJBPMINU91DKPeVVrU2k9oXWsgpvpdO/Z

>解密

	echo U2FsdGVkX18Zgoc+dfAdpIK58JbcEYFdJBPMINU91DKPeVVrU2k9oXWsgpvpdO/Z | openssl enc -aes-256-cbc -a -d -salt -pass pass:tecmint

####系统类

>清除Cache

	# sync; echo 1 > /proc/sys/vm/drop_caches    ---仅清除页面缓存（PageCache）
	# sync; echo 2 > /proc/sys/vm/drop_caches    ---清除目录项和inode
	# sync; echo 3 > /proc/sys/vm/drop_caches    ---清除页面缓存，目录项和inode

	sync 将刷新文件系统缓冲区（buffer），命令通过“;”分隔，顺序执行，shell在执行序列中的下一个命令之前会等待命令的终止。
	正如内核文档中提到的，写入到drop_cache将清空缓存而不会杀死任何应用程序/服务，echo命令做写入文件的工作。
	如果你必须清除磁盘高速缓存，第一个命令在企业和生产环境中是最安全，"...echo 1> ..."只会清除页面缓存。 
	在生产环境中不建议使用上面的第三个选项"...echo 3 > ..." ，除非你明确自己在做什么，因为它会清除缓存页，目录项和inodes。

>清除交换分区

	# swapoff -a && swapon -a

>综合使用

	#!/bin/sh 
	echo 3 > /proc/sys/vm/drop_caches && swapoff -a && swapon -a && printf '\n%s\n' 'Ram-cache and Swap Cleared'

	chmod +x clear_cache_swap.sh
	
	crontab -e
	0 3 * * * /path/to/clear_cache_swap.sh

	warning:当所有的用户都从磁盘读取数据时，这将导致服务器崩溃并损坏数据库。

####ssh

>远程重启tomcat时，简单采用ssh，会报JAVA_HOME找不到，可采用

	ssh user@host "source /etc/profile;/path/to/tomcat/bin/shutdown.sh"
	

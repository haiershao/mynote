# git

1.git阿里镜像地址：http://npm.taobao.org/mirrors/git-for-windows

2.

# demo

1.写个定时任务（shell,crontab）

（1）监控/flume/20??-??-??/目录下文件大小

（2）判断文件或文件夹的大小

（3）文件或文件夹大小超过100M报警，发邮件 

示例代码：

```
1.crontab -e
#*/1 * * * * sh /root/test/crontab_job.sh
表示一分钟执行一次
2.
#!/bin/bash

JOB_NAME="TEST"
FROM_EMAIL="haiersho666@163.com"
TO_EMAIL="1434591364@qq.com"

filelist=`hadoop fs -ls -d /flume/20??-??-??`
for file in $filelist
do

if [[ $file == /flume/20* ]]
then
#/bin/echo "=========================" >> /root/test/crontab_job.txt
#/bin/echo $file >> /root/test/crontab_job.txt
#hadoop fs -du -s -h $file
filesize=`hadoop fs -du -s -h $file`
#echo $filesize
#/bin/echo $filesize >> /root/test/crontab_job.txt
#echo $filesize
arr=($filesize)
tmp0=${arr[0]}
tmp=${arr[1]}
    if [[ $tmp == "M" ]]
    then
        if [[ $tmp0 > 5.0 ]]
        then
        echo $filesize
        echo -e "`date "+%Y-%m-%d %H:%M:%S"` : The file($filesize) size exceeds 5M ......" | mail \
        -r "haiershao: alertAdmin <${FROM_EMAIL}>" \
        -s "Warn: Skip the new $JOB_NAME spark job." ${TO_EMAIL}

        fi
    fi
fi
done

```

2.mysql数据到hive

需求：hive表格式orc,hive表压缩方式LZO。分区采用静态分区和动态分区

实现方式1：通过sqoop导入到hive

实现方式2：通过spark程序
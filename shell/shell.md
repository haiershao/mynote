# mynote

# 一、脚本

```xml
1.
#! /bin/bash

for i in hadoop102 hadoop103 hadoop104
do
        echo --------- $i ----------
        ssh $i "source /etc/profile;$*"
done
```

```xml
2.分发脚本

#!/bin/bash
#1 获取输入参数个数，如果没有参数，直接退出
pcount=$#
if ((pcount==0)); then
echo no args;
exit;
fi

#2 获取文件名称
p1=$1
fname=`basename $p1`
echo fname=$fname

#3 获取上级目录到绝对路径
pdir=`cd -P $(dirname $p1); pwd`
echo pdir=$pdir

#4 获取当前用户名称
user=`whoami`

#5 循环
for((host=2; host<5; host++)); do
        echo ------------------- CN-KUN-MGO-P0$host --------------
        rsync -av $pdir/$fname $user@CN-KUN-MGO-P0$host:$pdir
done
```


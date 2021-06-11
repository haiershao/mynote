# 1、YARN集群提交任务一直处于ACCEPT状态

```xml
我提交了四个任务，有一个任务处于RUNNING状态，另外三个处于ACCEPTED状态。但集群的总内存资源为1.05T，VCore为180个，已使用的内存为606.63 GB，已使用的VCore为51个。也就是说，当前集群还有大量的空闲资源，另外三个任务为什么会一直处于ACCEPT状态呢？

正常处于ACCEPT状态可能有两个原因。第一个原因可能是当前正在运行的任务数量达到了集群允许同时运行的任务数量的上限。可目前集群只有一个任务在运行，肯定没有达到此上限。第二个原因可能是集群资源不足，任务在等待其他任务释放资源。可当前任务还有大量的空闲资源，资源充足的。



前几天球友问了我一个问题：

请问浪总，集群400GB内存，提交了10个任务后就不能继续提交任务了，
资源还剩余300GB，CPU也很充足，完全满足新任务的资源，为啥就不能提交新任务了呢？？？
各位同仁也可以先思考一下可能的原因及解决方案。

估计很多人会说：

很明显，新任务申请的资源，大于了可提供的资源了～

但是这位球友说的很清楚了，剩余的资源很充足，完全可以提供新任务所需的资源。

知识点小贴士～

对spark on yarn研究比较多的朋友都应该发现过你明明给executor申请了1GB内存，结果发现该executor占用了yarn的2GB内存。

其实，对于spark的driver和executor在申请内存的时候有个计算公式：

spark.yarn.am.memoryOverhead 
除了指定的申请资源外额外申请（yarn-client模式）：
AM memory * 0.10, with minimum of 384

spark.driver.memoryOverhead 
除了指定的申请资源外，额外申请：
driverMemory * 0.10, with minimum of 384

spark.executor.memoryOverhead 
除了指定的申请资源外，额外申请：
executorMemory * 0.10, with minimum of 384

由于1GB*0.10才100MB，所以会是1GB+384MB<2GB，不符合预期。实际上这个还依赖于yarn的内存调度粒度。resourcemanager的参数：
最小值
<property>
<name>yarn.scheduler.minimum-allocation-mb</name>
<value>1024</value>
</property>
最大值
<property>
<name>yarn.scheduler.maximum-allocation-mb</name>
<value>20480</value>
</property>

默认yarn的调度最小单元就是1GB，所以结果就是使你原本申请1GB(+额外内存)的内存变为了2GB。

读到这里估计很多同学该说了，这个我了解但是貌似跟yarn最大并行度没什么关系呀？别急！

重磅来袭～

其实，yarn为了很方便控制在运行的任务数，也即是处于running状态任务的数目，提供了一个重要的参数配置，但是很容易被忽略。

<property>
<name>yarn.scheduler.capacity.maximum-am-resource-percent</name>
<value>0.1</value>
<description> Maximum percent of resources in the cluster which can be used to run application masters i.e. controls number of concurrent running applications. </description>
</property>

配置文件是：hadoop-2.7.4/etc/hadoop/capacity-scheduler.xml

参数含义很明显就是所有AM占用的总内存数要小于yarn所管理总内存的一定比例，默认是0.1。

也即是yarn所能同时运行的任务数受限于该参数和单个AM的内存。

那么回归本话题，可以看看该同学所能申请的AM总内存的大小是：

400GB*0.1=40GB。

https://blog.csdn.net/hongqiutiandi163/article/details/107635294
https://blog.csdn.net/rlnLo2pNEfx9c/article/details/89324824

```


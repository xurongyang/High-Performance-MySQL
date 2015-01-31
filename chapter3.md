#MySQL性能剖析
###MySQL性能优化的思路
性能优化的目的：在一定负载下降低响应时间

如何做性能优化？将大量精力用来测量响应时间花费在什么地方

时间耗费分为两部分：1.执行时间，2.等待时间

###如何做性能优化
####获得查询分布
找出查询的分布状况，可以用pt-query-digest来做，结果如下所示

	Rank Response time      Calls  R/Call  Item
	==== =============      =====  ======  =====
	   1  11256.3618 68.1%  78069  0.1442 SELECT InvisitesNew
	   2   2029.4730 12.3%  14415  0.1408 SELECT StatusUpdate
	   3   1345.3445 8.1%   3520   0.3822 SHOW STATUS
	   
从上面就可以看到响应时间，响应时间的排名，所占时间百分比，次数，每次查询所用时间与总时间的比值，已经该查询的digest，由此可以获得大致的分布状况。

####该优化哪些查询
占总响应时间比例比较小的查询不值得投入大量精力优化，投入产出比太高。

###剖析MySQL查询

####捕获MySQL查询
有三种方式：1.MySQL慢查询日志，对性能影响非常小，可以采用，2.可以用pt-query-digest --processlist通过记录processlist的方式，3.pt-query-processlist --type=tcpdump通过分析网络协议捕获查询。

####分析MySQL慢查询
可以用pt-query-digest来做分析，具体不多说

对于MySQL 5.1版本之后的MySQL，可以通过show profile来记录每一条查询具体时间花费在什么地方，比如opening table，Table lock等。

show status命令可以看到MySQL内部处理该查询都做了些什么，诸如：create_tmp_disk_tables，handler commit等

###诊断间歇性问题
单条查询的问题还是服务器问题？如果整体都慢，可能是服务器问题，如果只有某几条慢，那可能是查询的问题。

用show global status查询某些变量的值，比如thread_running，thread_connected等。

用show processlist查看这些查询究竟在做什么，比如sending data，cleaning up等。

分析查询日志。

通过设置触发器捕获诊断数据，可以利用pt-talk工具

###其它一些工具

USER_STATISTICS表

使用strace


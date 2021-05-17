## 一．	BUG环境介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;SpringBoot，IDEA，在进行简单的WebDemo实例测试时，出现的异常：This application has no explicit mapping for /error, so you are seeing this as a fallback. 
<br>


## 二．	BUG解决
&nbsp;  &nbsp;  &nbsp;  &nbsp;Application启动类我给放在了一个单独的包中，应该将启动类放到一个包含所有包的外侧。举例子来说，如果我们在src下面创建了一个consumer包用来装所有的消费者的代码，包括controller，pojo，service，mapper，那么这个启动类就应该放到consumer包下面即可。




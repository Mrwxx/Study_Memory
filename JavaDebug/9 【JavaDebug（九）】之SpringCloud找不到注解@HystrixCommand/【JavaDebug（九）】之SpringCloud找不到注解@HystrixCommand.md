## 一．	Bug介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在使用SpringCloud的熔断器Hystrix时，添加注解@HystrixCommand时，无法找到该注解。
<br>


## 二．	问题解决
&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于，注解@HystrixCommand是属于com.netflix.hystrix.contrib.javanica.annotation包的，因此需要添加以下的依赖：

```cpp
<dependency>
    <groupId>com.netflix.hystrix</groupId>
    <artifactId>hystrix-javanica</artifactId>
</dependency>
```

至此，问题解决！！！





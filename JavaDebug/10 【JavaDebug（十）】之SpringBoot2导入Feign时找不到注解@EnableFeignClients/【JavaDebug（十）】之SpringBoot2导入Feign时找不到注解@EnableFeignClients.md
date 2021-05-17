## 一．	BUG描述
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用SpringBoot 2.1.4 开发 Spring Cloud项目，导入Feign依赖后，发现找不到注解@EnableFeignClients。
<br>

## 二．	问题解决
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于Spring Cloud中Feign的包从org.springframework.cloud:spring-cloud-netflix-core转移到了org.springframework.cloud:spring-clloud-openfeign-core之中，可能我们所导入的feign依赖是之前的版本，即spring-cloud-netflix-core，我们需要手动设置feign的版本到2.1.1.RELEASE。

```cpp
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>2.1.1.RELEASE</version>
</dependency>
```




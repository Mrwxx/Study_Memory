## 一．	BUG描述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在使用SpringBoot学习SpringAOP的引入时，在切面导入了注解@DeclareParents，出现了报错信息，如下所示：

```cpp
warning can‘t determine implemented interfaces of missing type org.springframework.boot.autoconfigure.jdbc.metadata.DataSourcePoolMetadataProvidersConfiguration
```
<br>


## 二．	BUG解决
&nbsp;  &nbsp;  &nbsp;  &nbsp;注解@DeclareParents中的属性value的值为需要增强的类的全限定名，我填写的内容如下所示：

```cpp
@DeclareParents(value = "com.springboot.chapter4.aspect.service.impl.UserServiceImpl+",
                defaultImpl = UserValidatorImpl.class )
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，全限定名后面有一个+号，可能是不同版本的Spring要求不同，当我将+号去掉时，就不会报错了。






---
title: Spring中@Value()和@ConfigProperties()的区别
category: 技术
---
##  @Value()注解

1. 针对某一个属性值的,即你需要在属性中指定这个变量对应的具体哪个配置
```
@Value(${spring.redis.port})
private String port;
```
则会将port值绑定为配置文件中的spring.redis.port
2. 如果需要动态配置,则还需要加上@RefreshScope()注解
3. 支持SPEL表达式,即还可以对配置文件中的值进行计算再绑定
4. 不支持数据校验
5. 不支持复杂类型(List,Map)的绑定

## @ConfigProperties()注解
1. 支持同时绑定多个使用,在prefix属性中指定前缀
```
@ConfigurationProperties(prefix = "doutao.thread")
// @Component
@Data
public class ThreadPoolConfigProperties {

    private Integer coreSize;

    private Integer maxSize;

    private Integer keepAliveTime;

}
```
则会自动去绑定配置文件中的
- doutao.thread.coreSize
- doutao.thread.maxSize
- doutao.thread.keepAliveTime

2. 原生地支持动态配置
3. 不支持SPEL表达式
4. 支持数据校验
```
@ConfigurationProperties(prefix = "doutao.thread")
// @Component
@Data
public class ThreadPoolConfigProperties {
    @Max(value = 50, message="最大值不能超过50")
    private Integer coreSize;

    private Integer maxSize;

    private Integer keepAliveTime;

}
```
5. 支持复杂数据类型(List,Map)

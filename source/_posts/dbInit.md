---
title: 数据库的初始化(脚本)
category: 技术
---

对于一个项目来说,其刚刚运行时,或多或少会有一些初始的数据,一方面是功能所需,例如: 初始化admin账号;另一方面是使其在dev时或test时,不需要自己慢慢添加就有一些内容供显示、测试,也让人看的更加舒心.  
那么如何来初始化这些数据?  

## Schema.sql & data.sql
你可能曾在项目文件夹中看到过**Schema.sql**和**data.sql**两个文件, 这两个文件就是数据初始化的方式之一。
>针对于spring项目  

springboot默认地会去看有没有这两个脚本,在启动时,如果有这两个初始化脚本,则会执行,其中schema.sql关注表结构初始化,data.sql关注表数据初始化.

### 初始化脚本的初始化模式
可以通过配置
``` spring.datasource.initialization-mode=```  
三种配置:
- ALWAYS: 总是执行
- EMBEDED: 使用内嵌数据库时会执行(默认)
- NEVER: 从不执行

### spring项目更改默认的初始化脚本
初始化脚本的名字也可以不叫schema.sql/data.sql,即你可以自定义默认执行的脚本,甚至可以是很多个  
- 指定某个初始化脚本
```
spring.datasource.schema=classpath:schema-test.sql
```
- 指定多个初始化脚本;
```
spring.datasource.schema[0]=classpath:schema1.sql
spring.datasource.schema[1]=classpath:schema2.sql
....
```

### 初始化脚本可能的问题:
针对开发环境,当你的项目多次迭代,部署时,数据初始化脚本可能会多次运行带来一些不必要的麻烦.  
所以一般在初始化脚本中会有这样的语句:  
```
DROP TABLE IF EXISTS 'table_name'
CREATE TABLE 'table_name' (
        .......
)
```

## 程序中实现数据初始化
spring提供了一些接口帮助实现项目启动时的一些初始化逻辑,则我们也可以采用这种方式.  
实现CommandLineRunner接口,重写run()方法,并在其中执行相关的insert()逻辑,以达到数据初始化的目的


当然啦,你也可以不使用spring的这些机制,手动的去执行某个sql脚本来初始化你的数据,即非自动化

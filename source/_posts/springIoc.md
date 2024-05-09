---
title: Spring Ioc
category: Spring
---
- bean的生命周期
bean的生命周期是指IOC容器启动后,bean**实例化**,**属性赋值**,**初始化**再到**销毁**的一个过程.

- 三级缓存

|||
| --- | --- |
| singletonObjects(一级缓存) | 用于存放完全初始化好的Bean,从该缓存中取出的bean可以直接使用 |
| earlySingletonObjects(二级缓存) | 存放原始的Bean对象(还没有属性赋值),用于解决循环依赖 |
| singletonFactories(三级缓存) | 存放bean工厂对象,用于解决循环依赖 |

- 三级缓存如何解决的循环依赖
三级缓存通过提前暴露出Bean的引用来解决循环依赖问题.以A,B相互依赖为例:
首先实例化A(实例化完成后就可以将A放入缓存中),然后为A的属性赋值,这时候就需要去创建B,B实例化,然后B属性赋值(这依赖于A),这时会到缓存中拿A,A已经放入缓存,这时候就可以拿到A的对象(虽然还没有准备好,但是因为是引用所以可以延后设置属性也没事),然后B初始化,B创建完成.因此A也可以成功属性赋值,并且这是后已经是个完全的B了,然后为A初始化.创建完成

- Spring如何解决循环依赖
```
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {
	...
	// 从上至下 分表代表这“三级缓存”
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256); //一级缓存
	private final Map<String, Object> earlySingletonObjects = new HashMap<>(16); // 二级缓存
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16); // 三级缓存
	...
	
	/** Names of beans that are currently in creation. */
	// 这个缓存也十分重要：它表示bean创建过程中都会在里面呆着~
	// 它在Bean开始创建时放值，创建完成时会将其移出~
	private final Set<String> singletonsCurrentlyInCreation = Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Names of beans that have already been created at least once. */
	// 当这个Bean被创建完成后，会标记为这个 注意：这里是set集合 不会重复
	// 至少被创建了一次的  都会放进这里~~~~
	private final Set<String> alreadyCreated = Collections.newSetFromMap(new ConcurrentHashMap<>(256));
}
```
三级缓存,思路是提前暴露引用.以A.B循环依赖为例,先获取A的Bean时,发现还没有,则去创建:先实例化,然后将实例化的结果expose,引用放入三级缓存中,然后初始化时创建B,先实例化B,然后初始化A,在三级缓存中发现了A的工厂,移到二级缓存中,因为有A的引用所以初始化完成,回溯,A初始化也完成.  

- FactoryBean和BeanFactory的区别


- 为Bean初始化扩展逻辑
继承BeanPostProcessor并实现其中方法,可以为所有的Bean扩展初始化前后的逻辑(可以再逻辑中判断是否是想要操作的Bean)
如果是为单个Bean扩展逻辑,可以
1. 实现InitalizingBean接口(afterPropertySet()方法,属性注入后的逻辑),DisposableBean接口(destrop()方法,销毁前逻辑)
2. JSR250提供了@PostConstruc注解,@PreDestroy注解

- ApplicationContext和BeanFactory的区别
1. BeanFactory是spring ioc的root接口,ApplicationContext继承了BeanFactory接口,包含了BeanFactory的所有功能,另外还包括其他的功能,是企业应用的首选
2. BeanFactory是懒加载,如果bean有属性没有注入,是在第一次使用Bean时去加载的并抛出的异常;而ApplicationContext是预加载,如果有属性没有注入,应用启动时就加载完毕,抛出异常,同时也会消耗更多的时间,但是程序中使用性能更快

- 涉及到的设计模式

- spring启动时执行一些命令的方法
1. 实现CommandLineRunner接口,可以使用@Order注解去控制执行顺序
2. 实现ApplicationRunner接口
3. 实现InitilizingBean接口,并重写afterPropertySet()方法
4. 实现ApplicationListener接口,并监听ContextRefreshedEvent事件
5. @Component注解搭配@PostConstruct注解

- Spring Ioc如何保证Bean的单例:
在Spring的`getSingleton(String beanName,SingletonFactory factory)`方法中使用synchorinized关键词锁定了单例对象的高速缓存Map,以防止生成了多个对象
```
 public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
     // 使用单例对象的高速缓存Map作为锁，保证线程同步
     synchronized (this.singletonObjects) {
        // 从单例对象的高速缓存Map中获取beanName对应的单例对象
        Object singletonObject = this.singletonObjects.get(beanName);
        // 如果单例对象获取不到
        if (singletonObject == null) {
            ...
        }
        ...
     }
}
```

---
title: Spring Ioc
category: Spring
---
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

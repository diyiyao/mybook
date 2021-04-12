#@Resource和@Autowired的区别

@Resource和@Autowired两个注解大家应该很熟悉，在spring中我们通常使用@Resource和@Autowired做bean的注入时使用。有的时候它们是等价的，使用哪个都可以。但有的时候则不可以。接下来我们看看他们的区别吧。

> 相同点：
> + spring中都可以用来注入bean，同时都还可以作为注入属性的修饰。在接口仅有单一实现类时，两个注解的修饰效果是相同的，他们之间可以相互替换，不影响使用。

> 不同点：
> + @Resource是Java自己的注解，@Resource有两个主要的属性，分别是name和type；spring对@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略。而使用type属性，则是使用byType的自动注入策略。如果既不指定name属性也不指定type属性，这时将通过反射机制使用byName的自动注入策略。
> + @Autowired是spring的注解，是spring2.5版本引入的，@Autowired注解只根据type进行注入，不会去匹配name。如果涉及到根据type无法辨别的注入对象，将需要依赖@Qualifier或@Primary注解一起来修饰。
> + @Resource在Spring填充时机是交由org.springframework.context.annotation.CommonAnnotationBeanPostProcessor#postProcessPropertyValues方法
> + @Autowired在Spring填充时机是交由org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor#postProcessPropertyValues方法

上面相同点里面说到，如果接口仅有单一实现类的时候，二者的修饰效果是相同的。那如果接口有两个或者两个以上的实现类呢？？？接下来我们就来测试一下


````java
//首先写一个接口
public interface Human {
    
    /**
     * 跑马拉松
     * @return
     */
    String runMarathon();
}

//接口的第一个实现类
@Service
public class Man implements Human {
 
    public String runMarathon() {
        return "A man run marathon";
    }
}
//接口的调用
@RestController
@RequestMapping("/an")
public class HumanController {
 
    @Resource
    private Human human;
    
    @RequestMapping("/run")
    public String runMarathon() {
        return human.runMarathon();
    }
}

````

上面的代码使用的是@Resource注解，代码可以正常运行。如果换成@Autowired注解，效果也是一样的，可以正常运行

接下来，我们在上面代码的基础上在给接口添加一个实现类

````java
//接口的第二个实现类
@Service
public class Woman implements Human {
 
    public String runMarathon() {
        return "An woman run marathon";
    }
}
````

1、控制类里面如果使用@Resource注解，启动的时候会报错，核心的错误信息如下：
````
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'humanController': Injection of resource dependencies failed; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'com.example.demo1.Human' available: expected single matching bean but found 2: man,woman
````

这是我们就要借助@Resource注解的name属性或者@Qualifier来确定一个合格的实现类
只需要将控制类里面的注解改为如下即可：

````
    @Resource(name="woman")
    private Human human;
    //或者
    @Resource
    @Qualifier("woman")
    private Human human;
````

2、控制类里面如果使用@Autowired注解，启动的时候会报错，错误信息如下：

````
Description:

Field human in com.example.demo1.HumanController required a single bean, but 2 were found:
	- man: defined in file [/Users/yangdeke/IdeaProjects/test/demo1/target/classes/com/example/demo1/Man.class]
	- woman: defined in file [/Users/yangdeke/IdeaProjects/test/demo1/target/classes/com/example/demo1/Woman.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
````


报错信息很明显，HumanController需要一个bean实现，但是找到了两个 man 和woman

解决方案：使用@Primary注解，在有多个实现bean时告诉spring首先@Primary修饰的那个；或者使用@Qualifier来标注需要注入的类。

@Qualifier修改方式与改动二的相同，依然是修改HumanController.java 中间注入的Human上面，这里不再复述

@Primary是修饰实现类的，告诉spring，如果有多个实现类时，优先注入被@Primary注解修饰的那个。这里，我们希望注入Man.java ，那么修改Man.java为如下情况

```java

@Service
@Primary
public class Man implements Human {
 
    public String runMarathon() {
        return "A man run marathon";
    }
}
```

通过上面的案例我们就可以很明显的看出来他们之间的区别了。同时遇到问题也知道该如何解决了。

推荐使用：@Resource注解在字段上，这样就不用写setter方法了，并且这个注解是属于J2EE的，减少了与spring的耦合。这样代码看起就比较优雅

最后呢，说一下个人的见解，平时的开发过程中呢，更常用的是@Autowired注解，因为平时百分之九十以上都是一个接口仅有一个实现类，所以用@Autowired比较方便，当然这种情况用两个中的哪一个都一样，没什么太大的区别，纯属个人习惯。但是如果是生产环境的话，架构师对这方面要求有比较严格的情况下，会让大家使用@Resource(name="xxx")这种，因为在生产环境就会要求更高的效率问题，这样使用效率是最高的，类似于根据id查询数据库一样，效率高一些。所以根据个人情况，选择性使用吧。。。

链接：https://www.jianshu.com/p/e4a899bfd18b

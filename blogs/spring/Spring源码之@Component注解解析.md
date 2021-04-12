#Spring源码之@Component注解解析

##使用
```
@Component的作用就是将类注入到spring容器中
```

````java

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Indexed
public @interface Component {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	String value() default "";

}
````

##源码分析
在 `ConfigurationClassParser#doProcessConfigurationClass` 方法中

```
    //判断类上面是否有Component注解
    if (configClass.getMetadata().isAnnotated(Component.class.getName())) {
        // Recursively process any member (nested) classes first
        //递归处理有@Component注解的内部类
        processMemberClasses(configClass, sourceClass, filter);
    }
```

configClass.getMetadata()可以拿到类的元数据信息
如果类上面有@Component注解，则进行解析操作processMemberClasses
````java
public class ConfigurationClassParser{
    
    private void processMemberClasses(ConfigurationClass configClass, SourceClass sourceClass,
                                      Predicate<String> filter) throws IOException {

        //获取该类的内部类并又包装成sourceClass对象
        Collection<SourceClass> memberClasses = sourceClass.getMemberClasses();
        if (!memberClasses.isEmpty()) {
            List<SourceClass> candidates = new ArrayList<>(memberClasses.size());
            for (SourceClass memberClass : memberClasses) {
                //如果类是候选的
                if (ConfigurationClassUtils.isConfigurationCandidate(memberClass.getMetadata()) &&
                        !memberClass.getMetadata().getClassName().equals(configClass.getMetadata().getClassName())) {
                    candidates.add(memberClass);
                }
            }
            //排序
            OrderComparator.sort(candidates);
            //循环去处理每一个内部类
            for (SourceClass candidate : candidates) {
                if (this.importStack.contains(configClass)) {
                    this.problemReporter.error(new CircularImportProblem(configClass, this.importStack));
                }
                else {
                    this.importStack.push(configClass);
                    try {
                        //candidate 子  configClass 父，candidate 是 configClass的内部类
                        processConfigurationClass(candidate.asConfigClass(configClass), filter);
                    }
                    finally {
                        this.importStack.pop();
                    }
                }
            }
        }
    }
}
````

如果类中有内部类，那么要先拿到内部类，因为内部类上面也有可能有注解

````java
public class ConfigurationClassParser{
    public Collection<SourceClass> getMemberClasses() throws IOException {
        Object sourceToProcess = this.source;
        if (sourceToProcess instanceof Class) {
            Class<?> sourceClass = (Class<?>) sourceToProcess;
            try {
                //获取内部类
                Class<?>[] declaredClasses = sourceClass.getDeclaredClasses();
                List<SourceClass> members = new ArrayList<>(declaredClasses.length);
                for (Class<?> declaredClass : declaredClasses) {
                    members.add(asSourceClass(declaredClass, DEFAULT_EXCLUSION_FILTER));
                }
                return members;
            }
            catch (NoClassDefFoundError err) {
                // getDeclaredClasses() failed because of non-resolvable dependencies
                // -> fall back to ASM below
                sourceToProcess = metadataReaderFactory.getMetadataReader(sourceClass.getName());
            }
        }
    }
}


链接：https://blog.csdn.net/qq_27023083/article/details/113245337
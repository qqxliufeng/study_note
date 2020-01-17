## Spring学习笔记

### 1、Spring与SpringMVC的区别

Spring中维护了一个容器，在SpringMVC中也维护了一个容器，两个容器是父子关系。即Spring的容器是SpringMVC的父容器，SpringMVC容器可以向Spring容器获取Bean，反之则不可以。

对于web应用来说就是，SpringMVC管理着@Controller的Bean而，Spring管理着@Service和@Repository的Bean。对应的配置文件一般就是**applicationContext.xml**和**xxx-servlet.xml**

+ 如果只是在applicationContext.xml配置**<context:component-scan base-package="xx.xx"/>**，而**xxx-servlet.xml**中没有配置任何东西，则会出现**404**的错误。这是因为**SpringMVC**在处理请求的时候会到**SpingMVC**容器中找bean，比如：**处理器和处理器适配器**，但是在**xxx-servlet.xml**没有配置，也就是找不到。所以就会出现**404**的错误
+ 如果只是在**xxx-servlet.xml**配置**<context:component-scan base-package="xx.xx"/>**包扫描器，而**applicationContext.xml**没有配置任何东西，则不会影响访问，但是在用到一些**事务和aop**的功能的时候就会出现错误。
+ 因此我们一般在开发的时候要在**xxx-servlet.xml**中配置有**@Controller**注解中的**bean**，**applicationContext.xml**配置其它的。

### 2、在xxx-servlet.xml中有无`<mvc:annotation-driver/>`注解驱动的区别

+ 没有此注解驱动的时候：springmvc在初始化处理器时候会通过一个DispatcherServlet.properties中的配置：

  ```.properties
  org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
  	org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping
  ```

  创建两个默认的处理器映射器，所以如果我没有在配置`<mvc:annotation-driver/>`也是能正常访问的，而不会出现404的错误。但是当我们加上一些其它配置的时候如：`<mvc:default-servlet-handler/>`就会出现404的错误。因为此时**SpringMVC**不会按照默认策略去初始化，只会根据配置去初始化一个`SimpleUrlHandlerMapping`的处理器映射器，但此处理器**只能处理静态请求，没有能力处理动态请求**，所以在访问动态请求的时候就会出现404的错误 ，此时还得需要配置`<mvc:annotation-driver/>`注解驱动才可。

+ 有此注解驱动的时候：SpringMVC不会初始默认的处理器映射器，而是会通过配置去初始化一个叫`RequestMappingHandlerMapping`的处理器映射器，如下图：

  ![](/Users/lf/Desktop/学习笔记/spring图片/C698C7A3-A0D3-45FA-916C-A71716C55BBF.png)

  但此时请求静态资源的时候，还是会404，其根本原因还是因为没有能处理静态资源的`servlet`

  所以我们在配置的时候即要加上`<mvc:annotation-driver/>`又要加上`<mvc:default-servlet-handler/>`这样我们的就处理器映射器就如下图：

  ![](/Users/lf/Desktop/学习笔记/spring图片/A3646133-2944-4986-84CC-DD6C7DF924CB.png)

这样就可以了。在一个请求到来的时候会从上面查找如果找不到就往下查找，直到所有的处理器映射器都找不到能处理这个请求的处理器就会出现404，如果找到了就处理。

### 3. AOP

原理：

> 某个类的的某个方法在执行的时候需要额外的先执行一些其它操作和后执行一些操作等，这样就可以写一个代理，代理方法执行，在这个代理执行的时候动态的插入前置和后置操作。
>
> 这样就会出现三个类：
>
> > 1. 要代理的类
> > 2. 代理类
> > 3. 要执行前后等操作的类

**要代理的类：**

```java
public class MyCalculator implements ICalculator{

    @Override
    public int add(int a, int b) {
        System.out.println("被代理类的目标方法执行了…");
        return 0;
    }

}
```

​	在java原生的动态代理的实现中，被代理类必须要实现一个接口`ICalculator`，如下：

```java
public interface ICalculator {

    public int add (int a, int b);

}
```



**代理类：**

```java
public class MyProxy {

    public static Object proxy(final MyCalculator myCalculator) {
        return Proxy.newProxyInstance(myCalculator.getClass().getClassLoader(), myCalculator.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                MyLogUtils.start();
                Object result = method.invoke(myCalculator, args);
                MyLogUtils.end();
                return result;
            }
        });
    }
}
```

**要执行前后等操作的类：**

```java
public class MyLogUtils {

    public static void start() {
        System.out.println("方法要开始执行了");
    }

    public static void end () {
        System.out.println("方法执行完了");
    }


```

测试：

```java

public class MyTest {
    public static void main(String[] args) {
        MyCalculator myCalculator = new MyCalculator();
        ICalculator iCalculator = (ICalculator) MyProxy.proxy(myCalculator);
        iCalculator.add(1,2);
    }
}

```

执行结果：

```properties
方法要开始执行了
被代理类的目标方法执行了…
方法执行完了
```

***

动态代理的执行流程：

> 创建一个动态代理对象，由java内部创建，并且是实现了接口（ICalculator）的一个对象，当这个对象调用`add `方法的时候，就会调用`new InvocationHandler() {
>             @Override
>             public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
>                 MyLogUtils.start();
>                 Object result = method.invoke(myCalculator, args);
>                 MyLogUtils.end();
>                 return result;
>             }
>         }`这个对象的`invoke`方法，这个方法内部就是利用java的反射来执行具体的目标方法，并且在该方法前后 都会执行一些其它代码，如`MyLogUtils.start（）`和`MyLogUtils.end()`方法。这样就实现了动态代理。

为什么目标类是实现一个接口：

由于 java的特性，只能继承一个父类，代理对象已经继承了java中的`Proxy`类，就无法再实现其它类，但是可以实现多个接口，所以目标方法就必须实现一个接口。

动态代理对象通过java的反射技术被创建，是实现了目标类的接口了的，所在动态代理对象就可以实现接口中的方法，如：

`add(int, int)`方法，在调用此方法的时候，就会执行`InvocaiotnHandler`中的`invoke`方法。

***

以上就是java原生的动态代理实现过程和原理。当然目标类可以不一定非得实现一个接口，可以用第三方的库`cglib`来实现动态代理。Spring对两种方式都有实现。
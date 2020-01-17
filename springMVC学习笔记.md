## SpringMVC学习笔记

### SpringMVC中的九大组件

1. **HandlerMapping**

   用来查找请求过来的对应的Handler(处理器)，在springmvc中每一个请求都会对应一个处理器，而HandlerMapping就是用来查找对应的处理器。

2. **HandlerAdapter**

   用来查找具体的适配器来处理请求，我的个人理解就是，处理器映射器是找到哪个类的哪个方法来处理请求，而适配器就是来执行这个方法来处理请求。

3. **HandlerExceptionResolver**

   用来处理在请求过程中出现的各种错误异常

4. **ViewResolver**

   ViewResolver用来将String类型的视图名和Locale解析为View类型的视图。View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html（也可能是其它类型）文件。这里就有两个关键问题：使用哪个模板？用什么技术（规则）填入参数？这其实是ViewResolver主要要做的工作，ViewResolver需要找到渲染所用的模板和所用的技术（也就是视图的类型）进行渲染，具体的渲染过程则交由不同的视图自己完成。

5. RequestToViewNameTranslator

   ViewName是根据ViewName查找View，但有的Handler处理完后并没有设置View也没有设置ViewName，这时就需要从request获取ViewName了，如何从request中获取ViewName就是RequestToViewNameTranslator要做的事情了。RequestToViewNameTranslator在Spring MVC容器里只可以配置一个，所以所有request到ViewName的转换规则都要在一个Translator里面全部实现。

6. LocaleResolver

    解析视图需要两个参数：一是视图名，另一个是Locale。视图名是处理器返回的，Locale是从哪里来的？这就是LocaleResolver要做的事情。LocaleResolver用于从request解析出Locale，Locale就是zh-cn之类，表示一个区域，有了这个就可以对不同区域的用户显示不同的结果。SpringMVC主要有两个地方用到了Locale：一是ViewResolver视图解析的时候；二是用到国际化资源或者主题的时候。

7. ThemeResolver

    用于解析主题。SpringMVC中一个主题对应一个properties文件，里面存放着跟当前主题相关的所有资源、如图片、css样式等。SpringMVC的主题也支持国际化，同一个主题不同区域也可以显示不同的风格。SpringMVC中跟主题相关的类有 ThemeResolver、ThemeSource和Theme。主题是通过一系列资源来具体体现的，要得到一个主题的资源，首先要得到资源的名称，这是ThemeResolver的工作。然后通过主题名称找到对应的主题（可以理解为一个配置）文件，这是ThemeSource的工作。最后从主题中获取资源就可以了。

8. **MultipartResolver**

   用于处理上传请求。处理方法是将普通的request包装成MultipartHttpServletRequest，后者可以直接调用getFile方法获取File，如果上传多个文件，还可以调用getFileMap得到FileName->File结构的Map。此组件中一共有三个方法，作用分别是判断是不是上传请求，将request包装成MultipartHttpServletRequest、处理完后清理上传过程中产生的临时资源。   

9. FlashMapManager

   用来管理FlashMap的，FlashMap主要用在redirect中传递参数。

**九大组件的初始化过程：**

> 如果我们自己配置了相应的组件，那么springmvc就会用我们自己的组件，而对于没有配置的组件，除了`multipartResolver`之外，其它组件都会一个默认的初始化组件。

**dispatch-servlet.xml配置文件：**

+ 配置静态资源处理，自己配置**HandlerMapping**

  ```.xml
  <mvc:default-servlet-handler/>
  ```

  配置此项之后，会发现静态资源是可以请求到了，但是对于@Controller的请求会失效，还需再配置另一个配置：

  ```.xml
  <mvc:annotation-driven/>
  ```

  这样动、静态资源都可以请求到了

  这两个配置主要作用是告诉**springmvc**分别用不同的`handlerMapping`来处理请求，如果不加的话，`springmvc`会默认初始化两个处理器映射器：

  ```.properties
  org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
  org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping
  ```

  而这两个处理器是没法处理静态资源的。

  所以我们可以把这两个配置当作成自己配置**HandlerMapping**

### 异常处理机制

在`SpringMVC`中如果我们没有配置任何异常解析器，会从默认的配置中获取3个异常解析器：

```.xml
ExceptionHandlerExceptionResolver: 处理标注了@ExceptionHandler注解的异常
ResponseStatusExceptionResovler: 处理标注了@ResponseStatus注解的异常
DefaultHandlerExceptionResovler: 处理Spring中内部的异常
```

**SpringMVC在处理异常的时候会依次调用异常处理器来处理异常** ，如果我们没有配置任何有关异常的配置，那么`SpringMVC`就找不到对应的异常解析器，那么他就会直接把异常抛给`tomcat`服务器，从而我们就看见了那个显示 **500**的页面

1. **配置异常处理：（第一种方式：本类处理异常）**

```.java
@ExceptionHandler(Exception.class)
public ModelAndView handlerException(Exception ex) {
	ModelAndView mv = new ModelAndView("error");
	mv.addObject("exception", ex);
	return mv;
}
```

在**处理器类**上加上以上方法并用`@ExceptionHandler(Exception.claaa)`注解，就可以让SpringMVC处理异常了

**注**：在导入**ModelAndView**的时候，千万不要导错包，一定要是**org.springframework.web.servlet.ModelAndView**，而不是**org.springframework.web.portlet.ModelAndView**，否则会出现**404**的错误。

2. **配置异常处理：（第二种方式：全局处理异常）**

```.java

@ControllerAdvice
public class MyExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ModelAndView handlerError01(Exception ex, Model model) {
        System.out.println("全局异常处理" + ex);
        model.addAttribute("ex",ex);
        return new ModelAndView("error");
    }
}
```

新建一个类用`@ControllerAdvice`注解标注，这样就可以生成一个全局的异常处理器，当某一个处理器中没有相应的异常处理方法的时候，**springmvc**就会调用全局的异常处理器来处理此异常；**当一个处理器中有自己的异常处理方法时，会优先调用本类的异常处理器。**

**注：**如果在**dispatch-servlet.xml**中出现了以下配置：

```.xml
 <context:component-scan base-package="com.test" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```

一定要把**<context:include-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>**也加入到配置中，这样就相当于放到了springmvc的容器中了。如下配置：

```.xml
<context:component-scan base-package="com.test" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <context:include-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
    </context:component-scan>
```

以上两种方式的异常处理器都是交给了`org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver`这个异常解析器处理了。

3. **ResponseStatusExceptionResovler**

如果他想生效前提是**ExceptionHandlerExceptionResolver**不能生效执行。他对应的注解就是：`@ResponseStatus`

一般是把他标注在一个自定义的异常类上，如：

```.java
@ResponseStatus(reason = "没找到此用户", value = HttpStatus.NOT_FOUND)
public class NotFountUserException extends RuntimeException{
}
```

**如果把它标注在方法上，则该方法无论有无异常都会返回一个异常信息，若是该方法没有异常则会执行完。**也就是说你返回你的异常，我正常执行我的方法

4. **DefaultHandlerExceptionResovler**

若是以上解析器都没有处理成功，则会让此解析器执行，若是此解析器也没有执行成功，则直接抛给`tomcat`，此解析器主要是解析springmvc内部的一些异常，如：目标方法要求是**POST**方式，而用**GET**请求，则就是抛出异常，此解析器就可以处理。

### 目标方法上的POJO参数的值确定

1. 根据key(若是标有ModelAttribute("key")，key就是"key"值，没有就是参数名首字母小写，如Book book, key就是book)先从隐含模型中取。
2. 如果有@SessionAttribute注解，若是隐含模型中没有值就从@SessionAttribute中取，若是没有则抛异常
3. 再没有就通过反射创建一个对象。

最后把请求中的参数和pojo对象一一对应映射。

### SpringMVC中的拦截器

+ **与filter的区别**

  1. 拦截器是基于java的反射机制的，而过滤器是基于函数回调的。
  2. 拦截器不依赖于Servlet容器，而过滤器依赖与Servlet容器。
  3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求其作用。
  4. 拦截器可以访问action的上下文、值栈里面的对象，而过滤器不能访问。
  5. 拦截器可以获取IOC容器中的Bean，而过滤器不行。

+ 配置

  1. 编写一个**java****类继承自**HandlerInterceptor**，如：

     ```.java
     public class MyInterceptor implements HandlerInterceptor {
     
     		/**
          * 在目标方法运行先执行
          * @param request 请求体
          * @param response 响应体
          * @return false 说明拦截请求
          * @throws Exception
          */
         @Override
         public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
             System.out.println("preHandler invoke");
             return false;
         }
     
     		/**
          * 目标方法执行之后，会立即执行postHandler
          * @param request
          * @param response
          * @param handler
          * @param modelAndView
          * @throws Exception
          */
         @Override
         public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
             System.out.println("postHandle invoke");
         }
     
     		/**
          * 请求处理完成之后调用
          * @param request
          * @param response
          * @param handler
          * @param ex
          * @throws Exception
          */
         @Override
         public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
             System.out.println("afterCompletion invoke");
         }
     }
     ```

  2. 在`dispache-servlet.xml`中配置如下:

     ```.xml
     <mvc:interceptors>
         <bean class="com.test.controller.admin.MyInterceptor"></bean>
     </mvc:interceptors>
     ```

     此写法是默认拦截所有的请求，如果只想拦截指定的请求，则可以按下面的配置：

     ```.xml
     <mvc:interceptors>
        <mvc:interceptor>
        		<mvc:mapping path="/hello1"/>
        		<bean class="com.test.controller.admin.MyInterceptor"></bean>
        </mvc:interceptor>
      </mvc:interceptors>
     ```

     这样就可以只拦截指定的`/hello1`请求了

+ 运行流程:

  1. 多个拦截器中只要有一个拦截器的`preHandler`方法返回`false`那么都不会执行目标方法
  2. 只有`preHandler`方法返回`true`放行之后，`postHandler`和`afterCompletion`才可以执行。
  3. 如果有某一个拦截器拦截了，那么该的拦截器的`afterCompletion`不会执行，而其它的拦截器的`afterCompletion`方法还是会执行的。
  4. SpringMVC会根据配置文件的配置顺序决定拦截器的执行顺序。

+ 示例：

  准备两个拦截器：**Interceptor1**,**Interceptor2**,

  + 两个拦截器都不拦截的执行流程：

    ```.txt
    Interceptor1 preHandle invoke
    Interceptor2 preHandle invoke
    目标方法执行了……
    Interceptor2 postHandle invoke
    Interceptor1 postHandle invoke
    Interceptor2 afterCompletion invoke
    Interceptor1 afterCompletion invoke
    ```

  + 第一个拦截器拦截的执行流程：

    ```.txt
    Interceptor1 preHandle invoke
    ```

  + 第二个拦截器拦截的执行流程：

    ```.txt
    Interceptor1 preHandle invoke
    Interceptor2 preHandle invoke
    Interceptor1 afterCompletion invoke
    ```

  + 目标方法出现异常了：

    ```.txt
    Interceptor1 preHandle invoke
    Interceptor2 preHandle invoke
    目标方法执行了……
    Interceptor2 afterCompletion invoke
    Interceptor1 afterCompletion invoke
    ```

    


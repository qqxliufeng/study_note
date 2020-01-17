## tomcat学习笔记

tomcat 处理的三种资源类型：

> **静态资源，如css,html,js,jpg,png等**

> **Servlet**

> **JSP**

所有的请求都会经过**servlet**容器的处理，即使静态资源也不例外，会被一个叫做**DefaultServlet**的**Servlet**处理， 这一点和nginx还是不一样的，所**tomcat**不太适合处理静态资源，需要进一步映射才行，这样会降低**tomcat**的处理请求的能力。

**WEB-INF**目录：

在此目录下的文件是不可以直接用浏览器来访问的，必须经过服务器的处理之后才行。该目录下存放的文件夹包含：

> **lib**（用于存放一些jar包）
>
> **web.xml**
>
> **jsp**页面
>
> **classes**
>
> **springmvc**的配置，（若是用的springmvc开发的web项目，如：applicationContext.xml，dispatcher-servlet.xml）

对于静态资源最好是放在和在**WEB-INF**同级下的目录中，如：static，最后的目录结构如下：

> **static**
>
> > **js**
> >
> > **css**
> >
> > **images**
> >
> > **html**
>
> **WEB-INF**
>
> > **classes**
> >
> > **lib**
> >
> > **pages(存放一些jsp页面)**
> >
> > **index.jsp**
> >
> > **web.xml**

若是用springmvc开发项目，则还得配置一下**web.xml**的**servlet-mapping**如下：

```.xml
<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
	// *.do 可以任意取值，只要不是 / 或者 /* 就行
	<url-pattern>*.do</url-pattern>
</servlet-mapping>
```



这样配置的目的是，不要把静态资源的请求交给`dispatch`即`org.springframework.web.servlet.DispatcherServlet`	这个`servlet`去处理，而是用**tomcat**中**web.xml**中的**DefaultServlet**去处理静态资源

如果因为一些原因不能更改web.xml中的配置，必须要用以下配置：

```.xml
<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

有两种处理方式：

1. **springmvc中的配置文件中加下以下配置：**

```.xml
<mvc:default-servlet-handler/>
```

这是要告诉springmvc对于静态资源去调用tomcat中的web.xml中的**DefaultServlet**去处理

2. **springmvc中的配置文件中加下以下配置：**

```.xml
<mvc:resources mapping="/static/**" location="/static/" />
```



做一个静态资源映射。用此方式配置一定在再加一个配置：

```.xml
<mvc:annotation-driven/>
```

否则静态资源可以访问，但是controller不能访问，出现404的错误

## 此方式还可以解决把静态资源放在**WEB-INF**目录下不能访问的问题 ##


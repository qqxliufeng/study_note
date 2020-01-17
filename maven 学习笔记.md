## maven 学习笔记 

### 1. maven是什么

`Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理 。`

### 2. 什么是构建

`就是以我们编写的Java代码、框架配置文件、国际化等其他资源文件、jsp页面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。`

eclipse中的项目与tomcat中编译结果对比：

![](/Users/lf/Desktop/学习笔记/maven图片/20170728201331986.png)

### 3.构建的主要环节

> 1. 清理：删除以前的编译结果，为重新编译做好准备。
> 2. 编译：将Java源程序编译为字节码文件。
> 3. 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。
> 4. 报告：将每一次测试后以标准的格式记录和展示测试结果。
> 5. 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java工程对应jar包，Web工程对象war包。
> 6. 安装：在Maven环境下特指将打包的结果——Jar包或War包安装到本地仓库中。
> 7. 部署：将打包的结果部署到远程仓库或将war包部署到服务器上运行。

### 4. Maven的核心概念

> 1. 约定的目录结构
> 2. POM
> 3. 坐标
> 4. 依赖
> 5. 仓库
> 6. 生命周期/插件/目标
> 7. 继承
> 8. 聚合

### 5. 约定的目录结构

![](/Users/lf/Desktop/学习笔记/maven图片/20170728213202634.png)

### 6. 常用命令

> 注意：执行与构建过程相关的Maven命令，必须进入pom.xml 所在的目录。

【1】mvn clean : 清理
【2】mvn compile : 编译主程序
【3】mvn test-compile : 编译测试程序
【4】mvn test : 执行测试
【5】mvn package : 打包
【6】mvn install ： 安装
【7】mvn site ：生成站点

### 7. POM文件

`含义：Project Object Model 项目对象模型
类似的DOM ：Document Object Model 文档对象模型`

### 8. 坐标

> 使用下面三个向量在仓库中唯一定位一个Maven工程

> groupid:公司或组织域名倒序+项目名
>
> ```.xml
> <groupid>com.atguigu.maven</groupid>
> ```
>
> artifactid:模块名
>
> ```.xml
> <artifactid>Hello</artifactid>
> ```
>
> version：版本
>
> ```.xml
> <version>1.0.0</version>
> ```
>
> 如Spring：
>
> ```.xml
> <groupId>org.springframework</groupId>
> <artifactId>spring-core</artifactId>
> <version>4.0.0.RELEASE</version>
> org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar
> ```

### 9. 仓库

`仓库中保存的内容：Maven工程
Maven自身所需要的插件
第三方框架或工具的jar包
我们自己开发的Maven工程`

> 1. 本地仓库：当前电脑上部署的仓库目录，为当前电脑上所有Maven工程服务
> 2. 私服：搭建在局域网环境中，为局域网范围内的所有Maven工程服务
> 3. 中央仓库：假设在Internet上，为全世界所有Maven工程服务
> 4. 中央仓库镜像：为了分担中央仓库流量，提升用户访问速度

### 10. 依赖

1. *概念*

   `当 A jar 包用到了 B jar 包中的某些类时，A 就对 B 产生了依赖，这是概念上的描述。Maven解析依赖信息时会到仓库中查找被依赖的jar包。
   `

   **对于我们自己开发的Maven工程，要使用mvn install 命令安装后就可以进入仓库**

   ![](/Users/lf/Desktop/学习笔记/maven图片/20170730165534132.png)

2. *依赖范围*

   ![](/Users/lf/Desktop/学习笔记/maven图片/20170730170652211.png)

3. *依赖的传递性*

   `A依赖B，B依赖C，A能否使用C呢？要看B依赖C的范围是不是compile`

   ![](/Users/lf/Desktop/学习笔记/maven图片/20170730171006616.png)

4. 依赖的排除

   `如果我们当前工程中引入了一个依赖是A，而A又依赖了B，那么Maven会自动将A依赖的B引入当前工程，但是个别情况下B有可能是一个不稳定版本，或对当前工程有不良影响。这时我们可以在引入A的时候将B排除。`

   如：

   ![](/Users/lf/Desktop/学习笔记/maven图片/20170730172241301.png)

配置方式：

```.xml
<dependency>
<groupId>com.atguigu.maven</groupId>
<artifactId>HelloFriend</artifactId>
<version>0.0.1-SNAPSHOT</version>
<type>jar</type>
<scope>compile</scope>
<exclusions>
    <exclusion>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
    </exclusion> 
</exclusions>
</dependency>
```

5. 统一管理版本号

   > 1. 声明
   >
   >    ![](/Users/lf/Desktop/学习笔记/maven图片/20170730173201613.png)
   >
   > 2. 引用
   >
   >    ![](/Users/lf/Desktop/学习笔记/maven图片/20170730173318435.png)
   >
   > 3. 其它用法
   >
   >    ![](/Users/lf/Desktop/学习笔记/maven图片/20170730173423688.png)

6. 依赖冲突原则

   > 1. 路径最短者优先
   >
   >    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810171428094.png)
   >
   > 2. 路径相同时先声明者优先
   >
   >    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810171449146.png)

### 11. 生命周期

各个构建环节执行的顺序：不能打乱顺序，必须按照既定的正确顺序来执行。

Maven的核心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的。
Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期中各个阶段：不论现在要执行生命周期中的哪一阶段，都是从这个生命周期最初的位置开始执行。
Maven有三套相互独立的生命周期，分别是：
①Clean Lifecycle 在进行真正的构建之前进行一些清理工作。
②Default Lifecycle 构建的核心部分，编译、测试、打包、安装、部署等等。
③Site Lifecycle 生成项目报告，站点，发布站点。

他们相互独立。也可以直接运行 mvn clean install site 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。

Clean声明周期
①pre-clean 执行一些需要在clean之前完成的工作
②clean 移除所有上一次构建生成的文件
③post-clean 执行一些需要在clean 之后立刻完成的工作

Default声明周期
Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段：
validate
generate-sources
process-sources
generate-resources
process-resources 复制并处理资源文件，至目标目录，准备打包。
compile 编译项目的源代码。
process-classes
generate-test-sources
process-test-sources
generate-test-resources
process-test-resources 复制并处理资源文件，至目标测试目录。
test-compile 编译测试源代码。
process-test-classes
test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。
prepare-package
package 接受编译好的代码，打包成可发布的格式，如 JAR。
pre-integration-test
integration-test
post-integration-test
verify
install 将包安装至本地仓库，以让其它项目依赖。
deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

Site生命周期
①pre-site 执行一些需要在生成站点文档之前完成的工作
②site 生成项目的站点文档
③post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
④site-deploy 将生成的站点文档部署到特定的服务器上

这里经常用到的是 site 阶段和 site-deploy 阶段，用以生成和发布 Maven 站点，这可是 Maven 相当强大的功能，Manager 比较喜欢，文档及统计数据自动生成，很好看。

插件和目标
Maven的核心仅仅定义了抽象的声明周期，具体的任务都是交由插件完成的。
每个插件都实现多个功能，每个功能就是一个插件目标
Maven的生命周期与插件目标相互绑定，以完成某个具体的构建任务。
可以将目标看做“调用插件功能的命令”

例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。

### 12. 继承

`现状
Hello依赖的Junit：4.0
HelloFriend依赖的Junit：4.0
MakeFriends依赖的Junit：4.9 `

由于test范围的依赖不能传递，所以必然会分散在各个模块工程中，很容易造成版本不一致。

解决思路：将Junit依赖统一提取到“父”工程中，在子工程中声明Junit依赖是不指定版本，以父工程中统一设定的为准。同时也便于修改。

步骤：

> 1. 创建一个Maven工程作为父工程。注意：打包方式为pom
>
>    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810175950040.png)
>
> 2. 在子工程中声明对父工程的引用
>
>    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810180137704.png)
>
> 3. 将子工程的坐标中与父工程坐标中重复的内容删除
>
>    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810180306736.png)
>
> 4. 在父工程中统一管理Junit的依赖
>
>    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810180410293.png)
>
> 5. 在子工程中删除Junit依赖的版本号部分
>
>    ![](/Users/lf/Desktop/学习笔记/maven图片/20170810180504760.png)

**注意：配置集成后，执行安装命令时要先安装父工程。**

### 13. 聚合

`作用：一键安装各个模块工程。`

`配置方式：在一个“总的聚合工程”中配置各个参与聚合的模块`

![](/Users/lf/Desktop/学习笔记/maven图片/20170810191758989.png)

`使用方式：进入聚合工程的pom.xml 输入命令 maven install`
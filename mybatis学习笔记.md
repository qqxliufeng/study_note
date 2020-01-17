## MyBatis学习笔记

+ ### **在mybatis中有两个重要的配置文件：**

> Mybatis-config.xml： 全局配置文件
>
> xxxx.xml： sql映射文件

我们在写的时候基本都是在写这两个文件：比较重要的就是sql映射文件

**全局配置文件如下：**

```.xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<configuration>
	// 加载一个外部配置文件
	<properties resource="xx.properties"></properties>
	<typeAliases>
			// 给某一个类起一个别名
			<typeAlias type="xx.xxx.xxx.xxx" value="x"><typeAlias>
			// 给某一个包下的所有类起别名；默认的别人就是类名首字母小写；比如com.test.bean.User别名就是user
			<package name="xx.xxx.xxx"/>
	</typeAliases>
	//这个配置比较重要，可配置项比较多，以后用到了再来详细补充
	<settings>
		 //配置把数据库字段是否自动转成java驼峰命名；如：t_user表中的user_name转成java属性名就是：userName
		 <setting name="mapUnderscoreToCamelCase" value="false" />
	</settings>
	// 环境配置，主要是配置事务管理器和数据源，基本不用，以后用Spring中的事务管理器和专门的数据源
	<environments default="development">
		<!-- id为环境的唯一标识 -->
		<environment id="development">
			<!-- 事物管理 -->
			<transactionManager type="JDBC" />
			<!-- 数据源 -->
			<dataSource type="POOLED">
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
	// 多数据库支持， 不同的数据库在sql语法上可能有不一样的写法，主要是为了适配能在切换数据库的时候不sql能用；基     本上也用不着这个配置，不会随便去切换数据库的
	<databaseIdProvider type="DB_VENDOR">
		<property name="MySQL" value="mysql"></property>
		<property name="Oracle" value="oracle"></property>
		<property name="SQL Server" value="sql server"></property>
	</databaseIdProvider>
	//sql映射文件注册，最重要的就是这个配置，以后我们在写的每个sql映射文件都必须在这配置以后才能生效
	<mappers>
		// 通过引入一个映射文件来配置
		<mapper resource="com/example/mybatis/dao/UserMapper.xml"></mapper>
		// 通过配置java类名来配置，但这样做必须保证类文件和映射文件同名而且在同一个文件目录下；如:
		|xxx.xxx.dao
			|-- UserDao.java
			|-- UserDao.xml
		<mapper class="com.example.mybatis.dao.UserMapperAnnotaion"></mapper>
		// 把一个包下的所有文件都配置进来，达到批量注册的效果
		<package name="xx.xxx.xxx"/>
	</mappers>
</configuration>
```

**sql映射文件：**

```.xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.PersonDao">
		// 查询
    <select id="getUserById" resultType="com.bean.User">
        select * from user where id=#{id}
    </select>
    //更新
    <update id="updateUser">
        update user set password=#{password} where id=#{id}
    </update>
    //新增
    <insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        insert into user values(null, #{name}, #{loginTime}, #{password})
    </insert>
</mapper>
```

**java文件：**

```.java
package com.dao;

import com.bean.User;
import org.apache.ibatis.annotations.Param;

public interface PersonDao {
    public User getUserById(Integer id);
    public void updateUser(@Param("id") Integer id, @Param("password") String password);
    public void insertUser(User user);
}
```

+ ### 获取**自增主键**的设置

默认的情况下，在新增一条记录之后是获取不到该记录的主键`ID`的，要想获得自增的主键必须通过配置，如：

```.xml
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        insert into user values(null, #{name}, #{loginTime}, #{password})
</insert>
```

**useGeneratedKeys="true"**告诉mybatis，要使用自增主键id；

**keyProperty="id"**告诉mybatis，把自增`ID`存入到pojo中的`ID`属性中

+ ### 获取**非自增主键**的设置

对于一些数据库或者在一些特定的业务场景下我们没法使用自增id，这个时候就需要进行额外的配置来获取主键，如

```.xml
<insert id="insertUser">
	<seletKey order="before" returnType="integer" keyProperty="id">
		select max(id) + 1 from user
	</selectKey>
   insert into user values(#{id}, #{name}, #{loginTime}, #{password})
</insert>
```

```.xml
<seletKey order="before" returnType="integer" keyProperty="id">
		select max(id) + 1 from user
</selectKey>
```

告诉mybatis，在执行insert into 这条sql之前先执行`select max(id) + 1 from user`这个sql，并把返回值给对象的id属性

+ ### 方法参数的获取规则

对于`java类中的方法参数`mybatis有自己的解析规则：

1. **单个参数（基本数据类型）**，如：

   ```.java
   public User getUserById(Integer id);
   ```

   在sql映射文件中，获取的时候可以取任意名，不一定非得是`id`如：

   ```.xml
   <select id="getUserById" resultType="com.bean.User">
       select * from user where id=#{id} //可以是 id=#{hahaha}
   </select>
   ```

   因为对于单个参数，始终都是取的那一个参数，所以无论叫什么名都会把这个单一的参数放到**#{}**中

2. **多个参数**，如：

   ```.java
   public void updateUser(Integer id, String password);
   ```

   在sql映射文件中，获取的时候不可以按照`id`,`password`这样的key来联取值，如：

   ```.xml
   <update id="updateUser">
   		update user set password=#{password} where id=#{id}
   </update>
   ```

   这样写的话会报错。

   因为在mybatis中，对于多个参数，他会把所有的参数都封装在一个`Map`集合中，而map的key就是每一个参数的索引下标，value就是参数的值，如：

   ```.java
   Map<String, Object> map = new HashMap<String, Object>();
   map.put("0", id);
   map.put("1", password);
   ```

   所以在取值的时候就只能按照下标的方式来取值，如：

   ```.xml
   <update id="updateUser">
   		update user set password=#{1} where id=#{0} //或者
   		update user set password=#{param1} where id=#{param0}
   </update>
   ```

   对于这种情况，我们可以通过给参数上加一个注解：`@Param("名称")`来告诉mybatis用我的参数名来存放value，如：

   ```.java
       public void updateUser(@Param("id") Integer id, @Param("password") String password);
   ```

   这样我们在映射文件取值的时候就可以按照名称来取值了，如：

   ```.xml
   <update id="updateUser">
   		update user set password=#{id} where id=#{password}
   </update>
   ```

3. **单个参数，Map类型的**，可以直接传入一个map集合，用你自己想指定的key，在映射文件获取的时候就可以用你自己起的key来获取

4. **单个参数，pojo类型的**，可以直接传入一个pojo对角，在映射文件文件取值的时候直接用对象的属性名来获取取可，不需要再`对象.属性`的方式，直接`属性`就行了
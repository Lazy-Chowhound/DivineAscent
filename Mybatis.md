[toc]

# #{} 和 ${} 的区别

​	*MyBatis* 在处理 #{} 时，会将 *SQL* 中的 #{} 替换为 ?，预编译 *SQL*，通过 *PreparedStatement* 的 *setXxxx* 的方法进行参数赋值。使用 #{} 可以有效地防止 *SQL* 注入。

​	*MyBatis* 在处理 ${} 时，会直接把 ${} 替换为参数值，存在 *SQL* 注入的风险。

# 这个*Dao*接口的工作原理是什么？*Dao*接口里的方法，参数不同时，方法能重载吗？

​	*Mapper* 接口的工作原理是*JDK*动态代理，*Mybatis*运行时会使用*JDK*动态代理为*Mapper*接口生成代理对象*proxy*，代理对象会拦截接口方法，根据类的全限定名+方法名，唯一定位到一个*MapperStatement*并调用执行器执行所代表的*sql*，然后将*sql*执行结果返回。

​	*Mapper*接口里的方法，是不能重载的，因为是使用全限定名+方法名的保存和寻找策略。

​	*Dao*接口即*Mapper*接口。**接口的全限名，就是映射文件中的*namespace*的值**；接口的方法名，就是映射文件中*Mapper*的*Statement*的*id*值；接口方法内的参数，就是传递给*sql*的参数。

​	当调用接口方法时，接口全限名+方法名拼接字符串作为*key*值，可唯一定位一个*MapperStatement*。在*Mybatis*中，每一个*SQL*标签，比如<*select*>、<*insert*>、<*update*>、<*delete*>标签，都会被解析为一个*MapperStatement*对象。

> 举例：*com*.*mybatis*3.*mappers*.*StudentDao*.*findStudentById*，可以唯一找到*namespace*为*com*.*mybatis*3.*mappers*.*StudentDao*下面 *id* 为 *findStudentById* 的 *MapperStatement*。

# *MyBatis*是如何将*sql* 执行结果转换为目标对象并返回的？有哪些映射形式？

1. <*select*> 标签使用 *resultType* 参数，传递 *Java* 类，*sql* 中 *select* 的字段名保持与 *Java* 类属性名称一致

```sql
<select id="selectUser" resultType="constxiong.po.User" parameterType="constxiong.po.User">
  select * from user where id = #{id}
</select>
```

2. 使用 <*resultMap*> 标签，定义数据库列名和对象属性名之间的映射关系

```sql
<select id="selectUserByResultMap" resultMap="userMap" parameterType="constxiong.po.User">
  select * from user where id = #{id}
</select>

<resultMap id="userMap" type="constxiong.po.User">
  <id property="id" column="id" />
  <result property="mc" column="name"/>
</resultMap>
```

3. 使用注解 *select* 的字段名保持与接口方法返回的 *Java* 类或集合的元素类的属性名称一致。

```java
@Select("select * from user")
List<User> selectAllUsers();
```

# *Mybatis*一级缓存和二级缓存

## 一级缓存

​	*SqlSession* 级别的缓存，默认开启。使用同一个*SqlSession*对象调用一个*Mapper*方法，往往只执行一次*SQL*，因为使用*SelSession*第一次查询后，*MyBatis*会将其放在缓存中，以后再查询的时候，*statementId*、*boundSql*.*getSql*() 执行 *sql*、查询参数、查询时要求的结果集中的结果范围都相同，即认为是同一次查询，返回缓存值。***SqlSession*执行*insert*、*update*、*delete*等操作*commit*后会清空该*SQLSession*缓存**。

​	每个 *SqlSession* 对象包含一个 *Executor* 对象，*Executor* 对象中 *localCache* 属性使用 *PerpetualCache* 对象缓存查询数据；从源码中看 *DefaultSqlSession* 的 *close*、*commit*、*rollback*、*insert*、*delete*、*update* 相关的方法都会触发 *BaseExecutor* 对象清掉缓存。

## 二级缓存

​	当*Mybatis*整合*Spring*后，直接通过*Spring*注入*Mapper*的形式，如果不是在同一个事务中每个*Mapper*的每次查询操作都对应一个全新的*SqlSession*实例，这个时候就不会有一级缓存的命中（但是在同一个事务中时共用的是同一个*SqlSession*）。

​	二级缓存是*Mapper*级别的缓存，多个*SqlSession*去操作同一个*Mapper*的*sql*语句，多个*SqlSession*可以共用二级缓存，二级缓存是跨*SqlSession*的。二级缓存的作用范围更大。

 # *Mapper*传递多个参数

1. 

```sql
<select id="selectUser"resultMap="BaseResultMap"> 
  select * from user  
  where username = #{0} and userarea=#{1} 
</select> 
```

2. 使用 @*param* 注解:

```java
public interface usermapper {
  user selectuser(@param(“username”) string username,@param(“hashedpassword”) string hashedpassword);
}
```

​	然后,就可以在*xml*像下面这样使用(推荐封装为一个*map*,作为单个参数传递给*mapper*):

```sql
<select id="selectuser" resultType="user">
	select id, username, hashedpassword 
	from some_table 
	where username = #{username} 
	and hashedpassword = #{hashedpassword}
</select>
```

3. 多个参数封装成*map*

```java
 try{
	//由于我们的参数超过了两个，而方法中只有一个Object参数收集，因此我们使用Map集合来装载我们的参数
	Map<String, Object> map = new HashMap();
    map.put("start", start);
	return sqlSession.selectList("StudentID.pagination", map);
 }catch(Exception e){}
```

# *association*和*collection*

>  *association*处理一对一、*collection*处理一对多

```sql
<select id="selectUserWithInfo" resultMap="UserWithInfo">
  select user.id, user.name, info.userid, info.name  
  from user,info 
  where user.id = info.user_id
</select>
```

---

 ```sql
 <!-- 1对多 -->
 <select id="selectUserWithArticles" resultMap="UserWithArticles">
   select user.id, user.name, article.userid, article.title 
   from user,article 
   where user.id = article.userid
 </select>
 ```

---

 ```sql
 <resultMap id="UserWithInfo" type="constxiong.po.User">
 	<id property="id" column="id"/>
 	<result property="name" column="name"/>
 	<!-- 1对1 -->
 	<association property="info" javaType="constxiong.po.Info">
 		<id property="userId" column="user_id"/>
 		<result property="name" column="info_name"/>
 	</association>
 </resultMap>
 ```

---

```sql
<resultMap id="UserWithArticles" type="constxiong.po.User">
	<id property="id" column="id"/>
	<result property="name" column="name"/>
	<!-- 1对多 -->
	<collection property="articles" ofType="constxiong.po.Article">
		<result property="userId" column="user_id"/>
		<result property="title" column="title"/>
	</collection>
</resultMap>
```



# *MyBatis*与*Hibernate*有哪些不同？

1. *Mybatis*和*Hibernate*不同，它不完全是一个*ORM*框架，因为*MyBatis*需要程序员自己编写*Sql*语句。

2. *Mybatis*直接编写原生态*sql*，可以严格控制*sql*执行性能，灵活度高。但是*mybatis*无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套*sql*映射文件，工作量大。 

3. *Hibernate*对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用*hibernate*开发可以节省很多代码，提高效率。

 

 

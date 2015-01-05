#### 使用Spring hibernate && JPA的方式访问数据库

#### Spring hibernate 访问数据库

hibernate 操作数据库依赖的是Session，所以首先要做的事情就是在Spring的配置文件里面配置Session Factory。

在配置Hibernate Session 工厂Bean的时候，需要确定持久层对象是通过XML文件还是通过注解来进行配置的，如果选择是通过xml文件来定义对象与数据库之间的映射，那么需要在Spring中配置LocalSessionFactoryBean;

比如以下配置信息：

```
 <bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
  	<property name="dataSource" ref="dataSource" />
  	<property name="mappingResources">
  		<list>
  			<value>student.hbm.xml</value>
  	  		</list>
  	</property>
  	<property name="hibernateProperties">
  		<props>
  			<prop key="dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
  		</props>
  	</property>
  </bean>
```

在配置LocalSessionFactoryBean 的时候，需要使用三个属性，第一个属性是制定数据源，第二个属性是制定hibernate映射文件的位置，第三个属性是配置hibernate如何操作的细节，这里使用的mysql的操作。

如果需要使用注解的方式来使用hibernate，可以用如下的配置：

```
<bean id="sessionFactory2"
		class="org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="packagesToScan" value="con.spring.demo.domain" />
		<property name="hibernateProperties">
			<props>
				<prop key="dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
			</props>
		</property>
</bean>
```
这里使用packagesToScan 表明hibernate将自动扫描这个包下的类并持久化这些类。

在配置好sessionFactory之后，就可以将其注入到自己的DAO类中，在使用的时候可以继承HibernateDaoSupport， 这个里面封装了HibernateTemplate，可以直接使用。

```
public class StudentDAO extents HibernateDaoSupport {
	...
}
```
在配置StudentDAO的时候就需要将sessionFactory 注入进入即可。

#### 使用 JPA的方式访问数据库

简单来说，基于JPA的应用程序使用EntityManagerFactory 的实现类来获取EntityManager实例，JPA定义了两种类型的实体管理器。

* 应用程序管理类型
* 容器管理类型
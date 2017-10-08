# Spring

http://repo.springsource.org/libs-release-local/org/springframework/spring/ 

> 接口就是为了解耦。。。今后的扩展与维护。。。与spring没有必然联系
> 至于spring 里面的配置，注入未必要接口，只要这个bean存在就行了。
> 但这样，spring效果就大在降低了。。。
> 另外接口并非只是在spring框架中才有解耦作用
> 详细的，网上查一下接口编程之类的信息。。。

- spring 管理bean 就是实例 默认以单例管理

#### IOC 控制反转

- 依赖注入

  - 属性注入；

    ```xml
    <bean id="people2" class="com.java1234.entity.People">
    	<property name="id" value="1"></property>
    	<property name="name" value="张三"></property>
    	<property name="age" value="11"></property>
    </bean>
    ```

  - 构造函数注入；(通过类型；通过索引；联合使用)

    ```xml
    <bean id="people3" class="com.java1234.entity.People">
    	<constructor-arg type="int" value="2"></constructor-arg>
    	<constructor-arg type="String" value="李四"></constructor-arg>
    	<constructor-arg type="int" value="22"></constructor-arg>
    </bean>
    ```

  - 工厂方法注入；(非静态工厂，静态工厂)

    ```xml
    <bean id="peopleFactory" class="com.java1234.factory.PeopleFactory"></bean>

    <bean id="people7" factory-bean="peopleFactory" factory-method="createPeople"></bean>


    <bean id="people8" class="com.java1234.factory.PeopleFactory2" factory-method="createPeople"></bean>
    ```

  - 泛型依赖注入；(Spring4 整合 Hibernate4 的时候顺带讲)

- 注入参数

  - 基本类型值；

  - 注入 bean；

  - 内部 bean；

    ```xml
    <bean id="people3" class="com.java1234.entity.People">
    	<property name="id" value="1"></property>
    	<property name="name" value="张三"></property>
    	<property name="age" value="11"></property>
    	<property name="dog">
    		<bean class="com.java1234.entity.Dog">
    			<property name="name" value="Tom"></property>
    		</bean>
    	</property>
    </bean>
    ```

  - null 值；

    ```xml
    <bean id="people4" class="com.java1234.entity.People">
    	<property name="id" value="1"></property>
    	<property name="name" value="张三"></property>
    	<property name="age" value="11"></property>
    	<property name="dog">
    		<null></null>
    	</property>
    </bean>
    ```

  - 级联属性；（需要dog类有set方法）

    ```xml
    <bean id="people5" class="com.java1234.entity.People">
    	<property name="id" value="1"/>
    	<property name="name" value="张三"/>
    	<property name="age" value="11"/>
    	<property name="dog.name" value="Jack2"/>
    </bean>
    ```

  - 集合类型属性； 

    ```xml
    <bean id="people6" class="com.java1234.entity.People">
    	<property name="id" value="1"></property>
    	<property name="name" value="张三"></property>
    	<property name="age" value="11"></property>
    	<property name="dog" ref="dog1"></property>
    	<property name="hobbies">
    		<list>
    			<value>唱歌</value>
    			<value>跳舞</value>
    		</list>
    	</property>
    	<property name="loves">
    		<set>
    			<value>唱歌2</value>
    			<value>跳舞2</value>
    		</set>
    	</property>
    	<property name="works">
    		<map>
    			<entry>
    				<key><value>上午</value></key>
    				<value>写代码</value>
    			</entry>
    			<entry>
    				<key><value>下午</value></key>
    				<value>测试代码</value>
    			</entry>
    		</map>
    	</property>
    	<property name="addresses">
    		<props>
    			<prop key="address1">aaaaa</prop>
    			<prop key="address2">bbbbb</prop>
    		</props>
    	</property>
    </bean>
    ```

- Spring 自动装配 （配置 default-autowire 属性 ）

  - byName：通过名称进行自动匹配；

  - byType：根据类型进行自动匹配；

  - constructor：和 byType 类似，只不过它是根据构造方法注入而言的，根据类型，自动注入； 

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd"
            default-autowire="constructor">
    	
    	<bean id="dog2" class="com.java1234.entity.Dog">
    		<property name="name" value="Jack"></property>
    	</bean>
    	
    	<bean id="people1" class="com.java1234.entity.People">
    		<property name="id" value="1"></property>
    		<property name="name" value="张三"></property>
    		<property name="age" value="11"></property>
    	</bean>
    </beans>
    ```

- 方法注入 （lookup-method）

  ```xml
  <bean id="people1" class="com.java1234.entity.People">
    <property name="id" value="1"></property>
    <property name="name" value="张三"></property>
    <property name="age" value="11"></property>
    <lookup-method name="getDog" bean="dog"/>
  </bean>
  ```

- bean之间的关系

  - 继承（parent）

  - 依赖（depends-on）

    ```xml
    <bean id="abstractPeople" class="com.java1234.entity.People" abstract="true">
    	<property name="className" value="高三5班"/>
    	<property name="age" value="19"/>
    </bean>

    <bean id="zhangsan" parent="abstractPeople" depends-on="autority">
    	<property name="id" value="1"/>
    	<property name="name" value="张三"/>
    </bean>
    ```

  - 引用（ref）

    ```xml
    <bean id="dog1" class="com.java1234.entity.Dog">
    	<property name="name" value="Jack"></property>
    </bean>

    <bean id="people2" class="com.java1234.entity.People">
    	<property name="id" value="1"></property>
    	<property name="name" value="张三"></property>
    	<property name="age" value="11"></property>
    	<property name="dog" ref="dog1"></property>
    </bean>
    ```

- bean作用范围

  - 默认singleton （单例）

  - prototype ：每次返回一个新的实例

  - request：每次http请求都会创建一个新的实例

  - session 同一个 HTTP Session 共享一个 Bean；

  - global session 同一个全局 Session 共享一个 Bean，一般用于 Portlet 应用环境；

  - application 同一个 Application 共享一个 Bean； 

    ```xml
    <bean id="dog" class="com.java1234.entity.Dog" scope="singleton">
    	<property name="name" value="jack"></property>
    </bean>
    ```

#### AOP 面向切面 

- 定义一个类为aop(也可以叫做事务通知)

  - 定义切点（即寻找需要切入的方法）

  - 定义通知方式（即这个类中的一个方法），引用切点

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd">
    	
    	<bean id="studentServiceAspect" class="com.java1234.advice.StudentServiceAspect"></bean>

    	<aop:config>
    		<aop:aspect id="studentServiceAspect" ref="studentServiceAspect">
    			<aop:pointcut expression="execution(* com.java1234.service.*.*(..))" id="businessService"/>
    			<aop:before method="doBefore" pointcut-ref="businessService"/>
    			<aop:after method="doAfter" pointcut-ref="businessService"/>
    			<aop:around method="doAround" pointcut-ref="businessService"/>
    			<aop:after-returning method="doAfterReturning" pointcut-ref="businessService"/>
    			<aop:after-throwing method="doAfterThrowing" pointcut-ref="businessService" throwing="ex"/>
    		</aop:aspect> 
    	</aop:config>
    </beans>
    ```

- 通知类型

  - 前置通知；
  - 后置通知；
  - 环绕通知；
  - 返回通知；
  - 异常通知； 

#### DAO支持

- 使用Apache dbcp的数据库连接池

  > 现在常用的开源数据连接池主要有c3p0、dbcp和proxool三种
  >
  > hibernate开发组推荐使用c3p0; 
  > spring开发组推荐使用dbcp(dbcp连接池有weblogic连接池同样的问题，就是强行关闭连接或数据库重启后，无法reconnect，告诉连接被重置，这个设置可以解决); 
  > hibernate in action推荐使用c3p0和proxool;

  ```xml

  <bean id="dataSource2" destroy-method="close" class="org.apache.commons.dbcp.BasicDataSource">
  	<property name="driverClassName" value="${jdbc.driverClassName}"/>
  	<property name="url" value="${jdbc.url}"/>
  	<property name="username" value="${jdbc.username}"/>
  	<property name="password" value="${jdbc.password}"/>
  	
  	<property name="initialSize" value="5"/>
  	
  	<property name="maxActive" value="30"/>
  	
  	<property name="maxIdle" value="20"/>
  	
  	<property name="minIdle" value="3"/>
  	  
  	<property name="removeAbandoned" value="true" />
  	  
  	<property name="removeAbandonedTimeout" value="200"/>
  	 
  	<property name="logAbandoned" value="true"/>
  	  
  	<property name="maxWait" value="100"/>  
  </bean>


  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
  	<property name="jdbcUrl" value="${jdbc.url}" />
  	<property name="driverClass" value="${jdbc.driverClassName}" />
  	<property name="user" value="${jdbc.username}" />
  	<property name="password" value="${jdbc.password}" />
  	
  	<property name="maxPoolSize" value="100" />
  	
  	<property name="minPoolSize" value="1" />
  	
  	<property name="initialPoolSize" value="10" />
  	
  	<property name="maxIdleTime" value="30" />
  	
  	<property name="acquireIncrement" value="5" />
  	
  	<property name="maxStatements" value="0" />

  	
  	<property name="idleConnectionTestPeriod" value="60" />

  	
  	<property name="acquireRetryAttempts" value="30" />

  	
  	<property name="breakAfterAcquireFailure" value="true" />

  	
  	<property name="testConnectionOnCheckout"  value="false" />        
  </bean>
  ```

- 配置完连接池

  - 使用jbdcTemlate

    - 配置xml

      ```xml
      <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
      	<property name="dataSource" ref="dataSource"></property>
      </bean>

      <bean id="studentDao" class="com.java1234.dao.impl.StudentDaoImpl">
      	<property name="jdbcTemplate" ref="jdbcTemplate"></property>
      </bean> 

      <bean id="studentService" class="com.java1234.service.impl.StudentServiceImpl">
      	<property name="studentDao" ref="studentDao"></property>
      </bean> 
      ```

    - dao实现

      ```java
      //增删改
      String sql="delete from t_student where id=?";
      Object []params=new Object[]{id};
      return jdbcTemplate.update(sql,params);

      //查
      public List<Student> findStudents() {
      	String sql="select * from t_student";
      	final List<Student> studentList=new ArrayList<Student>();
      	jdbcTemplate.query(sql, new RowCallbackHandler(){

      		@Override
      		public void processRow(ResultSet rs) throws SQLException {
      			Student student=new Student();
      			student.setId(rs.getInt("id"));
      			student.setName(rs.getString("name"));
      			student.setAge(rs.getInt("age"));
      			studentList.add(student);
      		}
      		
      	});
      	return studentList;
      }
      ```

  - 使用NamedParameterJdbcTemplate，支持属性名称

    - 配置xml，以构造注入

      ```xml
      <bean id="namedParameterJdbcTemplate" class="org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate">
          	<constructor-arg ref="dataSource"></constructor-arg></bean>
      ```

    - dao实现

      ```java
      public int addStudent(Student student) {
      	String sql="insert into t_student values(null,:name,:age)";
      	MapSqlParameterSource sps=new MapSqlParameterSource();
      	sps.addValue("name", student.getName());
      	sps.addValue("age", student.getAge());
      	return namedParameterJdbcTemplate.update(sql,sps);
      }

      public List<Student> findStudents() {
      	String sql="select * from t_student";
      	final List<Student> studentList=new ArrayList<Student>();
      	namedParameterJdbcTemplate.query(sql, new RowCallbackHandler(){

      		@Override
      		public void processRow(ResultSet rs) throws SQLException {
      			Student student=new Student();
      			student.setId(rs.getInt("id"));
      			student.setName(rs.getString("name"));
      			student.setAge(rs.getInt("age"));
      			studentList.add(student);
      		}
      		
      	});
      	return studentList;
      }
      ```

#### 对jdbc支持

- dao实现类继承于JdbcDaoSupport

  - JdbcDaoSupport里面包含jdbcTemplate（所以dao类中的jdbcTemplate可以删除）

    ```java
    //private JdbcTemplate jdbcTemplate;

    /*public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
    	this.jdbcTemplate = jdbcTemplate;
    }*/
    ```

  - JdbcDaoSupport需要被注入dataSource，修改配置文件

    ```xml


    <bean id="studentDao" class="com.java1234.dao.impl.StudentDaoImpl">
    	<property name="dataSource" ref="dataSource"></property>
    </bean> 

    <bean id="studentService" class="com.java1234.service.impl.StudentServiceImpl">
    	<property name="studentDao" ref="studentDao"></property>
    ```

#### 对事务的支持

- 编程式事务管理

  - Spring 提供的事务模版类：org.springframework.transaction.support.TransactionTemplate

  - 事务管理器：org.springframework.jdbc.datasource.DataSourceTransactionManager

  - xml配置

    ```xml

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    	<property name="dataSource" ref="dataSource"></property>
    </bean>

    <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
    	<property name="transactionManager" ref="transactionManager"></property>
    </bean>

    <bean id="bankService" class="com.java1234.service.impl.BankServiceImpl">
    	<property name="bankDao" ref="bankDao"></property>
    	<property name="transactionTemplate" ref="transactionTemplate"></property>
    </bean> 
    ```

  - service层增加事务实现

    ```java
    private TransactionTemplate transactionTemplate;

    public void transferAccounts(final int count, final int userIdA, final int userIdB) {
    	// TODO Auto-generated method stub
    	transactionTemplate.execute(new TransactionCallbackWithoutResult() {
    		
    		@Override
    		protected void doInTransactionWithoutResult(TransactionStatus arg0) {
    			// TODO Auto-generated method stub
    			bankDao.outMoney(count, userIdA);
    			bankDao.inMoney(count, userIdB);				
    		}
    	});
    }
    ```

- 声明式事务管理（减少事务管理侵入业务代码）

  - 使用xml配置声明式事务

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:tx="http://www.springframework.org/schema/tx"
        xsi:schemaLocation="http://www.springframework.org/schema/tx
            http://www.springframework.org/schema/tx/spring-tx.xsd">
            
    	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
            <property name="driverClassName" value="${jdbc.driverClassName}"/>
            <property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
        </bean>
      
      	<context:property-placeholder location="jdbc.properties"/>
        
        
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        	<property name="dataSource" ref="dataSource"></property>
        </bean>
        
        
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
        	<tx:attributes>  
                <tx:method name="insert*" propagation="REQUIRED" />  
                <tx:method name="update*" propagation="REQUIRED" />  
                <tx:method name="edit*" propagation="REQUIRED" />  
                <tx:method name="save*" propagation="REQUIRED" />  
                <tx:method name="add*" propagation="REQUIRED" />  
                <tx:method name="new*" propagation="REQUIRED" />  
                <tx:method name="set*" propagation="REQUIRED" />  
                <tx:method name="remove*" propagation="REQUIRED" />  
                <tx:method name="delete*" propagation="REQUIRED" />  
                <tx:method name="change*" propagation="REQUIRED" />  
                <tx:method name="get*" propagation="REQUIRED" read-only="true" />  
                <tx:method name="find*" propagation="REQUIRED" read-only="true" />  
                <tx:method name="load*" propagation="REQUIRED" read-only="true" />  
                <tx:method name="*" propagation="REQUIRED" read-only="true" />  
            </tx:attributes>  
        </tx:advice>
        
        
        <aop:config>
        	
        	<aop:pointcut id="serviceMethod" expression="execution(* com.java1234.service.*.*(..))" />
        	
        	<aop:advisor advice-ref="txAdvice" pointcut-ref="serviceMethod"/>
        </aop:config>
    </beans>
    ```

  - 使用注解配置声明式事务（@Transactional）

    ```xml

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    	<property name="dataSource" ref="dataSource"></property>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>
    ```


#### 使用Spring

- java代码直接引用

  - 配置

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">

    	<bean id="helloWorld" class="com.java1234.test.HelloWorld"></bean>

      
    </beans>
    ```

  - 代码

      ```java
      public static void main(String[] args) {
      	ApplicationContext ac=new ClassPathXmlApplicationContext("beans.xml");
      	HelloWorld helloWorld=(HelloWorld)ac.getBean("helloWorld");
      	helloWorld.say();
      }
      ```


- web.xml

  ```xml
    
  <context-param>  
  	<param-name>contextConfigLocation</param-name>  
  	<param-value>classpath:applicationContext.xml</param-value>  
  </context-param>  


  <listener>  
  	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>  
  </listener> 
  ```
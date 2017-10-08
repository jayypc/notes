# java

#### 打印数组

```java
Arrays.toString(arr);
//利用这个方法可以打印List
List a = new ArrayList<>();
Arrays.toString(a.toArray());
```

#### 判断是否是数字的正则表达式

```java
public static boolean isNumeric(String tester) {
        boolean isNumeric=false;
        if(Validators.isNotnull(tester) && Pattern.matches("^(-)?\\d+$", tester)) {
            isNumeric = true;
        }
        return isNumeric;
}
/*
^  代表第一个位置
(-)代表负号, 要匹配圆括号字符，请使用“\(”或“\)”
?  前面的也就是(-)出现零次或者一次
\  在java中代表转义字符,所以要得到正则表达式的\d(代表一个数字),需要\\才能够得到\,然后加个d
+  代表前面的表达式即\d可以出现一次或者多次
$  代表最后一个位置
*/
```

#### Hibernate

**getSession()**

- getSession()只要继承 sessionFactory
- getSession()方法，spring会把最原始的session给你，在使用完之后必须自己调用相应的close方法，而且也不会对声明式事务进行相应的管理，一旦没有及时关闭连接，就会导致数据库连接池的连接数溢出

**getHibernateTemplate()**

- getHibernateTemplate()方法必须继承HibernateDaoSupport

- getHibernateTemplate()方法是经过spring封装的，例如添加相应的声明式事务管理，由spring管理相应的连接。

- HibernateTemplate 提供非常多的常用方法来完成基本的操作，比如通常的增加、删除、修改、查询等操作

  - q void delete(Object entity)：删除指定持久化实例 
  - q deleteAll(Collection entities)：删除集合内全部持久化类实例 
  - q find(String queryString)：根据HQL查询字符串来返回实例集合 
  - q findByNamedQuery(String queryName)：根据命名查询返回实例集合 
  - q get(Class entityClass, Serializable id)：根据主键加载特定持久化类的实例 
  - q save(Object entity)：保存新的实例 
  - q saveOrUpdate(Object entity)：根据实例状态，选择保存或者更新 
  - q update(Object entity)：更新实例的状态，要求entity是持久状态 
  - q setMaxResults(int maxResults)：设置分页的大小

- 有些方法在getHibernateTemplate()并没有提供，这时我们用HibernateCallback 回调的方法管理数据库

  ```java
  //execute是最基础的
  //<Object> Object HibernateTemplate.execute(HibernateCallback<Object> action) throws DataAccessException
  public VulScannerInfo getScannerInfoByScannerId(final Long scannerId) {
    return (VulScannerInfo) getHibernateTemplate().execute(new HibernateCallback<Object>(){
      public Object doInHibernate(Session session)
        throws HibernateException, SQLException {

        //扫描器ID为空，则返回空
        if(scannerId == null){
          return null;
        }

        String hql = "from VulScannerInfo where scannerid = :scannerid and deleted = 0";
  	  //使用hql调用createQuery()方法
        Query query = session.createQuery(hql);
        query.setParameter("scannerid", scannerId);
  	  //确定返回的实例只有一个或者null时 用uniqueResult()方法
        return query.uniqueResult();
      }

    });
  }

  //excuteFind就是execute的结果集判断了一下:HibernateCallback 类型的action必须返回list
  public List<VulRiskTaskResultDetail> getVulTaskResultDetails(final FilterChain chain) {
    return getHibernateTemplate().executeFind(new HibernateCallback<Object>(){
      public Object doInHibernate(Session session) throws HibernateException, SQLException {

        String sql = "select  distinct {t.*} from vul_risk_task_result_detail t ";
        sql += "left join vul_risk_task_detail_history history on history.detailid = t.detailid ";
        if(chain != null){
          sql += chain.toSql();
        }
        //使用sql调用createSQLQuery()方法
        SQLQuery query = session.createSQLQuery(sql);
        setQueryParameter(query, chain);
        query.addEntity("t",VulRiskTaskResultDetail.class);
  	  //查询返回多个值用list()方法
        return query.list();
      }

    });
  }

  //或者使用?来做占位符
  public void paySuccess(Long orderNo) {
    String sql = "update Order  set  status =1  ,  payTime =?  where orderNo =? ";
    getCurrentSession().createQuery(sql).setTimestamp(0 ,new Date()).setLong(1, orderNo).executeUpdate();
  }

  //in语句中可以使用setParameterList()方法
  public Integer updateStatusDiscard(List<Long> rids) {
    String sql = "UPDATE requirement r SET r.status=0,r.updateTime=NOW() WHERE r.id IN (:rids)";
    return getCurrentSession().createSQLQuery(sql).setParameterList("rids", rids).executeUpdate();
  }
  ```

#### struts1的转发

```java
//1.页面
href="transfer.do"
//2.struts-config.xml
<action input="/index.jsp" path="/transfer" scope="request"
			type="org.springframework.web.struts.DelegatingActionProxy">
			 <forward name="main" path="/WEB-INF/MyJsp.jsp" />
</action>
//3.java文件
//返回MyJsp.jsp
return mapping.findForward("main");
//返回index.jsp
return mapping.getInputForward()
```

#### 时间处理

```java
//当前时间
Calendar calendar = Calendar.getInstance();
Date now = new Date();
//互相转换
calendar.setTime(now);
now = calendar.getTime();
//时间与字符串
SimpleDateFormat sf = new SimpleDateFormat("yyyyMMdd");
String date = sf.format(new Date());

String year = calendar.get(Calendar.YEAR);
String month = calendar.get(Calendar.MONTH)+1; //get的返回值为int
String day = calendar.get(Calendar.DATE);
String weekday = calendar.get(Calendar.DAY_OF_WEEK);//可以设置从哪一天开始 默认星期日 返回1~7
//时间变化
calendar.add( Calendar.MONTH,  +1 ) ;
calendar.add( Calendar.MONTH,  -1 ) ;
//设置周一
calendar.setFirstDayOfWeek(Calendar.MONDAY);
```

#### 输出printStackTrace()堆栈信息

- 简单办法，需要用到log4j

```java
log.error(e.getMessage(),e); 
```

- 通用办法，使用printStackTrace()的另一个构造方法

```java
StringWriter sw = new StringWriter();
e.printStackTrace(new PrintWriter(sw, true));
String str = sw.toString();
logger.error ( "Exception : " + str ) ;
```

#### XML解析与构造

```java
//解析
Element element;  
element.asXML(); //result:<info><Account>33</Account><CityCd>44</CityCd></info>
if(element != null){
  if(element.selectSingleNode("Account") != null){
    account = element.selectSingleNode("Account").getText();
  }
  if(element.selectSingleNode("CityCd") != null){
    cityCd = element.selectSingleNode("CityCd").getText();
  }
}

Element paramElement;   //<Account>33</Account> 仅有一个节点
paramElement.getText(); //result: 33

//构造
Element result = root.addElement("result");
result.setText("1");
```

#### try catch finally

- 即使catch里面有return也会执行finally
- 一般finally写的代码都是流关闭语句
- 如果没有执行try语句之前就return了或者发生异常，则不会进入finally

#### session

- request.setAttribute(position, nameOfObj)不同页面间传值使用时，只会从a.jsp到b.jsp一次传递，之后这个request就会失去它的作用范围
- session.setAttribute()会在一个过程中始终保有这个值。

```java
public LoginResult check(CheckInfo checkInfo,HttpServletRequest request){
    HttpSession session = request.getSession();
    session.setAttribute("loginUser", sysUser);
}

public String getStructure(HttpServletRequest request, Map<String, Object> map) {
    HttpSession session = request.getSession();
    sysUser = (SysUser) request.getAttribute("loginUser");
}
```

#### fastjson

- 前端数据格式为json，且后端使用fastJson的时候可以使用反序列注解，对实体类的时间格式进行格式化

```java
@JSONField (format="yyyy-MM-dd HH:mm:ss")  
private Date ctime;
```
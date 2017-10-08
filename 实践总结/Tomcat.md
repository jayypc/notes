# Tomcat

#### 加入密码

tomcat-users.xml

```xml
<tomcat-users></tomcat-users>之间加上这一句
<user name="admin" password="admin123" roles="manager-gui"/>
```

#### 服务端支持utf-8

server.xml

```xml
<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="UTF-8"/>
```
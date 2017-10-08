# myeclipse 

#### 新建wsdl

右键
-> new -> other -> web services -> Web Service Client ->粘贴wsdl地址 ->选择要生成的地址
-> 删除多余文件

#### 增强补全功能

Eclipse -> Window -> Perferences -> Java -> Editor -> Content Assist下

**Auto activation triggers for Java**里的.改为

```
".abcdefghijklmnopqrstuvwsyzABCDEFGHIJKLMNOPQRSTUVWSYZ_"
```

如果版本比较低，不能直接修改的话，就导出配置文件，然后修改配置文件，最后再导入配置文件。

#### myeclipse低版本去除空格和=号补全

- 查看myeclipse对应eclipse版本

  安装目录MyEclipse 6.6/eclipse/readme下的readme_eclipse.html中的版本号

- 下载eclipse源码

  http://archive.eclipse.org/eclipse/downloads/

- 修改org.eclipse.jface.text包替换到eclipse/plugins

  - src/org/eclipse/jface/text/contentassist/CompletionProposalPopup.java

  ```java
  char[] triggers = t.getTriggerCharacter(); 
  if (contains(triggers, key))
  改为
  if(key != '=' && key != 0x20 &&contains(triggers,key)){ .........}
  ```


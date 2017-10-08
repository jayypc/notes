# Jquery

#### 回车键登陆

```javascript
document.onkeydown = function(e) {
    var ev = document.all ? window.event : e;
     if(ev.keyCode==13) {
          if (!$('#btsubmit').prop('disabled'))
                loginsubmit();
     }
}
```

#### prop attr css

```javascript
$('#btsubmit').prop('disabled')			//判断元素是否disabled，是的话返回true

$('#btsubmit').attr('disabled',false);	//设置元素的属性值

$('#errorinfo').css('display','none');	//设置元素的CSS属性值
$("p").css("background-color");			//返回元素的CSS属性值
```

#### animated重复动画

```javascript
$('#errorinfo').addClass( 'animated shake').one(
    'webkitAnimationEnd mozAnimationEnd MSAnimationEnd oanimationend animationend', function() {
        $(this ).removeClass('animated shake');
});
//.one用来判断动画结束
```

#### 实时监控文本框输入值

- oninput 是 HTML5 的标准事件，检测 textarea, input:text, input:password 和 input:search 发生的内容变化，在内容修改后立即被触发
- onchange 事件需要失去焦点才触发。
- oninput 事件在 IE9 以下版本不支持，需要使用 IE 特有的 onpropertychange 事件替代

```html
<head>
    <script type="text/javascript">
    // Firefox, Google Chrome, Opera, Safari, Internet Explorer from version 9
        function OnInput (event) {
            alert ("The new content: " + event.target.value);
        }
    // Internet Explorer
        function OnPropChanged (event) {
            if (event.propertyName.toLowerCase () == "value") {
                alert ("The new content: " + event.srcElement.value);
            }
        }
    </script>
</head>
<body>
    Please modify the contents of the text field.
    <input type="text" oninput="OnInput (event)" onpropertychange="OnPropChanged (event)" value="Text field" />
</body>
```

- 使用jquery库的写法

```javascript
$('textarea').bind('oninput propertychange', function() {
    $('.msg').html($(this).val().length + ' characters');
});
```

#### json和xml解析与转换

- JSON

  - json直接使用.解析：data.logincode == '1'


-   json对象转换为字符串 JSON.stringify(data);
  - 字符串转换为json对象 JSON.parse(jsonstr);

- xml

  - 解析：var account = $(data).find('Account').text();

  - xml对象转字符串

    ```javascript
    (new XMLSerializer()).serializeToString(xmlData); //火狐
    xmlData.xml;  //IE
    ```

  - 字符串转xml对象

    ```javascript
    //FF   
     if (document.implementation.createDocument) {   
             var parser = new DOMParser()   
             xmlDoc = parser.parseFromString(xmlString, "application/xml")   
    //IE   
     } else if (window.ActiveXObject) {   
             xmlDoc = new ActiveXObject("Microsoft.XMLDOM")   
             xmlDoc.async="false"  
             xmlDoc.loadXML(xmlString)   
     } 
    ```

#### 遍历来实现批量修改

```javascript
$("#strategySet").find("select").each(function (i){
    var DomId =  $(this).attr("name");
}
```

#### 字符串空格处理

```javascript
str=$.trim(str);
```

#### append() 和 prepend()

```javascript
$("p").append(" <b>Hello world!</b>");   //在被选元素的后面插入指定内容
$("p").prepend("<b>Hello world!</b>");	 //在被选元素的开头插入指定内容
```
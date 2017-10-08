# css

#### 两个DIV并排，且第一个居中

```html
<style type="text/css">
    * {margin:0px; padding:0px;}
    .box {position:absolute; top:50%; left:50%; width:200px; height:100px; margin-left:-50px; margin-top:-50px;}
    .div1 {width:100px; height:100px; background:#ccc; float:left;}
</style>
</head>
<body>
    <div>
        <div ></div>
        <div ></div>
    </div>
</body>
```

#### 判断手机方向

```css
/*判断是否竖屏*/
@media screen and (orientation:portrait) {
    .speedcenter{width:100%}
    .mspeed{left:50%}
}
/*判断是否横屏*/
@media screen and (orientation:landscape) {
    .speedcenter{width:50%}
    .mspeed{left:65%;top:50%}
}
```

#### CSS属性选择器

- 简易属性选择器

  ```css
  h1[class]{color:silver;}			/*作用于任何带class的h1元素*/
  img[alt]{color:silver;}				/*作用于任何带有alt属性的img元素*/
  a[href][title]{font-weight:bold;}	/*作用于同时带href和title属性的a元素*/
  ```

- 精确属性值选择器

  ```css
  h1#logo 等同于 h1[id="logo"]  /*不同于简易选择器，在其基础上添加更精准信息*/
  a[title="W3CHome"]{font-size:200%;} 将会作用于 <a href="http://www.mb5u.com/" title="mb5uHome"></a>
  ```

- 部分属性值选择器

  ```css
  <p>Whenhandlingplutonium,oidtheformationofacriticalmass.</p> 
  p[class~="warning"]{font-weight:bold;}  和p[class~="urgent"]{font-weight:bold;} 都会匹配上面的p
  /*比如你要样式化插图，其title中都含字符串”Figure”，如title="Figure5"，则你可以使用img[title~="Figure"]。*/
  ```

- 非凡属性选择器

  ```css
  [lang|="en"]{color:white;}
  这条规则将会选择属性lang的值en或者en-打头的元素
  可以匹配<h1>Hello!</h1>、<p>Greetings!</p>和<div>G'day!</div>
  而不匹配<p>Bonjour!</p>和<h3>Jrooana!</h3>。
  ```
# Spring MVC

- @RequestMapping 请求映射

- @RequestParam 请求参数

- ModelAndView 返回模型和视图

- SpringMVC 对象属性自动封装

- SpringMVC POST 请求乱码解决

- Controller 内部转发和重定向 

  ```java
  //重定向不带参数，同时会改变浏览器地址
  return "redirect:/student/list.do";
  //内部转发带参数，浏览器地址不会改变
  return "forward:/student/list.do";

  //原生servlet重定向和转发
  response.sendRedirect(request.getContextPath() + "/student_list.jsp");  
  request.getRequestDispatcher("/student_list.jsp").forward(request, response);  
  ```
## SSM-CRM开发过程知识总结

#### **SSM获取前台参数的方式**

##### 1.直接把表单的参数写在Controller相应的方法的形参中，适用于get方式提交，不适用于post方式提交。

##### 2.通过HttpServletRequest接收，post方式和get方式都可以

##### 3.通过一个bean来接收,post方式和get方式都可以。

##### 4.使用@ModelAttribute注解获取POST请求的FORM表单数据

##### 5.用注解@RequestParam绑定请求参数到方法入参  

##### 6.用request.getQueryString() 获取spring MVC get请求的参数，只适用get请求


## SpringMVC的错误处理

SpringMVC服务器验证一种是有两种方式,一种是基于Validator接口,一种是使用Annotaion JSR-303标准的验证



我们先学习第一种;



### 1.定义一个student类

```
public class Student {
    private Integer age;
    private String name;
    private Integer id;

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }
}
```

### 创建StudentValidator

```
import com.anlu.springmvc.model.Student;
import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

public class StudentValidator implements Validator {

    public boolean supports(Class<?> clazz) {
        return Student.class.isAssignableFrom(clazz);
    }


    public void validate(Object target, Errors errors) {

        Student student = (Student) target;

        ValidationUtils.rejectIfEmptyOrWhitespace(errors,
                "name", "required.name","名字不能为空");

        //此方法可以加四个参数,第一个表单域field,
        //区分是哪个表单出错,第二个errorCode错误码,
        //第三个制定了资源文件中占位符,第四个具体错误返回信息
        //简写版可以把2,3参数去掉


        if(student!=null){
            if(student.getAge()==null){
                errors.rejectValue("age",null,null,"age is null");
                return;
            }

            if(student.getAge().toString().length()<1||student.getAge().toString().length()>3){
                errors.rejectValue("age",null,null,"请问你是个妖怪吗?");
            }
        }

    }
}

```

validate这个方法是自定错误提示，可以判断对象的属性是否合格，如果不合格可以加入到errors中，前台进行提示/



### Controller

在控制器中，声明这个initBinder方法

```
  @InitBinder
    private void initBinder(WebDataBinder binder) {
        binder.replaceValidators(new StudentValidator());
    }
```

跳转到页面的方法

```
    @RequestMapping(value = "/student", method = RequestMethod.GET)
    public ModelAndView student() {
        return new ModelAndView("form", "student", new Student());
    }

```

进行验证的方法

```
   @RequestMapping(value = "/addStudentValidator", method = RequestMethod.POST)
    public String addStudentValidator(@ModelAttribute("student")@Validated Student student, BindingResult result, ModelMap model){
      if(result.hasErrors()){
          return "form";
      }
        model.addAttribute("name", student.getName());
        model.addAttribute("age", student.getAge());
        model.addAttribute("id", student.getId());

        return "result";
    }
```



### JSP页面

一个表单页面，form.jsp页面 如下:

```
<%--
  Created by IntelliJ IDEA.
  User: Anlu
  Date: 2018/2/26
  Time: 14:38
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<html>
<head>
    <title>Spring MVC表单处理</title>
    <style>
        .error {
            color: #ff0000;
        }

        .errorStyle {
            color: #000;
            background-color: #ffEEEE;
            border: 3px solid #ff0000;
            padding: 8px;
            margin: 16px;
        }
    </style>
</head>
<body>

<h2>Student Information</h2>
<form:form method="POST" action="/FormHandling/addStudentValidator"
           commandName="student">
    <form:errors path="*" cssClass="errorStyle" element="div" />
    <table>
        <tr>
            <td><form:label path="name">姓名：</form:label></td>
            <td><form:input path="name" /></td>
            <td><form:errors path="name" cssClass="error" /></td>
        </tr>
        <tr>
            <td><form:label path="age">年龄：</form:label></td>
            <td><form:input path="age" /></td>
            <td><form:errors path="age" cssClass="error" /></td>
        </tr>
        <tr>
            <td><form:label path="id">编号：</form:label></td>
            <td><form:input path="id" /></td>
        </tr>
        <tr>
            <td colspan="2"><input type="submit" value="提交" /></td>
        </tr>
    </table>
</form:form>
</body>
</html>

```



最终效果：

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180226161316.png)



### 代码下载

[code](https://github.com/SpringMVCAndMybatis2018/SpringMVC_HelloWorld/archive/master.zip)


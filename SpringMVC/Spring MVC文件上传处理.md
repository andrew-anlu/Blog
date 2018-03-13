## SpringMVC的文件上传





## 首先创建一个实体类

```
public class FileModel {
    private MultipartFile file;

    public MultipartFile getFile() {
        return file;
    }

    public void setFile(MultipartFile file) {
        this.file = file;
    }
}
```

### Controller

**FileUploadController.java** 的代码如下所示 -

```

@Controller
public class FileUploadController {

    @RequestMapping(value = "/fileUploadPage", method = RequestMethod.GET)
    public ModelAndView fileUploadPage() {
        FileModel file = new FileModel();
        ModelAndView modelAndView = new ModelAndView("fileUpload", "fileUpload", file);
        return modelAndView;
    }
    @RequestMapping(value="/fileUploadPage", method = RequestMethod.POST)
    public String fileUpload(FileModel file, BindingResult result, ModelMap model)throws Exception {
        try
        {
            if(result.hasErrors()){
                System.out.println("validation error");
                return "fileuploadPage";
            }else{
                MultipartFile multipartFile = file.getFile();
                String uploadPath = "F:\\upload\\"+ File.separator + "temp" + File.separator;
                //Now do something with file...
                FileCopyUtils.copy(file.getFile().getBytes(), new File(uploadPath+file.getFile().getOriginalFilename()));
                String fileName = multipartFile.getOriginalFilename();
                model.addAttribute("fileName", fileName);
                return "success";
            }
        }catch (Exception e){
            e.printStackTrace();
        }
        return "fileuploadPage";
    }

```

### jsp页面



```
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<html>
<head>
<title>Spring MVC上传文件示例</title>
</head>
<body>
    <form:form method="POST" modelAttribute="fileUpload"
        enctype="multipart/form-data">
      请选择一个文件上传 : 
      <input type="file" name="file" />
        <input type="submit" value="提交上传" />
    </form:form>
</body>
</html>
```

这里使用带有`value =“fileUpload”`的`modelAttribute`属性来映射文件用服务器模型上传控件。



**success.jsp** 的代码如下所示 -

```
<%@ page contentType="text/html; charset=UTF-8"%>
<html>
<head>
<title>Spring MVC上传文件示例</title>
</head>
<body>
    文件名称 :
    <b> ${fileName} </b> - 上传成功！
</body>
</html>
```



### 代码下载

[代码](https://github.com/SpringMVCAndMybatis2018/SpringMVC_HelloWorld/archive/master.zip)


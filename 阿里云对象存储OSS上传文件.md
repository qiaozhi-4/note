# 阿里云对象存储OSS上传文件



[OSS官方上手视频](https://help.aliyun.com/learn/learningpath/oss.html?spm=5176.8465980.guide.1.4e701450vjoYZf)

[文档](https://help.aliyun.com/product/31815.html)



## 前端代码:

>element-ui

```html
<el-upload
           action="/aliyun"
           list-type="picture-card"
           :on-preview="handlePictureCardPreview"
           :on-remove="handleRemove"
           name="file">
    <i class="el-icon-plus"></i>
</el-upload>
```

## 后端代码:

>springboot项目

==依赖支持==

```xml
<!-- https://mvnrepository.com/artifact/com.aliyun.oss/aliyun-sdk-oss -->
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.15.1</version>
</dependency>
```



**AccessKey具体查看方式**

![firefox_T3zwVu4HgC](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/firefox_T3zwVu4HgC.gif)



**bucketName具体查看方式**

![firefox_nlTWzRfrO1](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/firefox_nlTWzRfrO1.gif)



**查看文件路径URL**

![firefox_LASeXpt3d0](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/firefox_LASeXpt3d0.gif)



代码:

```java
    @RequestMapping(value = "aliyun", produces = "text/plain;charset=utf-8")
    @ResponseBody
    public String aliyun(MultipartFile file) throws IOException {
        // Endpoint填写为https://oss-cn-chengdu.aliyuncs.com。
        String endpoint = "https://oss-cn-shenzhen.aliyuncs.com";
        // 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
        String accessKeyId = "xxxxx";
        String accessKeySecret = "xxxxx";
        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);
        //调用oss实现上传第一个参数bucket名称  第二个参数文件夹路径+文件名称  第三个参数输入流
        String bucketName = "yyb-note-images";
        ossClient.putObject(bucketName, "img/Test/" + file.getOriginalFilename(), file.getInputStream());
        // 关闭OSSClient。
        ossClient.shutdown();
        //返回组成的文件url [注意文件夹路径！！！]
        String photoUrl = "https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Test/" + file.getOriginalFilename();
        System.out.println(photoUrl);
        return "photoUrl";
    }
```


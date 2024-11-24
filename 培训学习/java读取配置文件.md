###### 读取Properties文件

```java
//使用properties读取properties配置文件
Properties properties = new Properties();
//获取读取配置文件流
InputStream input = Main02.class.getResourceAsStream("/com/jsp10_2022_04_09/student.properties");
//加载流【字节流转字符流告诉他原本的编码格式】
properties.load(new InputStreamReader(input, "GBK"));
//关闭流
input.close();
//获取所有的键
Set<String> keys = properties.stringPropertyNames();
for (String key : keys) {
    //通过键获取值
    String value = properties.getProperty(key);
    System.out.printf("%s=%s\n", key, value);
}
```



###### 读取xml文件

```java
//使用java解析XMl文件，两种方式DOM、SAX
//获取DOM工厂
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
//创建一个DOM构建器
DocumentBuilder builder = factory.newDocumentBuilder();
//解析XML文档
Document document = builder.parse(Main02.class.getResourceAsStream("/com/jsp10_2022_04_09/sql.xml"));
//获取根节点，根节点保存了所有节点信息
Element element = document.getDocumentElement();
//获取所有子节点
NodeList childNodes = element.getChildNodes();
//获取所有sql的节点
NodeList sql = document.getElementsByTagName("sql");
for (int i = 0; i < sql.getLength(); i++) {
    //sql.item(i).getAttributes()【获取第i个节点并获取他的全部属性】
    NamedNodeMap attributes = sql.item(i).getAttributes();
    for (int j = 0; j < attributes.getLength(); j++) {
        //获取第j个属性
        Node attribute = attributes.item(j);
        //获取属性名
        System.out.println(attribute.getNodeName());
        //获取属性值
        System.out.println(attribute.getNodeValue());
    }
    //获取当前节点下的所有文本节点【.trim()去掉前后空白】
    String textContent = sql.item(i).getTextContent().trim();
    System.out.println(textContent);
}
```



###### 读取json文件

```java
//【XML01.class.getResourceAsStream("/com/file/Result.json") = 要解析的json文件】
//【new TypeReference<Result>() {} = 要解析为什么对象】
Result result =
    new ObjectMapper().readValue(JSON01.class.getResourceAsStream("/com/file/Result.json"),
                                 new TypeReference<Result>() {});
```


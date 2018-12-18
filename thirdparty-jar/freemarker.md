# freemarker 简介

是一个模板引擎

# java 代码使用

```java
Configuration configuration = new Configuration();
configuration.setDirectoryForTemplateLoading(templateFile);
configuration.setObjectWrapper(new DefaultObjectWrapper());
// 读取程序模板
Template temp = configuration.getTemplate("Message.ftl"); 

//import

List<String> codeImports = new ArrayList<>();
codeImports.add("com.alibaba.fastjson.JSON");
codeImports.add("com.alibaba.fastjson.JSONArray");
codeImports.add("com.alibaba.fastjson.JSONObject");
codeImports.add("java.util.List");
//把需要传入模板的参数都放进一个Map
HashMap<String, Object> root = new HashMap<>();
root.put("package", Const.basePackage);
root.put("className", messFile.getClassName());
root.put("fields", messFile.getMess().values());
root.put("prefix", messFile.getBeginMessId());
root.put("imports", codeImports);
Writer out = new StringWriter();

//把Map参数和一个Writer传入模板生成，
temp.process(root, out);

//就能得到生成出来的字符串
out.toString();
```

# 模板编写规则

## 循环

```xml
    <#list field.memo as comment>
     * ${comment}
    </#list>
```

## 选择

```xml
<#if cell.type=="int">
return jsonObject.getIntValue("${cell.field}");
<#elseif cell.type=="String">
return jsonObject.getString("${cell.field}");
<#else>
return jsonObject.getObject("${cell.field}",${cell.type}.class);
</#if>
```

## 首字母大写

```xml
${cell.field?cap_first}
```

?cap_first

## 是否等于某个字符串

```xml
<#if cell.type=="int">
```

## 等于某个字符串

```xml
<#if cell.field!="msgId">
```

## 传入参数是boolean的处理

```xml
<#if field.aMess>
```


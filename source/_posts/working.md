---
title: 工作中
categories: 技术分享    
---
# Java maven 打包后 不能读取resource目下的证书及其他文件：
原读取方式：FileReader.create(File, charset).readString();      

原因在于这种获取配置文件的方式无法获取压缩包中的配置文件   

解决方案以文件流方式读取：

```
  Resource resource = new ClassPathResource(wxPayV3Config.getKeyPath());
    InputStream is = resource.getStream();
    String originalKey = "";
    byte[] buf = new byte[1024];
    int len = 0;
    while ((len = is.read(buf)) != -1) {
      originalKey += new String(buf, 0, len);
    }
    is.close();

```


# Spring boot 整合consul 和feign不成功调用报错

spring boot  整合consul 和feign不成功，项目启动正常，也正常注册到consul，可是在请求时，报错找不到类
java.lang.ClassNotFoundException: com.netflix.config.CachedDynamicIntProperty

看样子是类冲突了  

解决方案：
```
<groupId>org.springframework.cloud</groupId> 
<artifactId>spring-cloud-starter-openfeign</artifactId> 
<exclusions> 
	<exclusion> 
		<groupId>com.netflix.archaius</groupId> 
		<artifactId>archaius-core</artifactId> 
	</exclusion>
 </exclusions>

```





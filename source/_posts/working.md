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



```

  public void modifyUserId(String phoneUserId, String wxUerId) {

    ActionListener<BulkByScrollResponse> listener = new ActionListener<BulkByScrollResponse>() {
      @Override
      public void onResponse(BulkByScrollResponse bulkByScrollResponse) {
        System.out.println("执行成功");
      }

      @Override
      public void onFailure(Exception e) {
        System.out.println("执行失败：" + e.getMessage());
      }
    };

    //创建SearchRequest对象
    SearchRequest searchRequest = new SearchRequest("bid-browse-log").types("type");
    //指定查询条件
    SearchSourceBuilder builder = new SearchSourceBuilder();
    //terms查询
    builder.query(QueryBuilders.termsQuery("userId", phoneUserId));
    //将条件放入request中
    searchRequest.source(builder);

    UpdateByQueryRequest updateByQueryRequest = new UpdateByQueryRequest(searchRequest);
    updateByQueryRequest.setConflicts("proceed");
    updateByQueryRequest.setRefresh(true);

    Map<String, Object> params = new HashMap<String, Object>();
    params.put("userId", wxUerId);
    //ctx._source即为该索引自己
    String code = "bid-browse-log._source.userId=params.userId;";
    ScriptType type = ScriptType.INLINE;
    //使用脚本进行更新字段值
    Script script = new Script(type, Script.DEFAULT_SCRIPT_LANG, code, params);
    updateByQueryRequest.setScript(script);
    try {
      client.getObject().updateByQueryAsync(updateByQueryRequest, RequestOptions.DEFAULT, listener);
    } catch (Exception e) {
      System.out.println("执行报错：" + e.getMessage());
    }
  }
```



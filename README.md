# 苹果推送APNS的HTTP2解决方案

## 苹果HTTP2推送[官网](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CommunicatingwithAPNs.html#//apple_ref/doc/uid/TP40008194-CH11-SW1)

    Status code     Description
    200             Success
    400             Bad request
    403             There was an error with the certificate or with the provider authentication token
    405             The request used a bad :method value. Only POST requests are supported.
    410             The device token is no longer active for the topic.
    413             The notification payload was too large.
    429             The server received too many requests for the same device token.
    500             Internal server error
    503             The server is shutting down and unavailable.




## 1 调用说明  
Maven的pom.xml添加以下内容  

        <dependency>
            <groupId>com.cdeer</groupId>
            <artifactId>apns-http2-core</artifactId>
            <version>1.0</version>
        </dependency>
        
代码调用如下  

1 初始化服务

        // 读取证书
        InputStream is = Thread.currentThread().getContextClassLoader().getResourceAsStream("zhengshu.p12");

        ApnsConfig config = new ApnsConfig();
        config.setName("name1");// 推送服务名称
        config.setDevEnv(false);// 是否是开发环境
        config.setKeyStore(is);// 证书
        config.setPassword("111111");// 证书密码
        config.setPoolSize(1);// 线程池大小
        config.setTimeout(3000);// TCP连接超时时间
        config.setTopic("com.push");// 标题,即证书的bundleID
        ApnsServiceManager.createService(config);
        
2 推送  
        
        Payload payload = new Payload();
        payload.setAlert("test");
        
        ApnsService service = ApnsServiceManager.getService(appName);
        service.sendNotification(token, payload);
        
3 错误处理  

        ErrorDispatcher.getInstance().addListener(new ErrorListener() {
            @Override
            public void handle(ErrorModel errorModel) {
                log.info("收到监听:" + errorModel);

            }
        });
        
        
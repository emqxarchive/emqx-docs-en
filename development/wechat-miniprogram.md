# EMQ X MQTT WeChat Mini Program Access


WeChat mini program supports instant communication through WebSocket, and EMQ X's MQTT Over WebSocket can be fully compatible with the WeChat mini program.

{% hint style="info" %}
Due to the specification restrictions of WeChat Mini Programs, EMQ X needs to pay attention to the following points when using WeChat Mini Programs access:

- It must use domain name that has passed [domain name registration](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D%E5%A4%87%E6%A1%88)

- The domain name needs to be in the [mini program management background](https://mp.weixin.qq.com/wxamp/devprofile/get_profile) domain name/IP whitelist (development -> development settings -> server domain name -> socket legal domain name )

- Only WebSocket/TLS protocol is supported, and the domain name needs to be assigned a certificate issued by a trusted CA

- Due to the bug of the WeChat mini program, the Android machine must use the TLS/443 port, otherwise the connection will fail (that is, the connection address cannot have a port)

  {% endhint %}

## Reference

- -EMQ X [Use WebSocket to connect to MQTT server](https://www.emqx.io/cn/blog/connect-to-mqtt-broker-with-websocket)
- CSDN [Nginx reverse proxy WebSocket](https://www.xncoding.com/2018/03/12/fullstack/nginx-websocket.html)
- WeChat Mini Program MQTT access [Demo](https://github.com/iAoe444/WeChatMiniEsp8266)


## Detailed steps

1. Register a WeChat Mini Program account and download [WeChat Developer Tools](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html). Due to the relatively high security requirements of the WeChat mini program, the communication with the back-end server must use the https or wss protocol. Therefore, a domain name server must be set up in the back-end of the WeChat mini program.

After logging in to the background of the applet, find **Development Settings -> Server Domain Name** to configure the server, fill in the socket legal domain name, such as wss://xxx.emqx.io.  The port number is not needed here.

2. Apply for and install a certificate on the server side. You can [apply for a free certificate](https://www.huaweicloud.com/product/scm.html) from  cloud providers such as Huawei Cloud.

It must be noted here that when the certificate bound is applied, it must be consistent with the server domain name used. It is recommended to use Nginx as a reverse proxy and terminate the certificate. The relevant configuration is as follows:

```bash
server {
    listen  443 ssl;        
    server_name xxx.emqx.io; 
    ssl_certificate   cert/***.pem;
    ssl_certificate_key  cert/***.key;
    ssl_session_timeout  5m;      
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    # Add reverse proxy
    location /mqtt {
      proxy_pass http://127.0.0.1:8083/mqtt;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      # client_max_body_size 35m;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";    
    }

}
```

3. The open source community provides a demo for the mini program access of MQTT: [https://github.com/iAoe444/WeChatMiniEsp8266](https://github.com/iAoe444/WeChatMiniEsp8266) After downloading and decompressing to a folder, you can use it  by opening the `index.js` file with WeChat Mini Program Developer Tool, **changing the MQTT address, username and password to the actual parameters**.

According to the above 3 steps of installation and configuration, your WeChat mini program can successfully connect to the EMQ X server.


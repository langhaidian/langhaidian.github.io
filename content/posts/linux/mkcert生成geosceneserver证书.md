---
title: "mkcert生成GeosceneServer证书"
date: 2024-08-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["mkcert"]
categories: ["证书"]
author: "langhaidian"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "" # edit text
    appendFilePath: true # to append file path to Edit link
---

* 解决Geoscene Server证书自己生成碰到浏览器不信任问题。采用mkcert工具生成相关证书。

#### 准备软件：
mkcert： https://github.com/FiloSottile/mkcert/releases
openssl：https://slproweb.com/download/Win64OpenSSL_Light-1_1_1q.msi

#### 证书生成
1. 定位到mkcert目录下，查看帮助
    ```
    mkcert --help
    ```

2. 安装CA证书
    ```
    mkcert -install
    ```
3. 生成证书,可以给定ip地址，*.portal.com通配符域名，具体域名gx42.portal.com等。当前目录下会生成两个.pem文件。
    ```
    mkcert 192.168.28.42  192.168.28.215 "*.portal.com" gx42.portal.com

    将192.168.X.X-key.pem文件转换成key
    openssl rsa -in 192.168.X.X.pem -out 192.168.X.X.key  
    ```
4. 利用openssl生成pfx证书,设定密码以及记录相关密码，tomcat配置时使用。
    ```
    openssl> pkcs12 -export -out d:\mkcert\192.168.X.X.pfx 
    -in d:\mkcert\192.168.X.X.pem -inkey d:\mkcert\192.168.X.X.key
    ```
5. 查找CA证书安装位置
    ```
    mkcert -CAROOT 
    ```
    将 rootCA.pem 转换成.crt格式，用于客户端安装自签名证书使用
    ```
    openssl x509 -in rootCA.pem -out rootCA.crt
    ```

    双击rootCA.crt进行安装,按照提示将证书存储到受信任的根证书颁发机构。

6. 配置tomcat证书信息
    ```
    <!--<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol....>" -->
    Modify the connector to match the parameters below:

    <Connector port="443" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="150" SSLEnabled="true" >
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
            <Certificate certificateKeystoreFile="conflocalhost-rsa-key.pem"
            certificateKeystorePassword="con"
            type="RSA" />
        </SSLHostConfig>
    </Connector>

    
    <Connector port="443" protocol="org.apache.coyote.http11.Http11AprProtocol"
           maxThreads="150" SSLEnabled="true" >
    <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="conf/ssl/172.26.156.21.pfx"
        certificateKeystorePassword="gxzb@123"
        type="PKCS12" />
    </SSLHostConfig>
    </Connector>
    ```

    ```
    keytools:
    keytool -genkey -alias mykey -keyalg RSA -keystore keystore.jks -validity 3650
    ```

    ```
    <Connector port="443" protocol="org.apache.coyote.http11.Http11AprProtocol"
            maxThreads="150" SSLEnabled="true"
            scheme="https" secure="true"
            keystoreFile="/path/to/keystore.jks"
            keystorePass="password"
            clientAuth="false" sslProtocol="TLS"/>



        <Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
            maxThreads="550" SSLEnabled="true"
            scheme="https" secure="true"
            keystoreFile="ssl/anyangarcgis.jks"
            keystorePass="anyang"
            clientAuth="false" sslProtocol="TLS"/>





          <Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
            maxThreads="550" SSLEnabled="true">
            <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol"/>
            <SSLHostConfig>
            <Certificate certificateKeystoreFile="conf/cert/xkcdxgx.pfx"
            certificateKeystorePassword="anyang"
            type="RSA"/>
            </SSLHostConfig>
           </Connector>



           {"WebContextURL":"https://59.227.104.202/arcgis"}
    ```

    ```
   nginx:
   server {
	listen  443 ssl;
	server_name  59.227.104.202  10.10.146.36 gx36.portal.com;

	ssl_certificate /etc/nginx/ssl/gx36.portal.com+5.pem
	ssl_certificate_key /etc/nginx/ssl/gx36.portal.com+5-key.pem

	ssl_session_cache   shared:SSL:1m;
	ssl_session_timeout 5m;

	ssl_ciphers   HIGH:!aNULL:!MD5;
	ssl_prefer_server_ciphers   on;


    location /server {
  	    proxy_pass https://gx36.portal.com/server;

  	    proxy_ignore_client_abort on;
  	    proxy_cookie_path /server /server

  	    proxy_set_header Host $host:$server_port;
  	    proxy_set_header X-Real-IP "";
  	    # ArcGIS Server 要求必须添加 X-Forwarded-Host 反代标头
  	    proxy_set_header X-Real-PORT $remote_port;
  	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

    location /arcgis {
  	    proxy_pass https://gx36.portal.com/arcgis;

  	    proxy_ignore_client_abort on;
  	    proxy_cookie_path /arcgis /arcgis

  	    proxy_set_header Host $host:$server_port;
  	    proxy_set_header X-Real-IP "";
  	    # ArcGIS Server 要求必须添加 X-Forwarded-Host 反代标头
  	    proxy_set_header X-Real-PORT $remote_port;
  	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
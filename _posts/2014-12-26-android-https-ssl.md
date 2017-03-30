---
layout: post
title: Android HTTPS SSL双向验证(自签名证书)
tags: [https ssl]
categories: [android]
---

>由于公司项目需要，为了保证服务器数据安全，保证接口不暴露给第三方，要求我们客户端接口全部采用HTTPS的SSL验证请求，所以才有了以下这篇博客的介绍。

## 一、HTTPS和HTTP的区别
	1、https协议需要到ca申请证书，一般免费证书很少，需要交费。

	2、http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。

	3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

	4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

## 二、SSL功能
	1)客户对服务器的身份认证:
	SSL服务器允许客户的浏览器使用标准的公钥加密技术和一些可靠的认证中心（CA）的证书，来确认服务器的合法性。

	2)服务器对客户的身份认证:
	也可通过公钥技术和证书进行认证，也可通过用户名，password来认证。

	3)建立服务器与客户之间安全的数据通道:
	SSL要求客户与服务器之间的所有发送的数据都被发送端加密、接收端解密，同时还检查数据的完整性。
>SSL协议位于TCP/IP协议与各种应用层协议之间，为数据通讯提供安全支持。SSL协议可分为两层：

>SSL记录协议（SSL Record Protocol）：它建立在可靠的传输协议（如TCP）之上，为高层协议提供数据封装、压缩、加密等基本功能的支持。

>SSL握手协议（SSL Handshake Protocol）：它建立在SSL记录协议之上，用于在实际的数据传输开始前，通讯双方进行身份认证、协商加密算法、交换加密密钥等。

## 三、生成密钥库和证书
>可参考以下密钥生成脚本，根据实际情况做必要的修改，其中需要注意的是：服务端的密钥库参数“CN”必须与服务端的IP地址相同，否则会报错，客户端的任意。

{% highlight ruby %}
1、生成服务器证书库

keytool -validity 365 -genkey -v -alias server -keyalg RSA -keystore D:\ssl\server.keystore -dname "CN=127.0.0.1,OU=rongyiwang,O=rongyiwang,L=Shanghai,ST=Shanghai,c=cn" -storepass 123456 -keypass 123456

2、生成客户端证书库

keytool -validity 365 -genkeypair -v -alias client -keyalg RSA -storetype PKCS12 -keystore D:\ssl\client.p12 -dname "CN=client,OU=rongyiwang,O=rongyiwang,L=Shanghai,ST=Shanghai,c=cn" -storepass 123456 -keypass 123456

3、从客户端证书库中导出客户端证书

keytool -export -v -alias client -keystore D:\ssl\client.p12 -storetype PKCS12 -storepass 123456 -rfc -file D:\ssl\client.cer

4、从服务器证书库中导出服务器证书

keytool -export -v -alias server -keystore D:\ssl\server.keystore -storepass 123456 -rfc -file D:\ssl\server.cer

5、生成客户端信任证书库(由服务端证书生成的证书库)

keytool -import -v -alias server -file D:\ssl\server.cer -keystore D:\ssl\client.truststore -storepass 123456 -storetype BKS -provider org.bouncycastle.jce.provider.BouncyCastleProvider

6、将客户端证书导入到服务器证书库(使得服务器信任客户端证书)

keytool -import -v -alias client -file D:\ssl\client.cer -keystore D:\ssl\server.keystore -storepass 123456

7、查看证书库中的全部证书

keytool -list -keystore D:\ssl\server.keystore -storepass 123456
{% endhighlight %}

>通过上面的步骤生成的证书，客户端需要用到的是**client.p12**(客户端证书，用于请求的时候给服务器来验证身份之用)和**client.truststore**(客户端证书库，用于验证服务器端身份，防止钓鱼)这两个文件.

其中安卓端的证书类型必须要求是BKS类型，具体生成可以参考这个[create a bks bouncycastle](http://stackoverflow.com/questions/4065379/how-to-create-a-bks-bouncycastle-format-java-keystore-that-contains-a-client-c),这里涉及到这个JAR[bcprov-ext-jdk15on-152.jar](http://www.bouncycastle.org/latest_releases.html)文件.

以下只给出Android端的请求，具体服务器端的配置可以参考这篇博客-->[Java Tomcat SSL 服务端/客户端双向认证（一）](http://www.blogjava.net/icewee/archive/2012/06/04/379947.html)
## 四、Android端SSL认证请求
>一般客户端验证SSL有两种方式，一种是通过SSLSocketFactory方式创建，需要设置域名及端口号(适应于HttpClient请求方式)，一种是通过SSLContext方式创建(适用于HttpsURLConnection请求方式).

1、下面给出SslSocketFactory方式进行SSL认证的客户端代码

{% highlight ruby %}
	private static final String KEY_STORE_TYPE_BKS = "bks";//证书类型 固定值
    private static final String KEY_STORE_TYPE_P12 = "PKCS12";//证书类型 固定值

    private static final String KEY_STORE_CLIENT_PATH = "client.p12";//客户端要给服务器端认证的证书
    private static final String KEY_STORE_TRUST_PATH = "client.truststore";//客户端验证服务器端的证书库
    private static final String KEY_STORE_PASSWORD = "123456";// 客户端证书密码
    private static final String KEY_STORE_TRUST_PASSWORD = "123456";//客户端证书库密码

	/**
     * 获取SslSocketFactory
     *
     * @param context 上下文
     * @return SSLSocketFactory
     */
    public static SSLSocketFactory getSslSocketFactory(Context context) {
        try {
            // 服务器端需要验证的客户端证书
            KeyStore keyStore = KeyStore.getInstance(KEY_STORE_TYPE_P12);
            // 客户端信任的服务器端证书
            KeyStore trustStore = KeyStore.getInstance(KEY_STORE_TYPE_BKS);

            InputStream ksIn = context.getResources().getAssets().open(KEY_STORE_CLIENT_PATH);
            InputStream tsIn = context.getResources().getAssets().open(KEY_STORE_TRUST_PATH);
            try {
                keyStore.load(ksIn, KEY_STORE_PASSWORD.toCharArray());
                trustStore.load(tsIn, KEY_STORE_TRUST_PASSWORD.toCharArray());
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    ksIn.close();
                } catch (Exception ignore) {
                }
                try {
                    tsIn.close();
                } catch (Exception ignore) {
                }
            }
            return new SSLSocketFactory(keyStore, KEY_STORE_PASSWORD, trustStore);
        } catch (KeyManagementException | UnrecoverableKeyException | KeyStoreException | FileNotFoundException | NoSuchAlgorithmException | ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 获取SSL认证需要的HttpClient
     *
     * @param context 上下文
     * @param port    端口号
     * @return HttpClient
     */
    public static HttpClient getSslSocketFactoryHttp(Context context, int port) {
        HttpClient httpsClient = new DefaultHttpClient();
        SSLSocketFactory sslSocketFactory = getSslSocketFactory(context);
        if (sslSocketFactory != null) {
            Scheme sch = new Scheme("https", sslSocketFactory, port);
            httpsClient.getConnectionManager().getSchemeRegistry().register(sch);
        }
        return httpsClient;
    }
{% endhighlight %}

2、下面给出SSLContext方式进行SSL认证的客户端代码

{% highlight ruby %}
	private static final String KEY_STORE_TYPE_BKS = "bks";//证书类型 固定值
    private static final String KEY_STORE_TYPE_P12 = "PKCS12";//证书类型 固定值

    private static final String KEY_STORE_CLIENT_PATH = "client.p12";//客户端要给服务器端认证的证书
    private static final String KEY_STORE_TRUST_PATH = "client.truststore";//客户端验证服务器端的证书库
    private static final String KEY_STORE_PASSWORD = "123456";// 客户端证书密码
    private static final String KEY_STORE_TRUST_PASSWORD = "123456";//客户端证书库密码

	/**
     * 获取SSLContext
     *
     * @param context 上下文
     * @return SSLContext
     */
    private static SSLContext getSSLContext(Context context) {
        try {
            // 服务器端需要验证的客户端证书
            KeyStore keyStore = KeyStore.getInstance(KEY_STORE_TYPE_P12);
            // 客户端信任的服务器端证书
            KeyStore trustStore = KeyStore.getInstance(KEY_STORE_TYPE_BKS);

            InputStream ksIn = context.getResources().getAssets().open(KEY_STORE_CLIENT_PATH);
            InputStream tsIn = context.getResources().getAssets().open(KEY_STORE_TRUST_PATH);
            try {
                keyStore.load(ksIn, KEY_STORE_PASSWORD.toCharArray());
                trustStore.load(tsIn, KEY_STORE_TRUST_PASSWORD.toCharArray());
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    ksIn.close();
                } catch (Exception ignore) {
                }
                try {
                    tsIn.close();
                } catch (Exception ignore) {
                }
            }
            SSLContext sslContext = SSLContext.getInstance("TLS");
            TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
            trustManagerFactory.init(trustStore);
            KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance("X509");
            keyManagerFactory.init(keyStore, KEY_STORE_PASSWORD.toCharArray());
            sslContext.init(keyManagerFactory.getKeyManagers(), trustManagerFactory.getTrustManagers(), null);
            return sslContext;
        } catch (Exception e) {
            Log.e("tag", e.getMessage(), e);
        }
        return null;
    }

    /**
     * 获取SSL认证需要的HttpClient
     *
     * @param context 上下文
     * @return OkHttpClient
     */
    public static OkHttpClient getSSLContextHttp(Context context) {
        OkHttpClient client = new OkHttpClient();
        SSLContext sslContext = getSSLContext(context);
        if (sslContext != null) {
            client.setSslSocketFactory(sslContext.getSocketFactory());
        }
        return client;
    }

    /**
     * 获取HttpsURLConnection
     *
     * @param context 上下文
     * @param url     连接url
     * @param method  请求方式
     * @return HttpsURLConnection
     */
    public static HttpsURLConnection getHttpsURLConnection(Context context, String url, String method) {
        URL u;
        HttpsURLConnection connection = null;
        try {
            SSLContext sslContext = getSSLContext(context);
            if (sslContext != null) {
                u = new URL(url);
                connection = (HttpsURLConnection) u.openConnection();
                connection.setRequestMethod(method);//"POST" "GET"
                connection.setDoOutput(true);
                connection.setDoInput(true);
                connection.setUseCaches(false);
                connection.setRequestProperty("Content-Type", "binary/octet-stream");
                connection.setSSLSocketFactory(sslContext.getSocketFactory());
                connection.setConnectTimeout(30000);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return connection;
    }
{% endhighlight %}

这里还没有给出Webview进行HTTPS请求的方式，正在研究中，后续有了再来更新，这里需要注意一下的就是SSLContext的**默认端口是443端口，这点需要注意一下**。

这里给出全部代码链接-->[HttpClientSslHelper.java](https://gist.github.com/Frank-Zhu/41e21a00df26d63cd38d)
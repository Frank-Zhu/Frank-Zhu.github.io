---
layout: post
title: Android HTTPS SSL双向验证(CA根证书)
tags: [https ssl]
categories: [android]
---

>由于公司项目需要，需要将原来部署的自签名证书切换为CA系统提供商的证书，所以需要对原来的证书生成做相应处理与替换。



想要看自签名证书生成方法可以参考原来的文章[Android HTTPS SSL双向验证(自签名证书)](http://frank-zhu.github.io/android/2014/12/26/android-https-ssl/)



## 一、相关证书生成
	1、从运维那边拿到P12格式的证书文件，后缀一般是.pfx或者.p12的文件

	2、生成cer文件
{% highlight ruby %}

1、从运维那边拿到P12格式的证书文件，后缀一般是.pfx或者.p12的文件，这个文件就是下面需要用到的client.p12文件

2、生成服务器cer证书

openssl pkcs12 -in server.pfx -out server.cer

3、生成客户端信任证书库(由服务端证书生成的证书库)，这里生成的文件就是用来校验服务器有效性的文件client.truststore

keytool -import -v -alias server -file server.cer -keystore client.truststore -storepass 123456 -storetype BKS -provider org.bouncycastle.jce.provider.BouncyCastleProvider

{% endhighlight %}

>通过上面的步骤生成的证书，客户端需要用到的是**client.p12**(客户端证书，用于请求的时候给服务器来验证身份之用)和**client.truststore**(客户端证书库，用于验证服务器端身份，防止钓鱼)这两个文件.

其中安卓端的证书类型必须要求是 BKS 类型，这里涉及到这个 JAR [bcprov-ext-jdk15on-156.jar](http://www.bouncycastle.org/latest_releases.html)文件.这里主要就是把下载的这个 jar 文件放到你 Java 主目录下的 **jre/lib/ext**目录下，然后修改**jre/lib/security/java.security**这个文件，在List of providers 注释的地方添加这一行**security.provider.11=org.bouncycastle.jce.provider.BouncyCastleProvider**其中11个数字可以自己改，按顺序排就行，之后重启终端，再执行应该就不会报找不到类的错误了。一定要注意目录不要放错了，不然一直会报找不到类的。

## 二、Android端SSL认证请求
1、通过 **client.p12** 和 **client.truststore** 这两个文件进行认证的可以参考上篇博客的内容即可

2、不生成 **client.truststore** 这个文件，自己做服务器校验的代码如下

{% highlight ruby %}
	public static SSLContext getSslContextByDefaultTrustManager(Context context) {
	        if (sslContext == null) {
	            try {
	                // 服务器端需要验证的客户端证书
	                KeyStore keyStore = KeyStore.getInstance(KEY_STORE_TYPE_P12);
	
	                InputStream ksIn = context.getResources().getAssets().open(KEY_STORE_CLIENT_PATH);
	                try {
	                    keyStore.load(ksIn, KEY_STORE_PASSWORD.toCharArray());
	                } catch (Exception e) {
	                    e.printStackTrace();
	                } finally {
	                    try {
	                        ksIn.close();
	                    } catch (Exception e) {
	                        e.printStackTrace();
	                    }
	                }
	                sslContext = SSLContext.getInstance("TLS");
	                // Create a TrustManager that trusts the CAs in our KeyStore
	                String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
	                TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
	                tmf.init(keyStore);
	
	                KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance("X509");
	                keyManagerFactory.init(keyStore, KEY_STORE_PASSWORD.toCharArray());
	                sslContext.init(keyManagerFactory.getKeyManagers(), tmf.getTrustManagers(), null);
	            } catch (Exception e) {
	                e.printStackTrace();
	            }
	        }
	        return sslContext;
	    }
{% endhighlight %}

3、也可以直接通过运维提供的 **crt** 证书自己写整个校验逻辑

{% highlight ruby %}
	public static SSLContext getSslContextByCustomTrustManager(Context context) {
	        if (sslContext == null) {
	            try {
	                CertificateFactory cf = CertificateFactory.getInstance("X.509");
	                InputStream caInput = new BufferedInputStream(context.getResources().getAssets().open(KEY_CRT_CLIENT_PATH));
	                final Certificate ca;
	                try {
	                    ca = cf.generateCertificate(caInput);
	                } finally {
	                    caInput.close();
	                }
	
	                sslContext = SSLContext.getInstance("TLS");
	                sslContext.init(null, new X509TrustManager[]{new X509TrustManager() {
	                    public void checkClientTrusted(X509Certificate[] chain,
	                                                   String authType) throws CertificateException {
	                        Log.d("HttpClientSslHelper", "checkClientTrusted --> authType = " + authType);
	                        //校验客户端证书
	                    }
	
	                    public void checkServerTrusted(X509Certificate[] chain,
	                                                   String authType) throws CertificateException {
	                        Log.d("HttpClientSslHelper", "checkServerTrusted --> authType = " + authType);
	                        //校验服务器证书
	                        for (X509Certificate cert : chain) {
	                            cert.checkValidity();
	                            try {
	                                cert.verify(ca.getPublicKey());
	                                isServerTrusted = true;
	                            } catch (NoSuchAlgorithmException | InvalidKeyException | NoSuchProviderException | SignatureException e) {
	                                e.printStackTrace();
	                                isServerTrusted = false;
	                            }
	                        }
	                    }
	
	                    public X509Certificate[] getAcceptedIssuers() {
	                        return new X509Certificate[0];
	                    }
	                }}, null);
	            } catch (Exception e) {
	                e.printStackTrace();
	            }
	        }
	        return sslContext;
	    }
{% endhighlight %}



这里给出全部代码链接-->[**AndroidHttpsDemo**](https://github.com/Frank-Zhu/AndroidHttpsDemo)
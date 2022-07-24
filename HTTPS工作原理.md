## HTTPS工作原理

### 1.对称加密

#### 1.1 解释

​			加解密使用的是同一个密钥 

#### 1.2 常见算法

 			AES，chacha20  

#### 1.3 缺点

​			1）秘钥泄露问题：客户端和服务端需要保存相同的秘钥

​			2）秘钥保存问题 ： 多个客户端就会请求服务端，服务端就需要保存多个秘钥

### 2 非对称加密

#### 2.1 解释

​		 加解密是采用不同的秘钥对，公钥加密，私钥解密

#### 2.2 常见算法	

​		RSA 、ECC

####  2.3 缺点

​		加密速度慢，效率低

#### 2.4 加密流程

![image-20220710120806022](/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220710120806022.png)

### 3.HTTPS混合加密模式

####  3.1 加密流程

![image-20220710121112233](/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220710121112233.png)

3.1

![image-20220723003850675](/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220723003850675.png)

通过CA 根证书校验，CA公钥是否是有效的并且是小红的







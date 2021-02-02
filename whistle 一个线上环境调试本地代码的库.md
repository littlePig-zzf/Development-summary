**whistle 一个线上环境调试本地代码的库**

**官网地址：http://wproxy.org/whistle/**



安装执行步骤：

1、安装

```sh
npm install -g whistle
```

2、启动

```sh
w2 start
```

![image-20210202135718540](https://ftp.bmp.ovh/imgs/2021/02/3f3dfc4aeee4da9d.png)



3、使用浏览器访问地址 

http://127.0.0.1:8899/

点击create创建test的规则，然后增加线上环境地址，和需要映射的本地项目运行地址



![image-20210202141053681](https://ftp.bmp.ovh/imgs/2021/02/5f5f0a33d667d0fd.png)



我项目运行地址为：

![3](https://ftp.bmp.ovh/imgs/2021/02/d191b0f259460f7b.png)



所以配置的时候，直接填localhost:8080即可。



![image-20210202140824780](https://ftp.bmp.ovh/imgs/2021/02/4b0c7f437e74edbf.png)



4、手机跟电脑需连上同一wifi上，然后


![image-20210202141053681](https://ftp.bmp.ovh/imgs/2021/02/86509fdb662fe862.png)

![image-20210202141053901](https://ftp.bmp.ovh/imgs/2021/02/8f62f400782fb637.png)

![image-20210202140824780](https://ftp.bmp.ovh/imgs/2021/02/c4ba10ba473af082.png)



5、访问http://local.whistlejs.com:8899/ ，可以j进行抓包，端口号需要对应w2 start启动之后生成的端口号，第一张截图。

![44](https://ftp.bmp.ovh/imgs/2021/02/b1ac8aafc1a5e7ac.png)

6、最后，修改了本地代码，直接手机访问线上地址，就可以进行调试了。

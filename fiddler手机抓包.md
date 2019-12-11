### fiddler手机抓包

window下安装根证书失败，如何解决？

打开fiddler安装目录执行以下命令行：

`.\makecert.exe -r -ss my -n "CN=DO_NOT_TRUST_FiddlerRoot, O=DO_NOT_TRUST, OU=Created by http://www.fiddler2.com" -sky signature -eku 1.3.6.1.5.5.7.3.1 -h 1 -cy authority -a sha1 -m 120 -b 10/12/2020`


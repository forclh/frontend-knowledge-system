
## 应用层协议

### URL

URL（uniform resource locator，**统一资源定位符**）用于定位网络服务

![image-20230112104100679](http://mdrs.yuanjin.tech/img/202301121041726.png)

URL是一个固定格式的字符串

![image-20230112102913056](http://mdrs.yuanjin.tech/img/202301121029498.png)

它表达了：

从网络中==哪台计算机（domain）==中的==哪个程序（port）==寻找==哪个服务（path）==，并注明了获取服务的==具体细节（path）==，以及要用什么样的==协议通信（schema）==

这里面包含了一些细节：

- 当协议是`http`端口为`80`时，端口可以省略
- 当协议是`https`端口为`443`时，端口可以省略
- `schema`、`domain`、`path`是必填的，其他的根据具体的要求填写

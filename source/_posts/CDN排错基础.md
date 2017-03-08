---
title:  CDN基础排查
tags: 运维基础
---

### 简介
内容分发网络（Content delivery network或Content distribution network，缩写：CDN）是指一种通过互联网互相连接的电脑网络系统，利用最靠近每位用户的服务器，更快、更可靠地将音乐、图片、视频、应用程序及其他文件发送给用户，来提供高性能、可扩展性及低成本的网络内容传递给用户（引用自维基百科）。在访问使用了CDN加速的网站时，首先会通过智能解析系统为用户分配最近的节点，以节省用户访问的时间。CDN的节点一般是一个反向代理服务器，用户通过DNS智能调度系统获取最近最近节点的ip，通过访问节点上的数据，而不是用户源站来达到加速访问的目的。根据最近公司CDN分享总结了这篇博客。


### 缓存规则
CDN节点会缓存用户网站的内容，当客户端（浏览器）在访问用户网站内容是，浏览器访问的不是原本的网站而是CDN的节点，由于CDN节点离用户较近，具有访问速度上的优势。那么现在的问题是CDN使用者如何能够控制节点和浏览器的缓存行为呢，通过设置CDN供应商的控制台和网站的http header来实现的。下面这张图说明了在某场景下CDN节点的默认缓存行为。
![CDN缓存控制策略](http://7xr7kh.com1.z0.glb.clouddn.com/CDN%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5.png)


### 常见问题 
正常思路：工具+分析，情景重现    分析问题     得出结论
 
场景一：用户反馈页面打不开，CDN资源加载失败（返回404或者显示超时）
1.先dig域名看解析是否正常
2. 获取访问异常的url（例http://static.smartisanos.cn/common/pr/original-img/jianguo/jianguo2.jpg）
3. 使用浏览器进行测试返回正常
4. 测试源站访问正常
5. 测试用户反馈的节点是否返回异常
6. 使用CDN运营工具查看，发现回源失败率上升，可以定位到是回源网络层存在问题

场景二：访问到旧文件
1.dig加速域名确定域名解析是否正常
```
dig do-yd-cc.ufile.ucloud.com.cn（加速域名）
```
2.确定节点资源是否已经更新
```
curl -vofile "http://.." -x ip：port
```

### 测试命令
测试解析
```
dig do-yd-cc.ufile.ucloud.com.cn（加速域名）
```

绑定host测试源站：
```
wget -S --header “Host:www.yxgames.com” http://106.75.141.170（源站）/newscontent/lhsgz/7133.html
```

测试节点或源站:
```
wget -S “http://imgs.ufile.ucloud.com.cn/vxia_20160901110633.apk” -e http_proxy=221.181.173.106（节点或源站）
```
```
curl –voa “http://imgs.ufile.ucloud.com.cn/vxia_20160901110633.apk ” -x 221.181.173.106:80（节点或源站）
```

验证referer头部白名单是否生效
```
curl -I 'http://res2.eqh5.com/' -x 106.74.23.144:80 -H "referer:http://*.yqh5.cn“
```

### 回源host
首先，源站决定了回源时，请求到哪个IP的问题，回源host决定回源请求访问到该IP上的哪个站点。也就是说，回源host是为了解决一个ip对应多个域名的场景而出现的。这种情况在使用了虚拟主机web-server和VPS租用的主机中非常常见。CDN默认是以加速域名为回源host回源，只有在源站未绑定加速域名时才需配置回源host。如果源站没有绑定加速域名将导致CDN无法正确回源，从而出现会502或404报错。

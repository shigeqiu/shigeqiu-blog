# Nginx 

<!-- TOC -->

- [Nginx](#nginx)
    - [参考网站](#参考网站)
    - [安装启动](#安装启动)
    - [location匹配命令](#location匹配命令)
    - [注意proxy_pass路径](#注意proxy_pass路径)

<!-- /TOC -->

## 参考网站

http://tengine.taobao.org/book/# 

## 安装启动

1. 启动
　　解压至c:\nginx，运行nginx.exe(即nginx -c conf\nginx.conf)，默认使用80端口，日志见文件夹C:\nginx\logs
2. 使用
　　http://localhost
3. 关闭
　　`nginx -s stop` 或`taskkill /F /IM nginx.exe > nul `

## location匹配命令

- `~ ` 波浪线表示执行一个正则匹配，区分大小写
- `~*` 表示执行一个正则匹配，不区分大小写
- `^~` 表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
- `=` 进行普通字符精确匹配
- `@` 定义一个命名的 location，使用在内部定向时，例如 error_page, try_files


location 匹配顺序优先级：


(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)






所以实际使用中，个人觉得至少有三个匹配规则定义，如下：

第一个必选规则直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。这里是直接转发给后端应用服务器了，也可以是一个静态首页

```
location = / {
proxy_pass http://tomcat:8080/index
}
```
第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
```conf
location ^~ /static/ {
    root /webroot/static/;
}

location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}
```
第三个规则就是通用规则，用来转发动态请求到后端应用服务器
非静态文件请求就默认是动态请求，自己根据实际把握,毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
```
location / {
proxy_pass http://tomcat:8080/
}
```


## 注意proxy_pass路径

在nginx中配置proxy_pass时，如果是按照`^~`匹配路径时,要注意proxy_pass后的url最后的`/`,当加上了`/`，相当于是绝对根路径，则nginx不会把location中匹配的路径部分代理走;如果没有`/`，则会把匹配的路径部分也给代理走。
```
location ^~ /static_js/ { 
    proxy_cache js_cache; 
    proxy_set_header Host js.test.com; 
    proxy_pass http://js.test.com/; 
}
```
如上面的配置，如果请求的url是`http://servername/static_js/test.html`
会被代理成`http://js.test.com/test.html`


而如果这么配置
```
location ^~ /static_js/ { 
    proxy_cache js_cache; 
    proxy_set_header Host js.test.com; 
    proxy_pass http://js.test.com; 
}
```
则会被代理到`http://js.test.com/static_js/test.htm`







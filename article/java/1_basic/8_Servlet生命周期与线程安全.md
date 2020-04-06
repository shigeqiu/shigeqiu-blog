# Servlet

<!-- TOC -->

- [Servlet](#servlet)
    - [线程安全](#线程安全)
    - [servlet 生命周期](#servlet-生命周期)
        - [初始化阶段](#初始化阶段)
            - [实例化](#实例化)
            - [容器装载Servlet](#容器装载servlet)
            - [调用init()方法进行初始化](#调用init方法进行初始化)
        - [响应客户请求阶段](#响应客户请求阶段)
        - [终止阶段](#终止阶段)

<!-- /TOC -->

## 线程安全

- ServletContext：它是线程不安全的，多线程下可以同时进行读写，因此我们要对其读写操作进行同步或者深度的clone。
- HttpSession:同样是线程不安全的，和ServletContext的操作一样。
- ServletRequest:它是线程安全的，对于每一个请求由一个工作线程来执行，都会创建一个ServletRequest对象，所以ServletResquest只能在一个线程中被访问，而且他只在service()方法内是有效的。
- Servlet的成员变量被所有的线程共享，
- http://www.cnblogs.com/digdeep/p/4429098.html
- https://www.cnblogs.com/LipeiNet/p/5699944.html


## servlet 生命周期



###  初始化阶段

#### 实例化  

创建一个Servlet实例对象（即为该对象分配一块内存空间）；

#### 容器装载Servlet

 - Servlet容器启动时自动加载某些servlet，通常大多数Servlet是在用户第一次请求的时候由应用服务器创建并初始化，但`<load-on-startup>n</load-on-startup>`可以用来改变这种状况，根据自己需要改变加载的优先级！
 - 客户端首次向Servlet发出请求，Servlet 创建于用户第一次调用对应于该 Servlet 的 URL 时。
 - Servlet的类文件被更新后，重新加载servlet;

#### 调用init()方法进行初始化

init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。因此，它是用于一次性初始化。

### 响应客户请求阶段

服务。容器收到该Servlet请求，调用该Servlet对象的service()方法处理请求；

service() 方法由容器调用，service 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。

对于到达Servlet容器的客户请求，Servlet容器创建于这个请求的ServletRequest对象和ServletResponse对象，然后调用Servlet的service()方法，service方法从ServletRequest对象获得客户请求信息，处理改请求。并通过ServletResponse对象向客户返回响应结果。

### 终止阶段

当服务器不再需要该Servlet对象的时候，服务器调用destroy()方法卸载该Servlet，
并释放Servlet运行时占用的资源；当多个客户请求一个Servlet时，引擎为每一个客户启动一个线程，

destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。


当web应用被终止，或Servlet容器终止运行，或Servlet容器重新加载servlet实例时，Servlet容器会调用Servlet的destory() 方法，在destory()方法中可以释放Servlet所占用的资源。

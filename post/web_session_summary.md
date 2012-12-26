## session 总结

	由于http请求是无状态的，每次请求是都打开新的连接到服务器并不保留先前的任何信息，但是现实中通常需要在请求之间标识用户，
	例如用户一旦登陆过后就需要记住用户登录状态，再例如几个请求之间需要共享数据(如果不使用会话变量)，就需要在每次请求时都携带所有的参数。


### 会话基本流程

   第一次请求达到后，服务器需要表示这个请求用户，然后在服务器生成唯一表示映射到这个用户，返回时把唯一标识放回给客户端，以后的请求都需要携带这个标识用来识别这个请求属于哪个会话

### 唯一标识的传递

  1. Cookies

   通过把唯一标识写到浏览器的cookies中，随后的每次请求都携带相关的cookies,然后服务器解析cookies, 缺点是有些浏览器并不支持.

   2. url参数
     a. Hidden Form Fields
     通过在页面中添加隐藏表单域，每次提交时都把这个参数传递，缺点是<a>标签并不支持

     b. Url Rewriting
     在所有的url中都添加额外的参数传递，这个需要在服务器中实现

### Server会话

  在server需要维护会话的创建，唯一表示与会话之间的关联。sessionId, session, sessionManager.


### 实例学习

#### ring中的wrap-session实现

  使用cookies来传递sessionId, 通过在函数中传递session对象实现对session的访问，从而可以在wrap-session后面对session进行读写。

  session-key-name -> read session -> add session to request -> handle request -> get session in response ->
    |
    V
   if exist session, then write session into store 
   if not exist session,  then delete session from store
    |
    V
   write cookies


#### servlet中session的实现





### 进一步了解

  1. URL重写是如何实现
  2. Cookies详细信息
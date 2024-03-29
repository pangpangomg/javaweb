##### Servlet的体系结构

```javascript
###### Servlet -- 接口

​		GenericServlet -- 抽象类
​		HttpServlet  -- 抽象类

###### GenericServlet

将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象，将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可

###### HttpServlet

​	对http协议的一种封装，简化操作

1. 定义类继承HttpServlet
2. 复写doGet/doPost方法
```

##### Servlet的生命周期

```javascript
1. 被创建：执行init方法，只执行一次
	* Servlet什么时候被创建？
		* 默认情况下，第一次被访问时，Servlet被创建
		* 可以配置执行Servlet的创建时机。* 在<servlet>标签下配置
		1. 第一次被访问时，创建	* <load-on-startup>的值为负数
		2. 在服务器启动时，创建	* <load-on-startup>的值为0或正整数
	* Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
		* 多个用户同时访问时，可能存在线程安全问题。
		* 解决：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值
2. 提供服务：执行service方法，执行多次
	* 每次访问Servlet时，Service方法都会被调用一次。
3. 被销毁：执行destroy方法，只执行一次
	* Servlet被销毁时执行。服务器关闭时，Servlet被销毁
	* 只有服务器正常关闭时，才会执行destroy方法。
	* destroy方法在Servlet被销毁之前执行，一般用于释放资源
```

##### Servlet执行原理

```javascript
* 执行原理：
	1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径
	2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容。
	3. 如果有，则在找到对应的<servlet-class>全类名
	4. tomcat会将字节码文件加载进内存，并且创建其对象
	5. 调用其方法
```

##### Servlet3.0版本的特点

```javascript
* Servlet3.0：
	* 好处：
		* 支持注解配置。可以不需要web.xml了。
	* 步骤：
		1. 创建JavaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml
		2. 定义一个类，实现Servlet接口
		3. 复写方法
		4. 在类上使用@WebServlet注解，进行配置
			* @WebServlet("资源路径")
```

##### request对象

```javascript
Request：
	1. request对象和response对象的原理
		1. request和response对象是由服务器创建的。我们来使用它们
		2. request对象是来获取请求消息，response对象是来设置响应消息
	2. request对象继承体系结构：	
		ServletRequest		--	接口	|	继承
		HttpServletRequest	-- 接口	|	实现
		org.apache.catalina.connector.RequestFacade 类(tomcat)
	3. request功能：
		1. 获取请求消息数据
			1. 获取请求行数据
				* GET /day14/demo1?name=zhangsan HTTP/1.1
				* 方法：
					1. 获取请求方式 ：GET
						* String getMethod()  
					2. (*)获取虚拟目录：/day14
						* String getContextPath()
					3. 获取Servlet路径: /demo1
						* String getServletPath()
					4. 获取get方式请求参数：name=zhangsan
						* String getQueryString()
					5. (*)获取请求URI：/day14/demo1
						* String getRequestURI():		/day14/demo1
						* StringBuffer getRequestURL()  :http://localhost/day14/demo1
						* URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
						* URI：统一资源标识符 : /day14/demo1					共和国
					6. 获取协议及版本：HTTP/1.1
						* String getProtocol()
					7. 获取客户机的IP地址：
						* String getRemoteAddr()
			2. 获取请求头数据
				* 方法：
					* (*)String getHeader(String name):通过请求头的名称获取请求头的值
					* Enumeration<String> getHeaderNames():获取所有的请求头名称
				
			3. 获取请求体数据:
				* 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
				* 步骤：
					1. 获取流对象
						*  BufferedReader getReader()：获取字符输入流，只能操作字符数据
						*  ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据
							* 在文件上传知识点后讲解
					2. 再从流对象中拿数据
		2. 其他功能：
			1. 获取请求参数通用方式：不论get还是post请求方式都可以使用下列方法来获取请求参数
				1. String getParameter(String name):根据参数名称获取参数值    username=zs&password=123
				2. String[] getParameterValues(String name):根据参数名称获取参数值的数组  hobby=xx&hobby=game
				3. Enumeration<String> getParameterNames():获取所有请求的参数名称
				4. Map<String,String[]> getParameterMap():获取所有参数的map集合
				* 中文乱码问题：
					* get方式：tomcat 8 已经将get方式乱码问题解决了
					* post方式：会乱码
						* 解决：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");
			2. 请求转发：一种在服务器内部的资源跳转方式
				1. 步骤：
					1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
					2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 
				2. 特点：
					1. 浏览器地址栏路径不发生变化
					2. 只能转发到当前服务器内部资源中。
					3. 转发是一次请求
			3. 共享数据：
				* 域对象：一个有作用范围的对象，可以在范围内共享数据
				* request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
				* 方法：
					1. void setAttribute(String name,Object obj):存储数据
					2. Object getAttitude(String name):通过键获取值
					3. void removeAttribute(String name):通过键移除键值对
			4. 获取ServletContext：
				* ServletContext getServletContext()
```

##### response对象

```javascript
Response对象
	* 功能：设置响应消息
		1. 设置响应行
			1. 格式：HTTP/1.1 200 ok
			2. 设置状态码：setStatus(int sc) 
		2. 设置响应头：setHeader(String name, String value) 
			
		3. 设置响应体：
			* 使用步骤：
				1. 获取输出流
					* 字符输出流：PrintWriter getWriter()
					* 字节输出流：ServletOutputStream getOutputStream()
				2. 使用输出流，将数据输出到客户端浏览器
	重定向
		* 重定向：资源跳转的方式
		* 代码实现：
			//1. 设置状态码为302
response.setStatus(302);
//2.设置响应头location
response.setHeader("location","/day15/responseDemo2");
		        //简单的重定向方法
			        response.sendRedirect("/day15/responseDemo2");
		* 重定向的特点:redirect
			1. 地址栏发生变化
			2. 重定向可以访问其他站点(服务器)的资源
			3. 重定向是两次请求。不能使用request对象来共享数据
		* 转发的特点：forward
			1. 转发地址栏路径不变
			2. 转发只能访问当前服务器下的资源
			3. 转发是一次请求，可以使用request对象来共享数据
	* forward 和  redirect 区别
		* 路径写法：
			1. 路径分类
				1. 相对路径：通过相对路径不可以确定唯一资源
					* 如：./index.html
					* 不以/开头，以.开头路径
					* 规则：找到当前资源和目标资源之间的相对位置关系
						* ./：当前目录
						* ../:后退一级目录
			2. 绝对路径：通过绝对路径可以确定唯一资源
				* 如：http://localhost/day15/responseDemo2		/day15/responseDemo2
				* 以/开头的路径
				* 规则：判断定义的路径是给谁用的？判断请求将来从哪儿发出
					* 给客户端浏览器使用：需要加虚拟目录(项目的访问路径)
						* 建议虚拟目录动态获取：request.getContextPath()
						* <a> , <form> 重定向...
					* 给服务器使用：不需要加虚拟目录
						* 转发路径
						
	2. 服务器输出字符数据到浏览器
		* 步骤：
			1. 获取字符输出流
			2. 输出数据
		* 注意：
			* 乱码问题：
				1. PrintWriter pw = response.getWriter();获取的流的默认编码是ISO-8859-1
				2. 设置该流的默认编码
				3. 告诉浏览器响应体使用的编码
				//简单的形式，设置编码，是在获取流之前设置
					        			response.setContentType("text/html;charset=utf-8");
	3. 服务器输出字节数据到浏览器
		* 步骤：
			1. 获取字节输出流
			2. 输出数据
	4. 验证码
		1. 本质：图片
		2. 目的：防止恶意表单注册
```

##### ServletContext对象

```javascript
ServletContext对象：
	1. 概念：代表整个web应用，可以和程序的容器(服务器)来通信
	2. 获取：
		1. 通过request对象获取
			request.getServletContext();
		2. 通过HttpServlet获取
			this.getServletContext();
	3. 功能：
		1. 获取MIME类型：
			* MIME类型:在互联网通信过程中定义的一种文件数据类型
				* 格式： 大类型/小类型   text/html		image/jpeg
			* 获取：String getMimeType(String file)  
		2. 域对象：共享数据
			1. setAttribute(String name,Object value)
			2. getAttribute(String name)
			* ServletContext对象范围：所有用户所有请求的数据
			3. removeAttribute(String name)
		3. 获取文件的真实(服务器)路径
			1. 方法：String getRealPath(String path)  
				 String b = context.getRealPath("/b.txt");//web目录下资源访问
			         System.out.println(b);
			        String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
			        System.out.println(c);
			        String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
			        System.out.println(a);
```

##### ServletConfig对象

```javascript
ServletConfig对象
	在Servlet的配置文件web.xml中，可以使用一个或多个<init-param>标签为servlet配置一些初始化参数。
	当servlet配置了初始化参数后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中。我们通过ServletConfig对象就可以得到当前servlet的初始化参数信息。
```


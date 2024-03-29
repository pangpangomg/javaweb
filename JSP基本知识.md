##### JSP作用

jsp类似于HTML，它是用来展示数据的。

##### jsp指令作用

用于配置JSP页面，导入资源文件

##### jsp指令

```javascript
//纯输出
<%=%>
    1. 指令
	* 作用：用于配置JSP页面，导入资源文件
	* 格式：
		<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>
	* 分类：
		1. page		： 配置JSP页面的
			* contentType：等同于response.setContentType()
				1. 设置响应体的mime类型以及字符集
				2. 设置当前jsp页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）
			* import：导包
			* errorPage：当前页面发生异常后，会自动跳转到指定的错误页面
			* isErrorPage：标识当前也是是否是错误页面。
				* true：是，可以使用内置对象exception
				* false：否。默认值。不可以使用内置对象exception
		2. include	： 页面包含的。导入页面的资源文件
			* <%@include file="top.jsp"%>
		3. taglib	： 导入资源
			* <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
				* prefix：前缀，自定义的
```

##### *jsp内置对象(隐式对象)

```javascript
//一共有9个
	变量名						真实类型							作用
* pageContext				PageContext					当前页面共享数据，还可以获取其他八个内置对象
* request					HttpServletRequest			一次请求访问的多个资源(转发)
* session					HttpSession					一次会话的多个请求间
* application				ServletContext				所有用户间共享数据
* response					HttpServletResponse			响应对象
* page						Object						当前页面(Servlet)的对象  this
* out						JspWriter					输出对象，数据输出到页面上
* config					ServletConfig				Servlet的配置对象
* exception					Throwable					异常对象
```

##### 注释

```javascript
//1. html注释：<!-- -->:只能注释html代码片段  只能注释单行   在浏览器当中查看源代码时是能够看到的
//2. jsp注释：推荐使用<%-- --%>：可以注释所有  可以注释多行   在浏览器当中查看源代码时是能够看不到的
```

##### *JSP中的四大域对象

```javascript
//pageContext 
当前页面（最小的一个），超过这个页面就不能使用
//request    servlet三大域对象
JSP网页发出请求到另一个JSP网页之间，随后这个属性就失效。
//session
一段用户持续和服务器所连接的时间，但与服务器断线后，这个属性就无效。比如断网或者关闭浏览器。
//application
在服务器一开始执行服务，到服务器关闭为止。它的范围最大，生存周期最长。
```


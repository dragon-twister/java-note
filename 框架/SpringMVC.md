工作原理

springmvc所有的请求都提交给dispatcherservlet，它会委托系统其他模块对请求进行处理。

dispatcherservlet首先会查询一个或者多个handlermapping，找到请求的controller

conreoller进行业务处理之后会返回一个modelandvierw

dispatcherservlet查询一个或者多个ViewResolver视图解析器，找到modelandview中指定的视图对象







转发：以前的request中存放的变量不会失效，就像把两个页面拼到了一起。request.getRequestDispatcher("page.jsp").forward(request, response);



重定向：以前的request中存放的变量全部失效，并进入一个新的request作用域。response.sendRedirect("page.jsp");
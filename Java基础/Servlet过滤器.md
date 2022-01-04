# Servlet过滤器

## 原理

过滤所有的web请求（包括静态资源请求，而拦截器不包括），修改请求和返回信息

## 应用场景

1. 权限控制，比如未登陆用户重定向到登陆页面
2. 设置字符编码，避免乱码

### 使用方法

**1. 实现javax.servlet.Filter接口 **

接口中的3个方法

- void init(FilterConfig filterConfig) 用于完成过滤器的初始化

- void destroy() 用于过滤器销毁前，完成某些资源的回收

- void doFilter(ServletRequest request, ServletResponse response,FilterChain chain)   实现过滤功能，对每个请求增加额外的处理

```java
package com.cn.util;  
 
import java.io.IOException;  
 
import javax.servlet.Filter;  
import javax.servlet.FilterChain;  
import javax.servlet.FilterConfig;  
import javax.servlet.ServletException;  
import javax.servlet.ServletRequest;  
import javax.servlet.ServletResponse;  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
 
public class FilterUtil implements Filter{  
 
 @SuppressWarnings("unused")  
 private FilterConfig filterConfig;  
 @Override 
 public void init(FilterConfig filterConfig) throws ServletException {  
 this.filterConfig = filterConfig;  
        System.out.println("过滤器Filter初始化");  
    }  
 
 @Override 
 public void doFilter(ServletRequest request, ServletResponse response,  
            FilterChain chain) throws IOException, ServletException {  
 if (!(request instanceof HttpServletRequest) || !(response instanceof HttpServletResponse)) {  
 throw new ServletException("FilterUtil just supports HTTP requests");  
        }  
        HttpServletRequest httpRequest = (HttpServletRequest) request;  
        HttpServletResponse httpResponse = (HttpServletResponse) response;  
        httpRequest.setCharacterEncoding(this.filterConfig.getInitParameter("encoding"));  
        httpResponse.setCharacterEncoding(this.filterConfig.getInitParameter("encoding"));  
        chain.doFilter(httpRequest, httpResponse);  
    }  
 
 @Override 
 public void destroy() {  
        System.out.println("过滤器Filter销毁");  
    }  
 
}  
```

**2. 配置web.xml**

```text
<filter> 
 <filter-name>encodingFilter</filter-name> 
 <!-- <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class> --> 
 <filter-class>com.cn.util.FilterUtil</filter-class> 
 <async-supported>true</async-supported> 
 <init-param> 
 <param-name>encoding</param-name> 
 <param-value>UTF-8</param-value> 
 </init-param> 
</filter> 

<filter-mapping> 
 <filter-name>encodingFilter</filter-name> 
 <url-pattern>/*</url-pattern> 
</filter-mapping> 
```


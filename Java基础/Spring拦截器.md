[TOC]

# Spring拦截器

## 拦截器介绍

Java中的拦截器是动态拦截 action 调用的对象，然后提供了可以在 action 执行前后增加一些操作，也可以在 action 执行前停止操作，功能与过滤器类似，但是标准和实现方式不同。

*   登录认证：在一些应用中，可能会通过拦截器来验证用户的登录状态，如果没有登录或者登录失败，就会给用户一个友好的提示或者返回登录页面，当然大型项目中都不采用这种方式，都是调单点登录系统接口来验证用户。
*   记录系统日志：我们在常见应用中，通常要记录用户的请求信息，比如请求 ip，方法执行时间等，通过这些记录可以监控系统的状况，以便于对系统进行信息监控、信息统计、计算 PV、性能调优等。
*   通用处理：在应用程序中可能存在所有方法都要返回的信息，这是可以利用拦截器来实现，省去每个方法冗余重复的代码实现。

## 使用方法

实现 HandlerInterceptor 类，并且重写三个方法

*   preHandle：在 Controoler 处理请求之前被调用，返回值是 `boolean`类型，如果是`true`就进行下一步操作；若返回`false`，则证明不符合拦截条件，在失败的时候不会包含任何响应，此时需要调用对应的`response`返回对应响应。
*   postHandler：在 Controoler 处理请求执行完成后、生成视图前执行，可以通过`ModelAndView`对视图进行处理，当然`ModelAndView`也可以设置为 null。
*   afterCompletion：在 DispatcherServlet 完全处理请求后被调用，通常用于记录消耗时间，也可以对一些资源进行处理。

```
import lombok.extern.log4j.Log4j2;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@Log4j2
@Component
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("【MyInterceptor】调用了:{}", request.getRequestURI());
        request.setAttribute("requestTime", System.currentTimeMillis());
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response,
                           Object handler, ModelAndView modelAndView) throws Exception {
        if (!request.getRequestURI().contains("/online")) {
            HttpSession session = request.getSession();
            String sessionName = (String) session.getAttribute("name");
            if ("haixiang".equals(sessionName)) {
                log.info("【MyInterceptor】当前浏览器存在 session:{}",sessionName);
            }
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                Object handler, Exception ex) throws Exception {
        long duration = (System.currentTimeMillis() - (Long)request.getAttribute("requestTime"));
        log.info("【MyInterceptor】[{}]调用耗时:{}ms",request.getRequestURI(), duration);
    }
}
```

## Spring拦截器与Servlet过滤器的区别

1. 参考标准

*   过滤器是 JavaEE 的标准，依赖于 Servlet 容器，生命周期也与容器一致，利用这一特性可以在销毁时释放资源或者数据入库。
*   拦截器是SpringMVC中的内容，依赖于web框架，通常用于验证用户权限或者记录日志，但是这些功能也可以利用 AOP 来代替。

2. 实现方式

*   过滤器是基于回调函数实现，无法注入 ioc 容器中的 bean。
*   拦截器是基于反射来实现，因此拦截器中可以注入 ioc 容器中的 bean，例如注入 Redis 的业务层来验证用户是否已经登录。

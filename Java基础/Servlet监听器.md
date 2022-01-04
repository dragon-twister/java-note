[TOC]

# Servlet监听器

## 原理

Servlet监听器用于监听另一个 Java 对象的方法调用或属性改变，并触发响应的实现。 

相关概念：

*   事件：方法调用、属性改变、状态改变等。
*   事件源：被监听的对象（ 例如：request、session、servletContext）。
*   监听器：用于监听事件源对象 ，事件源对象状态的变化都会触发监听器。
*   注册监听器：将监听器与事件源进行绑定。

## 使用方法
### 重写监听器接口

Servlet 规范中定义了 8 个监听器接口，可以用于监听 ServletContext、HttpSession 和 ServletRequest 对象的生命周期和属性变化事件。开发 Servlet 监听器需要实现相应的监听器接口并重写接口中的方法。  

- Request

  ServletRequestListener (处理request对象创建和销毁)
  ServleRequestAttributeListener (处理域对象中的数据添加 替换 删除)

- Session

  HttpSessionListener (处理session对象创建和销毁)
  HttpSessionAttributeListener 处理session域对象中的数据添加 修改删除)
  HttpSessionBindingListener (处理session对象监听器绑定和解绑定接口)
  HttpSessionActivationListener (处理session对象钝化和活化状态接口)

- ServletContext

  ServletContextListener (处理application对象创建和销毁)
  ServletContextAttributeListener (处理application域对象中的数据添加 修改 删除)

### 注册 Servlet 监听器

监听器需要注册到事情源上，才能够在事件发生的时候调用。

JAVA中有两种注册方式：

1.   在 web.xml 中注册监听器；
2.   使用 @WebListener 注册监听器。



---
title: Spring MVC
author: Pengyuanbing
date: 2021-04-13 11:33:00 +0800
categories: [Spring, Spring MVC]
tags: [Spring, Spring MVC]
pin: true
---

## Spring MVC

<https://github.com/iokays/Samples/tree/main/spring_web>

从一个例子, 深入浅出 Spring MVC 的工作原理.


```java
@RestController
@SpringBootApplication
public class SpringMvcSample {

  @ApiOperation(value = "/", notes = "print hello")
  @GetMapping(value = "/")
  public String hello() {
    return "hello";
  }

  public static void main(String[] args) {
    SpringApplication.run(SpringMvcSample.class, args);
  }

}
```

这是一个简单的SpringBoot的WEB应用, 当我们访问<http://localhost:8080/> 页面显示.

```console
hello
```

SpringMvcSample::hello 这个方法在SpringMVC这中最终是以HandlerMethod存在. 我们再加一个功能, 可以验证HandlerMethod有哪些数据.


```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    private static final Logger logger = LoggerFactory.getLogger(WebConfig.class);

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HandlerInterceptor() {
            @Override
            public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
                if (handler instanceof HandlerMethod) {
                    HandlerMethod handlerMethod = (HandlerMethod) handler;
                    final ApiOperation apiOperation = handlerMethod.getMethodAnnotation(ApiOperation.class);

                    logger.info("handlerMethod: {}, apiOperation.notes: {}", handler, apiOperation.notes());

                }
                return true;
            }
        });
    }

}

```

再次请求 logger会打印下面的日志:

```console
: handlerMethod: com.iokays.web.boot.SpringMvcSample#hello(), apiOperation.notes: print hello
```

现在我画出了HandlerMethod的类图关系,和部分属性和方法.做了一些简单的解释.

![img_1.png](/assets/img/spring_mvc/handler_method.png)

我们从HandlerMethod的UML类图可以看到, 我们看到 Bean, Method, Parameters 等一些参数, 其实我们就可以调用invoke实现方法的执行操作. 但在HandlerMethod没有这样做.
只是对参数做了一些封装.

当我们在调试InvocableHandlerMethod::getMethodArgumentValues方法的时候, 会发现 HandlerMethodArgumentResolverComposite 这个对象, 这个对象提供了所有的参数解析器.
我们也简单的画出一些重要的UML类图.








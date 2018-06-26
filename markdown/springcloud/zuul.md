# zuul

1. ### 没有API网关时存在的问题

   - 运维人员需要维护路由规则与服务实例列表，随着服务数量的增加，维护难度也增加
   - 开发人员需要在每个微服务里面做认证和鉴权，修改的话会对每个服务都进行修改，增加出错概率

2. ### zuul的解决方案

   - 自身注册到eureka中，获取eureka注册中心的所有实例列表，通过以服务名作为ContextPath的方式来创建路由映射
   - 统一的前置过滤，对微服务统一进行认证和鉴权

3. ### zuul的入门

   - 加入eureka的依赖，会从eureka拉取服务列表
   - 进行面向服务的路由规则配置

   ```yml
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8761/eureka/
         
   zuul:
     routes:
       api-a:  #路由名称
         path: /api-a/**   #路由规则
         serviceId: hello-service  #需要路由的服务id
       api-b:
         path: /api-b/**
         serviceId: feign-consumer
   #等价于上面的配置
   zuul:
     routes:
       hello-service: /hello/**   # serviceId: path
       feign-consumer: /**
   ```

   - 服务路由的默认规则，在不进行配置的时候，默认的path会使用serviceId配置的服务名作为请求前缀，可以使用如下设置来忽略不希望对外开放的服务

   ```yml
    zuul:
     ignoredServices: '*' #忽略所有的默认规则
   ```

4. ### 路径匹配，路径匹配采用ant风格

   | 通配符 | 说明                             |
   | ------ | -------------------------------- |
   | ？     | 匹配任意单个字符                 |
   | *      | 匹配任意数量的字符               |
   | **     | 匹配任意数量的字符，支持多级目录 |

   简单实现配置

   | URL路径          | 说明                                        |
   | ---------------- | ------------------------------------------- |
   | /user-service/?  | 可以匹配/user-service/a,/user-service/b等   |
   | /user-service/*  | 可以匹配/user-service/a,/user-service/aaa等 |
   | /user-service/** | 可以匹配/user-service/a/b等                 |

   当出现这样的匹配时

   ```properties
   zuul.routes.user-service.path=/user-service/**
   zuul.routes.user-service.serviceId=user-service
   
   zuul.routes.user-service-ext.path=/user-service/ext/**
   zuul.routes.user-service-ext.serviceId=user-service-ext
   ```

   此时调用user-service-ext可能会匹配到/user-service/**，具体匹配到那个服务，有他们的配置顺序来决定的，此时使用yml格式来配置定义顺序比较合适

5. ### 忽略表达式

   ```yml
   zuul:
     routes:
       hello-service: /hello/**
       feign-consumer: /**
     ignored-patterns: /**/hello/**  #忽略所有的服务中的hello接口的访问
   ```

6. ### 路由前缀

   ```yml
   zuul:
     prefix: /api  #要避免路由的起始字符串和全局的路由前缀参数相同，参数相同在部分版本下存在在bug
   ```

7. ### 使用forward实现本地跳转

   ```yml
   zuul:
     routes:
       feign-consumer:
         path: /feign-consumer/**
         url: forward:/local            #需要在zuul服务上实现对应的接口，不推荐使用
   ```

8. ### Cookie和头信息

   cookie和一些头信息在zuul中默认是不会传递的，可以通过两个方式来进行设置

   ```yml
   #通过设置全局参数为空来覆盖默认值,不推荐的写法
   zuul:
     sensitive-headers: 
   #通过指定路由的参数；爱配置
   zuul:
     routes:
       <router>:
         sensitiveHeaders:   #将指定路由的敏感头设置为空
         
   zuul:
     routes:
       <router>:
         customSensitiveHeaders: true   #对指定路由开启自定义敏感头      
   ```

   

9. ### zuul默认已经集成hystrix和ribbon的功能，需要使用path和serviceId映射的方式才起作用

   ```yml
   #设置hystrix的超时时间
   hystrix:
     command:
       default:
         execution:
           isolation:
             thread:
               timeoutInMilliseconds: 5000
   #设置ribbon的连接超时时间
   ribbon:
     connectTimeout: 5000
   # 设置ribbon转发的请求超时时间
   ribbon:
     readTimeout: 5000
   #关闭重试机制，全局和指定路由
   zuul:
     retryable: false
     routes:
       <route>:
         retryable: false
   ```

10. ### 过滤器详解-4个抽象方法

  - ### filterType: 过滤器的类型，pre,rounting,post,error

  - ### filterOrder:  通过int值来定义过滤器的执行顺序，值越小优先级越高

  - ### shouldFilter:  确定过滤器是否要执行

  - ### run: 自定义的过滤逻辑

11. ### 默认pre过滤器

   |         过滤器         | 顺序 |                             说明                             |
   | :--------------------: | :--: | :----------------------------------------------------------: |
   | ServletDetectionFilter |  -3  | 检查请求是Spring的DispatchServlet处理运行的，还是ZuulServlet来处理运行的，一般/zuul/*之类的请求由ZuulServlet来处理 |
   | Servlet30WrapperFilter |  -2  |   将原始的HttpServletRequest包装成Servlet30RequestWrapper    |
   | FormBodyWrapperFilter  |  -1  | 1. 处理Content-Type为application/x-www-form-urlencoded的请求<br />2. 处理Content-Type为multipart/form-data并且是有spring的DispatchServlet处理的请求<br />3.将符合上述要求的请求包装成FormBodyRequestWrapper对象 |
   |      DebugFilter       |  1   | 会根据配置参数zuul.debug.request和请求中的debug参数来决定是否执行过滤器中的操作 |
   |  PreDecorationFilter   |  5   | pre的最后一个阶段，通过判断请求上下文是否存在forward.to和serviceId来执行具体过滤器的操作 |

12. ### 默认route过滤器

   |         过滤器          | 顺序 |                             说明                             |
   | :---------------------: | :--: | :----------------------------------------------------------: |
   |   RibbonRoutingFilter   |  10  | 只对请求上下文中存在serviceId参数的请求进行处理，通过使用ribbon和hystrix来向服务实例发起请求 |
   | SimpleHostRoutingFilter | 100  | 只对请求中上下文中存在routeHost参数的请求进行处理，即通过url配置路由规则的请求生效 |
   |    SendForwardFilter    | 500  | 只对请求上下文中存在forward.to参数的请求进行处理，即路由规则中的forward本地跳转配置 |

13. ### 默认post过滤器

   |       过滤器       | 顺序 |     说明     |
   | :----------------: | :--: | :----------: |
   |  SendErrorFilter   |  0   | 产生错误响应 |
   | SendResponseFilter | 1000 | 产生正常响应 |

14. ### 自定义过滤器的异常处理 

-  try-catch处理，在catch抛出ZuulRuntimeException即可
- 创建ErrorFilter处理
---
category: 后端
layout: post
tags:
  - java
  - springboot
  - aop
title: springboot应用之切面日志
date: 2020-09-12
aliases:
    - /2020/09/12/springboot应用之切面日志/
---


## 前言

<font color=#999AAA >利用**自定义注解**和**切面**为方法添加日志打印功能</font>





##  一、效果

```yaml
com.example.demo1.Aspet.LogAspect        : ------------------------------- start --------------------------
com.example.demo1.Aspet.LogAspect        : Method detail Description: login打印
com.example.demo1.Aspet.LogAspect        : Request Args: [{"email":"2419254437@qq.com","username":"fff"}]
com.example.demo1.Aspet.LogAspect        : Request method: POST
com.example.demo1.Aspet.LogAspect        : Request URL: http://localhost/user/login
com.example.demo1.Aspet.LogAspect        : Request Class and Method: com.example.demo1.controller.UserController.login
com.example.demo1.Aspet.LogAspect        : ------------------------------- End --------------------------
com.example.demo1.Aspet.LogAspect        : login打印，Response result  : {"email":"2419254437@qq.com","username":"fff"}
com.example.demo1.Aspet.LogAspect        : Time Consuming: 9906 ms
```

##  二、使用步骤
### 1. 引入相关依赖


```yaml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```

###  2. 创建自定义注解 `PrintlnLog`

```java
@Retention(RetentionPolicy.RUNTIME)  //指定注释要保留多长时间
@Target({ElementType.METHOD})  //对方法生效
@Documented //表明这个注解 由 javadoc记录的
public @interface PrintlnLog {

    /**
     * 自定义日志描述信息文案
     *
     * @return
     */
    String description() default "";
}

```
###  3. 创建切面 `LogAspect` 并指定切面入口

```java
@Slf4j
@Aspect
@Component //扫描不到应在启动类添加对应的 ComponentScan
public class LogAspect {
    private static final String LINE_SEPARATOR = System.lineSeparator();
    /**
     * 以自定义 @PrintlnLog 注解作为切面入口
     */
    @Pointcut("@annotation(com.example.demo1.Aspet.PrintlnLog)")
    public void PrintlnLog() {
    }

    
    @Before("PrintlnLog()")
    public void doBefore(JoinPoint joinPoint) throws Throwable {

        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();

        String methodDetailDescription = this.getAspectMethodLogDescJP(joinPoint);

        log.info("------------------------------- start --------------------------");
        /**
         * 打印自定义方法描述
         */
        log.info("Method detail Description: {}", methodDetailDescription);
        /**
         * 打印请求入参
         */
        //JSON 需引入依赖 fastjson
        log.info("Request Args: {}", JSON.toJSONString(joinPoint.getArgs()));

        /**
         * 打印请求方式
         */
        log.info("Request method: {}", request.getMethod());
        /**
         * 打印请求 url
         */
        log.info("Request URL: {}", request.getRequestURL().toString());

        /**
         * 打印调用方法全路径以及执行方法
         */
        log.info("Request Class and Method: {}.{}", joinPoint.getSignature().getDeclaringTypeName(), joinPoint.getSignature().getName());
    }

  
    @Around("PrintlnLog()")
    public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {

        String aspectMethodLogDescPJ = getAspectMethodLogDescPJ(proceedingJoinPoint);

        long startTime = System.currentTimeMillis();

        Object result = proceedingJoinPoint.proceed();
        /**
         * 输出结果
         */
        log.info("{}，Response result  : {}", aspectMethodLogDescPJ, JSON.toJSONString(result));

        /**
         * 方法执行耗时
         */
        log.info("Time Consuming: {} ms", System.currentTimeMillis() - startTime);

        return result;
    }

 
    @After("PrintlnLog()")
    public void doAfter(JoinPoint joinPoint) throws Throwable {
        log.info("------------------------------- End --------------------------" + LINE_SEPARATOR);
    }

    public String getAspectMethodLogDescJP(JoinPoint joinPoint) throws Exception {
        String targetName = joinPoint.getTarget().getClass().getName();
        String methodName = joinPoint.getSignature().getName();
        Object[] arguments = joinPoint.getArgs();
        return getAspectMethodLogDesc(targetName, methodName, arguments);
    }
    /**
     * @param proceedingJoinPoint
     * @author xiaofu
     * @description @PrintlnLog 注解作用的切面方法详细细信息
     * @date 2020/7/15 10:34
     */
    public String getAspectMethodLogDescPJ(ProceedingJoinPoint proceedingJoinPoint) throws Exception {
        String targetName = proceedingJoinPoint.getTarget().getClass().getName();
        String methodName = proceedingJoinPoint.getSignature().getName();
        Object[] arguments = proceedingJoinPoint.getArgs();
        return getAspectMethodLogDesc(targetName, methodName, arguments);
    }
    
    public String getAspectMethodLogDesc(String targetName, String methodName, Object[] arguments) throws Exception {
        Class targetClass = Class.forName(targetName);
        Method[] methods = targetClass.getMethods();
        StringBuilder description = new StringBuilder("");
        for (Method method : methods) {
            if (method.getName().equals(methodName)) {
                Class[] clazzs = method.getParameterTypes();
                if (clazzs.length == arguments.length) {
                    description.append(method.getAnnotation(PrintlnLog.class).description());
                    break;
                }
            }
        }
        return description.toString();
    }

}

```

###  4. 创建方法并在方法上添加自定义注解

```java
@PostMapping("/login")
    @PrintlnLog(description = "login打印")
    public User login(@RequestBody @Valid User user){
        return user;
    }
```

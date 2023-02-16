---
title: springmvc interceptor
---

[https://git.oschina.net/qiudeqing/springmvc-interceptor][1]

# 1. 实现HandlerInterceptor接口


    package com.qiudeqing.web.interceptor;

    import org.springframework.web.servlet.HandlerInterceptor;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    /**
     * Created by qiudeqing on 16/12/18.
     */
    public class HelloInterceptor implements HandlerInterceptor {


        @Override
        public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {

            System.out.println("HelloInterceptor.preHandle()");
            return true;
        }

        @Override
        public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
            System.out.println("HelloInterceptor.postHandle");
        }

        @Override
        public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
            System.out.println("afterCompletion.afterCompletion()");
        }
    }


# 2. 在spring.xml中配置拦截器


    <!-- 拦截器 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/index"/>
            <bean class="com.qiudeqing.web.interceptor.HelloInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>


[1]: https://git.oschina.net/qiudeqing/springmvc-interceptor

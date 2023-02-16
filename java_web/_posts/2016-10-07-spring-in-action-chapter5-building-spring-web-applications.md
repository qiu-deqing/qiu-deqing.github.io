---
title: spring in action 第五章: 创建Spring web app
---

准备工作: 创建空白web app并添加spring mvc依赖

# 配置Spring MVC


继承了`AbstractAnnotationConfigDispatcherServletInitializer`的类会自动用于配置`DispatcherServlet`和Spring application context.

从Servlet3.0开始, web容器在classpath下寻找实现了`javax.servlet.ServletContainerInitlializer`接口的类, 如果找到了这样的类, 就用于配置servlet容器.

Spring提供了一个叫做`SpringServletContainerInitializer`的实现, 这个类会寻找`WebApplicationInitlializer`的实现并把配置托管给它. Spring3.2提供了叫做`AbstractAnnotationConfigDispatcherServletInitializer`的实现.
我们的配置类继承了这个对象所以会被作为初始化配置对象.

`getServletMappings()`方法返回一个或多个`DispatcherServlet`被映射的路径, `/`表示app的默认servlet, 它将处理所有请求.

    package com.qiudeqing.config;

    import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

    /**
     * Created by qiudeqing on 16/10/18.
     */
    public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

        @Override
        protected String[] getServletMappings() {
            return new String[] { "/" };
        }

        @Override
        protected Class<?>[] getRootConfigClasses() {
            return new Class<?>[] { RootConfig.class };
        }

        @Override
        protected Class<?>[] getServletConfigClasses() {
            return new Class<?>[] { WebConfig.class };
        }
    }


WebConfig.java

    package com.qiudeqing.config;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.servlet.ViewResolver;
    import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
    import org.springframework.web.servlet.config.annotation.EnableWebMvc;
    import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
    import org.springframework.web.servlet.view.InternalResourceView;
    import org.springframework.web.servlet.view.InternalResourceViewResolver;

    /**
     * Created by qiudeqing on 16/10/18.
     */
    @Configuration
    @EnableWebMvc
    @ComponentScan("com.qiudeqing.web")
    public class WebConfig extends WebMvcConfigurerAdapter {

        @Bean
        public ViewResolver viewResolver() {
            InternalResourceViewResolver resolver = new InternalResourceViewResolver();
            resolver.setPrefix("/WEB-INF/views/");
            resolver.setSuffix(".jsp");
            resolver.setExposeContextBeansAsAttributes(true);
            return resolver;
        }

        @Override
        public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
            configurer.enable();
        }
    }


WebConfig继承WebMvcConfigurerAdapter并重写configureDefaultServletHandling()方法, 调用DefaultServletHandlerConfigurer的enable()方法. DispatcherServlet将我们没有处理的请求转发到静态资源.

RootConfig.java

    package com.qiudeqing.config;

    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.FilterType;
    import org.springframework.web.servlet.config.annotation.EnableWebMvc;

    /**
     * Created by qiudeqing on 16/10/18.
     */
    @Configuration
    @ComponentScan(basePackages = {"com.qiudeqing"},
            excludeFilters = {
                    @ComponentScan.Filter(type = FilterType.ANNOTATION, value = EnableWebMvc.class)
            }
    )
    public class RootConfig {
    }


# 5.3 接受请求参数

Spring支持以下方式传递参数：

- query 字符串
- form参数
- path参数


    package com.qiudeqing.web.controller;

    import com.qiudeqing.dao.SpittleRepository;
    import com.qiudeqing.model.Spittle;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.bind.annotation.RequestParam;

    import java.util.List;

    /**
     * Created by qiudeqing on 16/11/5.
     */
    @Controller
    @RequestMapping("/spittles")
    public class Spittlecontroller {
        private SpittleRepository spittleRepository;

        private static final String MAX_LONG_AS_STRING = "20";


        @Autowired
        public Spittlecontroller(SpittleRepository spittleRepository) {
            this.spittleRepository = spittleRepository;
        }


        @RequestMapping(method = RequestMethod.GET)
        public String spittles(
                @RequestParam(value = "max", defaultValue = MAX_LONG_AS_STRING) long max,
                @RequestParam(value = "count", defaultValue = "20") int count,
                Model model
            ) {
            model.addAttribute(spittleRepository.findSpittles(max, count));
            return "spittles";
        }

        @RequestMapping(value = "/{spittleId}", method = RequestMethod.GET)
        public String spittle(
                @PathVariable("spittleId") long spittleId,
                Model model
            ) {
            model.addAttribute(spittleRepository.findOne(spittleId));
            return "spittle";
        }
    }


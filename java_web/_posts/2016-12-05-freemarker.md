---
title: freemarker
---

[http://freemarker.org/docs/dgui_quickstart_template.html]()

# 输出数字自动格式化

`${number}`输出的数字会被格式化,使用`${number?c}`避免格式化

# 自定义时间输出格式

    ${seckill.startTime?datetime?string("yyyy-MM-dd HH:mm:ss")}     // 按指定格式输出

    ${seckill.startTime?long}       // 输出毫秒数

# HTML转义

freemarker默认执行转义`&`会输出`&amp;`

# 值缺失

freemarker值缺失会报错.

## 默认值

    ${name!"visitor"}

如果name缺失,将使用visitor

## 检测是否存在

    <#if name??>Hello ${name}</#if>

如果值存在,输出欢迎

# 值输出

    ${product.url}

# 判断

    <#if user == "Big Joe">
        Hello Leader
    <#elseif user == "small Joe">
        Hello small leader
    <#else>
        hello boy
    </#if>

# 列表

    <#list animals as animal>
        <div>${animal.name} - ${animal.price}</div>
        <#sep>,</#sep>
    <#else>
        no animal
    </#list>

判断最外层

    <#list animals>
        <p>Animals:</p>
        <ul>
            <#items as animal>
                <li>${animal.name}</li>
            </#items>
        </ul>
    <#else>
        No animals
    </#list>

# 包含文件

    <#include "common/footer.html">

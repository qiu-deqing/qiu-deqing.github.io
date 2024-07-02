---
title: "[译]CSS最佳实践"
---

[原文][1]

## Namespaces

组件**必须**设置唯一的命名空间做为前缀

- 命名空间必须短并且有意义
- 用`-`分隔命名空间和元素
- 视图应该由多个单独组件构成

比如一个**drop down list**组件使用`ddl`作为命名空间:

**Good**

```
.ddl-container {}

.ddl-item-list {}

.ddl-item {}
```

**Bad**

```
.item-list {}

.dropdown-item-list {}

.xyz-item-list {}
```

通用的类提供可复用样式应该放在单独命名空间如`uv`

**Good**

```
.uv-clearfix {}
```

**Bad**

```
.clearfix {}
```

## Classes

类名应该遵循以下规则

- 必须小写
- 用`-`分隔词
- **尽量短**, 缩写时注意
- 命名一致性
- 语义化描述元素
- 低于4个单词

**Good**

```
.ddl-item {}

.ddl-selected {}

.ddl-item-selected {}
```

**Bad**

```
.ddlItem {}


.ddl-item-container-text {}

.ddl-foo-bar-baz {}
```

## Attributes

属性选择器经常带来好处, 基础规则:

- 如果只检查属性是否设置可以满足要求, 就只检查属性是否设置
- 不要使用标签过分修饰

**Good**

```
[href] {}
```

**Bad**

```
a[href] {}

[href^='http'] {}
```

## `id` 选择器

**禁止**使用id选择器

- id选择器不可复用
- 优先级噩梦

**Good**

```
.ur-name {}
```

**Bad**

```
#ur-name {}
```

## Tag Names

标签选择器使用应遵循以下规则

- 需要在整个应用级别上覆盖元素默认样式时可以使用
- **尽量使用类代替标签选择器**
- 如果在同一个命名空间下有很多元素需要使用相同的样式, 可以使用标签选择器
- **不要过分修饰**: `a.foo`

**Good**

```
button {
  padding: 5px;
  margin-right: 3px;
}

.ddl-button {
  background-color: #f00;
}
```

**Bad**

```
.ddl-container button {
  background-color: #f00;
}
```

## selectors and nesting

选择器嵌套不应该超过三级

**Good**

```
.sg-title-icon:before {}

.dg-container .sg-title {}
```

**Bad**

```
.dg-container .sg-container .sg-title {}

.dg-container .sg-title span:before {}
```

如果一个组件需要与另外一个地方的同种组件不一样

- 如果可能, 在子组件的类名里面加上父组件命名空间
- 如果上面一条无法满足, 使用嵌套选择器


假设你有一个用户列表组件`.ul-*`和一个用户卡片组件`.uc-*`

**Good**

```
<div class="ul-container">
  <div class="uc-container">
    <span class="uc-name ul-card-name">John Doe</span>
  </div>
</div>
```

```
.ul-card-name {}
```

**Okay**

```
<div class="ul-container">
  <div class="uc-container">
    <span class="uc-name">John Doe</span>
  </div>
</div>
```

```
.ul-container .uc-name {}
```

**Bad**

```
<div class="ul-container">
  <div class="uc-container">
    <span class="uc-name uc-name-in-ul">John Doe</span>
  </div>
</div>
```

```
.uc-name-in-ul {}
```

## Organization

完美状态下应该将所有样式保存在单独文件中

- 使用build工具将他们合并成一个再发布

注意规则:

- 每个组件应该在一个单独文件
- 对全局标签应用的样式应该放在一个单独文件
- 尽量**分开展现样式**和**布局样式**

## Presentation-Specific vs Layout-Specific Styles

展现相关样式是指**只影响元素视觉效果**的样式, **不改变元素尺寸和位置 in a meaningful way**. 如:

- 类似`color`, `font-weight`, `font-variant`
- 动画,渐变规则
- `font-size` is not considered a meaningful dimension change
- 设置`box-sizing: border-box;`情况下的`padding`
- `max-width`, `max-height`同时可以属于两个类别, 更偏向于归为显示

布局相关样式**修改元素尺寸和位置**

- 类似`margin`, `padding`
- `width`, `height`
- `position`
- `z-index`

[1]: https://github.com/bevacqua/css

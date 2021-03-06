# 09 WGSL基础之函数和入口函数

**在 WGSL 中函数分为内置函数和用户自定义函数，入口函数属于一种特殊的用户自定义函数，入口函数会被着色器调用。**



<br>

#### 用户自定义函数

> 用户自定义函数英文是：user-defined function

我们先学习一下用户自定义函数相关知识点，和 JS 中的 函数在定义和使用方面还是有一些差异的。



<br>

**如何定义函数？**

在 WGSL 中使用 `fn` 来作为创建函数的关键词

> 相当于 JS 中的 function



<br>

一个典型的函数应该包含以下几部分：

1. 函数名

2. 函数的参数以及参数类型

3. 使用 `->` 来表示此函数有返回值

   > 注意并不是 JS 中的箭头函数 `=>`

4. 函数返回值的类型



<br>

函数有返回值的简单示例：

```
fn addTwo( a: i32, b:f32) -> i32 {
    return a + b;
}
```



<br>

假设函数没有返回值，那么可以写成：

```
fn myFun() {
    ...
}
```



<br>

**停止执行函数：**

当执行语句中出现了 `discard` ，则会立即停止函数的调用和执行，并且无法恢复。

> discard 关键词在上一节 “WGSL基础之控制语句” 中学习过。
>
> discard 只能用于 片元 着色器阶段，表明立即结束当前片元着色器的执行，并丢弃当前的片元数据。



<br>

**特别注意：在WGSL 中不允许出现 递归函数**

也就是说不允许函数之间循环调用。



<br>

#### 入口函数(entry point)

入口函数属于一种特殊的用户自定义函数。

入口函数是指在管线的 计算(compute)、顶点(vertex)、片元(fragment) 这 3 个阶段中，WGSL 会去调度执行的函数。



<br>

**指明入口函数属于哪个阶段的关键词分别是：@vertex、@fragment、@compute**

简单示例：

```
@vertex
fn vert_main() -> ...

@fragment
fn frag_main() -> ...

@compute
fn comp_main() { ... }
```

> 通常情况下 顶点入口函数会返回和空间顶点坐标相关的值，而片元入口函数则返回和颜色相关的值。



<br>

**补充说明：已被废弃的声明方式**

在早些时候，使用的是 `@stage(vertex)`、`@stage(fragment)`、`@stage(compute)` 这种形式来定义入口函数属于哪个阶段。

不过这种方式已被废弃，现在使用刚才提到的 `@vertex`、`@fragment`、`@compute` 。

> WebGPU 和 WGSL 目前还处于草案当中，很多语法都在不断变化中。



<br>

实际上本文到此就应该结束了。但是在入口函数中，还有一个非常重要的概念：插值

可是关于插值的相关知识，我也不是特别懂。

以下内容为我在查阅一些文章后 有限的理解，请勿完全相信，因为我也是处于学习中，如果讲错了还请理解。



<br>

#### 插值

插值，即如何控制用户定义的输入和输出数据。

插值主要分为 2 个方面：

1. 插值类型
2. 插值的采样方式



<br>

**插值类型**

1. perspective(默认)：值以透视的方式进行插值计算

2. linear：值以线性、非透视的方式进行插值计算

3. flat：值不是内插的。

   > 以展开(扁平化) 2D 平面的方式进行插值计算



<br>

**插值的一些名词：内插、外插、间插**

> 我也不是很懂，百度了一些文章之后，大概明白了一些

1. 图像内插、外插 是 2 种不同的图像插值计算方式，其作用是将 1 张低分辨率的图像转化为 1 张较高分辨率的图像。

   > 也就是说将 1 张小图 拉大变成 1 张大图

2. 图像间插：就是将 2 张不同的图像通过计算得到处于它们 2 者中间过渡的那 1 张新的图像。

   > 也就是说计算出 2 张图片中间的 补间 图像



<br>

在 CSS 中有一个类似的样式：transform-style

> 我只是说有些相似，但和本文所学习的并没有任何关联

它有 2 个配置值：

1. preserve-3d：指示元素的子元素位于该 元素的 3D 空间中

   > 元素的子元素将会有 3D 遮挡关系

2. flat(默认值)：指示元素的子元素位于该 元素的 2D 平面中

   > 元素的子元素将不会有 3D 遮挡关系



<br>

**插值的采样方式：**

> 在有些图形学文章中会将 “插值的采样方式” 称呼为 “插值修饰符”

1. center(默认)：在像素的中心执行

2. centroid：(实在是没有理解官方文档中关于 centroid 的介绍)

   > centroid 单词本意为 “质心”

3. sample：对每一个样本执行内插，每个样本调用一次 片元着色器



<br>

如果插值类型设置为 flat，则不得指定插值采样方式。

> 换句话说，只有将插值类型设置为 perspective 或 linear 时才需要设置 插值的采样方式



<br>

1. 如果未指定插值类型，则默认为 perspective
2. 如果未指定插值的采样类型，则默认为 center。



<br>

关于插值就先讲到这里，等以后更加深入学习，再来填坑。



<br>

本文到此结束，下一节我们学习 WGSL 中的内置值和内置函数，学完之后就完成了对 WGSL 基础部分的学习。
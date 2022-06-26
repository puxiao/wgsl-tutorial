# 06 WGSL基础之变量声明

**在WGSL中，变量声明相关的 4 个关键词为：var、let、const、override**

**变量修饰符相关的 4 个关键词为：private、workgroup、uniform、storage**

**和全局变量相关的 2 个关键词为：group、binding**



<br>

#### 变量声明相关：var、let、const、override

能够想象这 4 个关键词的用法应该和 TypeScript 中的用法差不多。

> 由于 JS 并非面对对象语言，所以在 JS 中没有 override(重写/覆盖) 的概念，但是 TypeScript 中是可以使用 override 的。



<br>

实际情况是 var/let/const/override 他们的用法和 JS 中的用法确实非常相似，只是在某些细节上略微不同。



<br>

**let**

```
let mysize: i32 = 1024
```

let 只能出现在函数内部中。

let 声明变量时，其对应的 `类型` 就会固定下来，且以后也无法改变。

这句话隐含了 2 个事情：

1. let 声明变量时其值为必填项

2. let 声明变量时，假设没有明确定义其类型，WGSL 则会根据其值来推导出该变量是什么类型。

   因此 `let mynum = 1` 与 `let mynum = 1.0` 推导出 mynum 的类型是不一样的



<br>

**override**

1. WGSL 并不是面对对象语言，也没有 类(class) 的概念，所以本文提到的 override(覆盖声明) 并不是指去覆盖父类中的某个变量。

2. WGSL 中的 override 是指 `覆盖管道创建方法指定的值`

3. 假设管道中并未指定有该被覆盖的值，此时 override 相当于初始化该变量的值

   > 换句话说，override 至少需要指明类型 或者 具体的值，(具体的值可以反向推理出它是什么类型)

4. override 只能出现在 模块作用域 中

   > 换句话说，override 不应该出现在 函数中



<br>

**const**

1. const 的用法 和 在 JS 中相同，用于声明类型和值都不能再改变的常量。



<br>

**var**

1. var 的用法 和 在 JS 中相同

2. var 声明变量时允许先不赋值，例如 `var i: i32;`，此时默认其值为 0

   > 尽管默认可以不赋值，但是这样做法并不被推荐，最好还是在声明时明确其初始值



<br>

#### 变量修饰符：private、workgroup、uniform、storage

接下来讲解一下变量修饰符，如果你只是会 JS ，那么这个变量修饰符似乎对你略微有点难以理解。

因为在 JS 中某个对象的所有属性都是可访问的，根本不存在 可访问 类型定义。

> 当然目前 JS 类(Class)的定义中，声明变量时可以通过添加 `#` 的形式来将该属性变为私有。



<br>

如果你本身会 TypeScript ，那么也得认真看一下，因为 WGSL 中的变量修饰符和传统面对对象中的修饰符不太一样。



<br>

**变量修饰符，就是在声明变量之前明确该变量的作用域以及可访问性。**

**WGSL中的变量修饰符关键词有：private、workgroup、uniform、storage**



<br>

在了解这些变量修饰符之前，有必要先回顾一下在之前 `WGSL基础之普通类型` 那篇文章中的 `地址空间` 知识点。

| 存储地址  | 可访问性           | 释义             |
| --------- | ------------------ | ---------------- |
| function  | 读、写             | 同一函数内       |
| private   | 读、写             | 同一模块内       |
| workgroup | 读、写             | 着色器工作组内   |
| uniform   | 读                 | 相同的着色器阶段 |
| storage   | 读(默认仅为读)、写 | 相同的着色器阶段 |
| handle    | 读                 | 相同的着色器阶段 |

> 注意：上述中的 handle 目前为 WGSL 的保留关键词，实际中还未被真正投入使用。



<br>

**private：**

明确该变量仅在当前模块下可以访问。

示例：`var<private> mynum: f32;`



<br>

**workgroup：**

表明该变量在相同的 workgroup(着色器工作组) 中可以访问。

示例：`var<workgroup> worklist: array<i32,10>;`



<br>

**uniform 与 storage：**

他们都表明该变量在相同的 着色器阶段(shader stage) 下可以访问。

不同点在于：

1. uniform：字面意思为 “统一”，即不可修改，其值为固定值
2. storage：字面意思为 “存储”，即可读也可写，其值可以修改



<br>

**storage的特别补充：**

1. 假设我们的代码为 `var<storage>`，它相当于 `var<sotrage,read>` 此时仅为只读模式
2. 若想变为可读可写，则代码需要修改为 `var<storage,read_write>`，此时 storage 声明的变量才是可读可写。



<br>

接下来的内容并不是换了个话题，而是对变量修饰符做了一个知识延展。



<br>

在 WGSL 中 “资源接口” 包含：统一缓冲区、存储缓冲区、纹理、采样器



<br>

**统一缓冲区 和 存储缓冲区**

1. 多个使用 `var<uniform>` 生成的变量汇聚到一起，形成了 统一缓冲区(uniform buffers)
2. 多个使用 `var<storage>` 生成的变量汇聚到一起，形成了 存储缓冲区(storage buffers)



<br>

WGSL 中的纹理和采样器相关知识我们还没学习，不过因为学习过 WebGPU，所以 纹理和采样器 我假定你都知道他们是做什么的。

> 如果你是纯小白，对 纹理和采样器 的概念都不清楚，那么你可以去看我之前写的 WebGPU 系列教程，里面有相关的介绍。



<br>

**强调一下：在 WGSL 中对于纹理和采样器资源，无需使用变量修饰符**

为什么无需使用？

这里我猜测是因为 纹理 和 采样器 属于 WGSL 核心资源，WGSL 本身对它们 2 个有自己特定的访问、读写、存储规范，因此无需(也没必要) 我们像对待普通变量那样给它们增加变量修饰符。



<br>

#### 和全局变量相关的关键词：group、binding

WGSL 中定义了可应用于全局变量的 2 个关键词：group、binding

这 2 个关键词牵扯到 资源接口 中的一些概念，我们此刻只是知道有这 2 个关键词即可，不做深究。

我们只需要有个大体印象，

1. group：对应着色器工作组，
2. binding：对应管线布局

在使用它们 2 个时，还需要明确它们对应的 编号(序列号)数值：

1. 例如 `group(0)`
2. 例如 `binding(1)` 或 `binding(2)` 



<br>

>关于 编号(序列号) 的含义，需要查看 WebGPUPipelineLayout(管线布局) 相关知识才可以容易理解。



<br>

**示例代码：**

```
struct Params {
  specular: f32,
  count: i32
}
@group(0) @binding(2)
var<uniform> param: Params


@group(0) @binding(0)
var<storage,read_write> pbuf: array<vec2<f32>>


@group(0) @binding(1)
var filter_params: sampler
```



<br>

**本文知识点回顾：**

1. WGSL 中声明变量的关键词有：let、const、var、override

2. WGSL 中变量修饰符关键词有：private、workgroup、uniform、storage

   > 纹理和采样器 无需使用修饰符

3. WGSL 中全局变量相关的关键词有：group、binding



<br>

本文到此结束，接下来要学习 表达式。
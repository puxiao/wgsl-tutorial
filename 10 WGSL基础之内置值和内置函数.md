# 10 WGSL基础之内置值和内置函数

**在 WGSL 中存在一些内置值和内置函数，这些内置值和内置函数是我们着色器编程中最为重要的核心。**



<br>

#### 内置值

内置值按照名称、所处管线阶段、属于输入还是输出，以及该内置值的类型，统计如下：

| 名称                   | 阶段 | 输入或输出 | 类型        |
| ---------------------- | ---- | ---------- | ----------- |
| vertex_index           | 顶点 | 输入       | u32         |
| instance_index         | 顶点 | 输入       | u32         |
| position               | 顶点 | 输出       | `vec4<f32>` |
| position               | 片元 | 输入       | `vec4<f32>` |
| front_facing           | 片元 | 输入       | bool        |
| frag_depth             | 片元 | 输出       | f32         |
| sample_index           | 片元 | 输入       | u32         |
| sample_mask            | 片元 | 输入       | u32         |
| sample_mask            | 片元 | 输出       | u32         |
| local_invocation_id    | 计算 | 输入       | `vec3<u32>` |
| local_invocation_index | 计算 | 输入       | u32         |
| global_invocation_id   | 计算 | 输入       | `vec3<u32>` |
| workgroup_id           | 计算 | 输入       | `vec3<u32>` |
| num_workgroups         | 计算 | 输入       | `vec3<u32>` |



<br>

目前我们仅仅处于 WGSL 基础理论知识学习过程中，上述内置值的具体使用，以后都会慢慢使用到的。

此刻我们只是简单对它们有一个初步印象即可。



<br>

上述内置值对应的含义为：

> 顺序和上面那个表格相同

| 名称                   | 描述                                          |
| ---------------------- | --------------------------------------------- |
| vertex_index           | 当前顶点的索引                                |
| instance_index         | 当前顶点的实例索引                            |
| position               | 当前顶点的输出位置                            |
| position               | 当前片元在帧缓冲区中的位置                    |
| front_facing           | 当前片段位于正面图元上时为 true，否则为 false |
| frag_depth             | 片段的深度                                    |
| sample_index           | 当前片段的样本索引                            |
| sample_mask            | 当前片元(输入)的样本覆盖掩码                  |
| sample_mask            | 当前片元(输出)的样本覆盖掩码                  |
| local_invocation_id    | 当前执行单元在本地工作组中的索引              |
| local_invocation_index | 当前执行单元在本地工作组中的偏平形式的索引    |
| global_invocation_id   | 当前执行单元在全局工作组中的索引              |
| workgroup_id           | 当前调用的工作组 ID                           |
| num_workgroups         | 工作组的数量                                  |

> 以上内置值需要经过大量实际代码练习使用，才会真正理解其含义，目前大致有个初步印象即可。



<br>

#### 内置函数

首先强调一点，WGSL 的内置函数都有一个特性：重载(overload)。

简单来说就是 **同一个函数名，参数的数据类型不同，其对应的含义相似却又略微不同。**

> 等后面看几个例子就明白 重载 的含义了。



<br>

**内置函数的类型：**

WGSL 中的内置函数分为以下 12 个分类：

1. 逻辑相关的内置函数
2. 数组相关的内置函数
3. 浮动数相关的内置函数
4. 整数相关的内置函数
5. 矩阵相关的内置函数
6. 向量相关的内置函数
7. 导数相关的内置函数
8. 纹理相关的内置函数
9. 原子相关的内置函数
10. 数据打包相关的内置函数
11. 数据解包相关的内置函数
12. 同步相关的内置函数



<br>

**第1种：逻辑相关的内置函数：**

和逻辑相关的内置函数一共有 3 个，它们分别是：all、any、select

> 简单来说可以把这 3 个看作是某些 if 条件语句的简写形式
>
> 在 Python 中也有这些函数的用法



<br>

首先说一下，这 3 个内置函数的参数都可以是以下 2 种形式：

1. `e: vecN<bool>`：某维度下的向量，且每个分量都为 bool 类型
2. `e`：某个可迭代对象 e 的所有元素都为 bool 类型



<br>

那么，以上 3 个逻辑内置函数的用法为：

1. `all(e:vecN<bool>)`：当 e 的所有分量都为 true 时，其运行结果才为 true

2. `all(e)`：当 e 的所有可迭代属性值都为 true 时，其运行结果才为 true

3. `any(e:vecN<bool>)`：当 e 的任何一个分量为 true 时，其运行结果就为 true

4. `any(e)`：当 e 的任意一个可迭代属性值为 true 时，其运行结果就为 true

5. `select(f:T, t:T, cond:bool)`：当 cond 参数为 true 时，其运行结果返回 t，否则返回 f

   > select() 和 三元运算 有些类似，只不过是先写 flase 结果值，后写 true 结果值

6. `select(f:vecN<T>, t:vecN<T>, cond:vecN<bool>)`：将 cond 的每一个分量都进行一次 select() 计算，并返回最终的结果



<br>

**第2种：数组相关的内置函数**

数组相关的只有一个内置函数：arrayLength

其含义也非常简单，就是返回数组中元素的数量。



<br>

**第3种：浮动数相关的内置函数**

浮动数相关的内置函数，简单来说相当于 JS 中 Math 提供的很多计算相关的函数。

例如：

1. abs()：求绝对值
2. cos()：求余弦
3. acos()：求反余弦值
4. ceil()：求上限(整数)
5. max()：求参数中的最大值
6. ...



<br>

除了像 JS 中 Math 所能提供的二维几何相关的计算函数外，WGSL 还提供了浮动数其他的一些函数。

例如：

1. faceForward(e1, e2, e3)：如果  e2 与 e3 点乘 结果为负则返回 e1，否则返回 -e1

   > 点乘也叫 内积，是线性代数 中的知识点，用于计算 2 个向量之间的夹角

2. distance(e1, e2)：计算两个空间坐标的距离

3. fract(e)：返回 e 的小数点后的数值

4. cross(e1,e2)：返回 3维向量 e1 和 e2 的叉乘

5. ...

> 函数太多，就不逐一介绍了，更多的请查阅 WGSL 官方文档。



<br>

**第4种：整数相关的内置函数**

整数相关的内置函数，不少都与 向量的分量 有关。

例如：

1. clamp()：限制分量的取值范围

   > 有些文章会把 clamp 翻译为 “收窄”，个别文章会翻译为 “夹紧”

2. shiftRight()：逻辑右移

3. reverseBits()：反转参数中的位

4. ...



<br>

> 同样，因为函数众多，就不逐一介绍了。



<br>

**第5种：矩阵相关的内置函数**

一共有 2 个：

1. determinant(e)：返回 e 的行列式
2. transpose(e)：返回 e 矩阵转置后的结果



<br>

**第6个：向量相关的内置函数**

只有 1 个：dot()，用于计算参数的 点乘。

> 点乘又被称为 点积、内积，其运算结果为一个向量在另外一个向量上的投影。



<br>

> 有点乘(dot)，但为什么没有 叉乘(cross)？
>
> 实际上 叉乘(cross) 被归类到了 `第3种：浮动数相关的内置函数`



<br>

**第7个：导数相关的内置函数**

先解释一下什么叫导数和偏导数。



<br>

> 以下内容来源于百度百科

<br>

**导数**(derivative)也叫 导函数值，又名 微商，是微积分中的重要基础概念。

> 这里的 微商 可不是卖货的那个微商

导数是函数的局部性质。一个函数在某一点的导数描述了这个函数在这一点附近的变化率。



<br>

**偏导数**(partial derivative)：在数学中，一个多变量的函数的偏导数，就是它关于其中一个变量的导数而保持其他变量恒定。偏导数在向量分析和微分几何中是很有用的。

> 我大学也学过微积分，可是忘得一干二净了，微积分对我来说是天书。



<br>

有偏导数，那自然有对应的 全导数。

**全导数：......**

> 不想听



<br>

总之记住以下 2 点：

1. 在 WGSL 中 偏导数 只能应用在 片元 着色器阶段

   > 用于相邻片元操作的片段着色器调用协作中

2. 只能在 uniform control flow 中被调用

   > uniform control flow 从字面上应该翻译为：统一控制流



<br>

在 WGSL 中一共存在以下几种 导数 相关的内置函数：

1. dpdx(e:T) -> T：e 的偏导数对应窗口的 x 坐标
2. dpdxCoarse(e:T) -> T：使用局部差异，返回 e 的偏导数相对于窗口 x 的坐标
3. dpdxFine(e:T) -> T：返回 e 的偏导数相对于窗口 x 坐标
4. dpdy(e:T) -> T：e 的偏导数对应窗口的 y 坐标
5. dpdyCoarse(e:T) -> T：使用局部差异，返回 e 的偏导数相对于窗口 y 的坐标
6. dpdyFine(e:T) -> T：返回 e 的偏导数相对于窗口 y 坐标
7. fwidth(e:T) -> T：相当于返回 abs(dpdx(e) + abs(dpdy(e)))
8. fwidthCoarse(e:T) -> T：相当于返回 abs(dpdxCoarse(e) + abs(dpdyCoarse(e)))
9. fwidthFine(e:T) -> T：相当于返回 abs(dpdxFine(e) + abs(dpdyFine(e)))



<br>

**第8个：纹理相关的内置函数**

纹理可能是我们打交道最多的对象，但是本文不过多介绍，因为 纹理 太重要了，以后我们再慢慢展开。

1. textureDimensions：返回纹理的尺寸，或以 纹素(texels) 为单位的纹理的 mipmap 级别

   > dimensions 单词本意为：规模、大小

2. textureGather：返回一个四元素向量，其分量是从选定的纹素指定的通道中提取的

   > gather 单词本意为：收集

3. textureGatherComparse：对深度纹理中的四个纹素进行深度比较，并返回比较结果的四元素向量

   > comparse 单词本意为：比较、对比

4. textureLoad：从纹理中读取单个纹素，无需采样或过滤

5. textureNumLayers：返回数组纹理的层数

6. textureNumLevels：返回纹理的 mipmap 级别数

7. textureNumSamples：返回多重采样纹理中每个纹素的样本数

8. textureSample：采样纹理，只能在 片元 着色器阶段使用、只能在 统一控制流(uniform control flow) 中调用

9. textureSampleBias：对具有 mipmap 级别偏差的纹理进行采样，只能在 片元着色器阶段和 统一控制流中调用

10. textureSampleCompare：对深度纹理进行采样，并将采样的深度值与参考值进行比较，只能在 片元着色器阶段和 统一控制流中调用

11. textureSampleCompareLevel：对深度纹理进行采样，并将采样的深度值与参考值进行比较

    > 注意：textureSampleCompare 和 textureSampleCompareLevel 函数几乎相同，只不过 textureSampleCompareLevel 总是从 mipmap 0 级别开始采样纹理，并且可以在任何着色器阶段调用。

12. textureSampleGrad：使用显式渐变对纹理进行采样

13. textureSampleLevel：使用显式 mipmap 级别 或 mipmap 级别 0 对纹理进行采样

14. textureStore：将单个纹素写入到纹理中



<br>

**第9个：原子相关的内置函数**

先复习一下什么叫 原子类型。

> 关于 原子类型 在之前的 `WGSL基础之普通类型` 中简单讲解过。



<br>

在高并发程序中往往是多个线程要同时访问和操作某一个数据，这就需要解决一个问题：如何保护和同步共享数据。一般情况下都是通过 自旋锁、互斥锁 来解决的。



<br>

原子操作：多个共享内存的线程能够同时读写同一位置上的数据。原子操作会确保正在读或写的数据的值是符合预期的，简单来说就是下一个原子操作一定会在上一个原子操作结束后才开始。



<br>

在这些解决方案中，就存在一种 “永远无法再分割” 的数据类型概念，这种类型就被称之为 “原子类型”。



<br>

> 在化学中，原子是指化学反应不可再分的基本微粒。
>
> 在物理学中，原子由原子核和绕核运动的电子组成，尽管原子核与电子可以再次分割，但分割所需要的力量是非常巨大的。



<br>

原子的英文单词是 atomic，所以在 WGSL 中也是用 atomic 这个关键词来定义原子类型。

原子类型的书写模板为：`atomic<T>`，其中 T 的类型只能是 u32 或 i32



<br>

**并不是只有在 WGSL 中才有原子类型，在原生 JS 中也有：atomics**

具体可以查看：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Atomics



<br>

原子内置函数的几个规定：

1. 不能在 顶点(vertex) 着色阶段使用
2. 内置函数的参数 atomic_ptr 的存储类 SC 必须是 storage(相同着色阶段) 或 workgroup(同一工作组)
3. 内置函数的参数 A (访问模式) 必须是 读与写(read_write)



<br>

下面是 原子 相关的 2 个内置函数：

1. `atomicLoad(atomic_ptr: ptr<SC, atomic<T>,A>) -> T`：原子负载
2. `atomicStore(atomic_ptr:ptr<SC,atomic<T>,A>,v:T)`：原子商店



<br>

下面是和 原子 读-修改-写 相关的内置函数：

1. atomicAdd()：相加
2. atomicSub()：相减
3. atomicMin()：取小
4. atomicAnd()：相与
5. atomicOr()：相或
6. atomicXor()：异或
7. atomicExchange()：替换并返回旧值

> 以上几个原子内置函数的参数和返回值都相同，都是：`(atomic_ptr:ptr<SC,atomic<T>,A>,v:T) -> T`



<br>

以上几个原子内置函数，在 JS 的 atomics 中也有类似的函数。



<br>

`atomicCompareExchangeWeak(atomic_ptr: ptr<SC, atomic<T>, A>,cmp:T,v:T) -> __atomic_compare_exchange_result_<T>`

> 求放过



<br>

**第10个：数据打包相关的内置函数**

这里的 “数据打包” 是指针对 WGSL 中某类型的数据格式进行编码，以便程序可以将打包后的数据密集写入内存，从而减少着色器的内存带宽需求。

1. `pack4x8snorm(e:vec4<f32>) -> u32`：将4个标准化浮点值转化为8位有符号的整数，然后将他们组合成一个 u32 值
2. `pack4x8unorm(e:vec4<f32>) -> u32`：将4个标准化浮点值转化为8位无符号的整数，然后将他们组合成一个 u32 值
3. `pack2x16snorm(e:vec2<f32>) -> u32`：将2个标准化浮点值转化为16位有符号的整数，然后将他们组合成一个 u32 值
4. `pack2x16unorm(e:vec2<f32>) -> u32`：将2个标准化浮点值转化为16位无符号的整数，然后将他们组合成一个 u32 值
5. `pack2x16float(e:vec2<f32>) -> u32`：将2个浮点值转化为半精度浮点数，然后将他们组合成一个 u32 值



<br>

**第11个：数据解包相关的内置函数**

数据解包 是和 数据打包 刚好相反的一个过程，即程序从内存中读取许多密集打包的值，从而减少着色器的内存带宽需求。

数据解包相关的内置函数 和 数据打包内置函数相似，只不过：

1. 数据打包内置函数名都是以 `pack...` 为开头
2. 而数据解包内置函数名是以 `unpack...` 为开头

他们分别是：unpack4x8snorm()、unpack4x8unorm()、unpack2x16snorm()、unpack2x16unorm、unpack2x16float()



<br>

**第12个：同步相关的内置函数**

所有同步函数只能在 计算(compute) 着色器阶段使用。

WGSL 中有 2 个同步相关的内置函数：

1. storageBarrier()：存储屏障，影响 存储类中的内存和原子操作
2. workgroupBarrier()：工作组屏障，影响 工作组 存储类中的内存和原子操作

> barrier 单词本意为：障碍、屏障、阻力、隔阂、关卡、分界线



<br>

至此，关于 WGSL 中的 12 个方面的内置函数讲解完毕。

只需有一些大概印象即可。



<br>

本文学习了 WGSL 中的 内置值 和 内置函数。

同时，本文也是 WGSL 基础系列教程中最后一篇文章了。



<br>

**总结：**

通过一系列 WGSL 基础知识的学习，对 WGSL 有了一些底层的基础印象，虽然我们现在还不会熟练编写 WGSL 代码，但是有了这些基础后，再往下深入学习和实际应用就比较容易了。

至少从心里上对 WGSL 不再感到那么神秘和难易理解，也更容易看懂别人写的 WGSL 代码了。



<br>

加上之前对于 WebGPU 的学习，我们已经入门了 WebGPU + WGSL。

**WebGPU 和 WGSL 基础系列教程的结束，恰恰是全新的、深入的实际开发之路的开始。**



<br>

接下来，我将筹划下一阶段的学习目标和教程。

**加油，自己。**
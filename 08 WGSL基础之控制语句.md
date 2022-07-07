# 08 WGSL基础之控制语句

**和任何一门编程语言类似，WGSL 中支持常见的各种控制执行顺序的控制语句(statements)，例如 if/switch/loop/for/while 等等。**



<br>

在学习 控制语句 前，先学习一下：

1. 赋值语句
2. 递增和递减



<br>

> 尽管简单，但是我们还是过一遍。



<br>

**简单赋值：**

将右侧的运算结果赋值给左侧的变量(引用)。

```
struct Person {
   name: string,
   age: i32,
   weight: f32
}

var<private> me: Person;

me.name = 'puxiao';
me.age = 36;
me.weight = 76.5
```

> `struct` 是定义结构类型的关键词，相当于 TypeScript 中的 `interface`



<br>

**虚假赋值：**

所谓虚假赋值，就是赋值语句中 用 `_` 来充当左侧，简单来说 `_` 就是一个无意义的占位符。

```
_ = someFun()
```

> `_` 在 TypeScript 中有时很有用，假设 TS 配置有 “若有未使用的变量则发出警告”，当有些函数中的某个参数确确实实不需要使用的时候，就可以使用 `_` 来做占位符，从而避免 TS 的警告。



<br>

**复合赋值：**

将右侧的运算符移动到 等号 的左侧。

```
a = a + b

//等同于

a += b
```

在 WGSL 中类似的运算符有：`+、-、*、/、%、&、|、^、>>、<<`



<br>

**递增或递减**

对于变量自身的 递增(+1) 或 递减(-1)。

```
var a: i32 = 38;
a++ // 此时 a 的值为 39
a-- // 此时 a 的值又重新为 38
```



<br>

接下来，我们过一遍控制语句。

请注意，控制语句在极个别地方的用法和 JS 是不相同的。



<br>

**if 语句：**

和 JS 用法完全相同



<br>

**switch 语句：**

switch 的用法和 JS 相同，只不过在 WGSL 中多了一个关键词 `fallthrough`，可以将 判断控制器 移交给 下一个 case。

> fallthrough 单词原意为：失败、落空
>
> 在 JS 的 switch 中是不存在 fallthrough 的，但是在其他的一些编程语言也是有 fallthrough 关键词的。
>
> 在 JS 中类似作用的是 continue ，只不过 continue 不允许出现在 switch 中。



<br>

几点需要注意：

1. switch 查询的值 不需要使用 括号 包裹
2. switch 查询的值 不必在乎拼写形式，而是在乎表达的值，例如 `0` 和 `0x0000` 是相等的
3. default 不需要必须位于结尾处，但它还是会作为 最终默认 的匹配选项
4. fallthrough 关键词不能使用在最后一项中(case 或 default)



<br>

示例：

```
switch x {
    case 0: {
        ...
    }
    default {
        ...
    }
    case 1,2 {
        ....
        fallthrough
    }
    case 3 {
        ...
    }
}
```



<br>

**for 与 loop 循环：**

for 循环 和 JS 中的用法完全相同。

而 loop 循环可以看做是 for 循环形式上的一种 “变体”。

```
let a: i32 = 2;
var i: i32 = 0;
loop {
    if(i>=4) { break; }
    a *= 2
    i = i++
}
```

> 从代码可读性角度来说，for 循环是 loop 循环的简写形式(语法糖)。



<br>

**while 循环：**

和 JS 中的用法完全相同。



<br>

**break if 语法：**

break 可以跳出 switch、loop、for 循环。

而 break if 的用法是指：break + 条件判断 来决定是否跳出。

```diff
//假设原本我们只写了一个 break ，那么意味着一定是跳出循环
- break

//而 break if xxx 是指只有当 xxx 为 true 时才会选择跳出循环
+ break if i>= 4;
```



<br>

**continue 与 continuing：**

continue 是跳出本次循环的剩余代码，进入下一次循环。

那 WGSL 中的 continuing 是什么意思呢？

> 我看了 WGSL 官方文档，也没太看明白。



<br>

大体上来讲 continuing 位于 loop 循环体的结尾处，你可以简单把它理解为 “本次循环体中最后一项执行的内容”，即使在前面已经发生了 continue(跳出本次循环体)，也依然会执行 continuing 中的代码。

> 我也不确定这样理解对不对。

```
var i: i32 = 0;
loop {
    if(i >=4 ) { break; }
    if(i % 2 == 0) { continue; }
    let step: i32 = 2;
    
    continuing {
        i = i + step;
    }
}
```



<br>

在 continuing 中不允许出现以下 2 个关键词：

1. return
2. discard



<br>

**discard 关键词：**

只有在 片元 着色器阶段才可以使用 `discard` ，表明立即结束当前片元着色器的执行，并丢弃当前的片元数据。

```
discard;
```



<br>

**本文小结：**

在 WGSL 的赋值语句、控制语句 绝大多数用法和 JS 都相同。

只不过 WGSL 中有以下几个 JS 中不存在的关键词和用法：

1. switch 中的 fallthrough
2. loop 循环：for 循环的另外一种形式
3. break if xxx：只有 xxx 为 true 时才执行 break 跳出
4. continuing：当前循环体中最后执行的内容
5. discard：立即停止当前片元着色器的执行，并丢弃当前片元数据



<br>

本文到此结束，下一节我们学习 函数(function) 相关知识点。
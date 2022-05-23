# 02 WGSL基础之着色器的4个生命周期节点

**WGSL中着色器(shader)的4个生命周期节点分别是：着色器模块创建、管线创建、着色器开始执行、着色器执行完成。**



<br>

上面提到的前 2 个生命周期节点可以看做是 “着色器前期准备阶段”，而后 2 个生命周期节点可以看做是 “着色器开始和执行完成阶段”。



<br>

> 本文提到的 "生命周期" 和 React/Vue 中的生命周期概念是相同。

我们把 “生命周期” 中几个关键的时间点称之为 “**节点**”，而节点与节点之间我们称之为 “**阶段**”。



<br>

下面就这 4 个生命周期节点详细了解一下。



#### 着色器的4个生命周期节点

<br>

> 以下会使用 “节点” 这个词来指代 “生命周期节点”



<br>

**第1个节点：着色器模块创建(Shader module creation)**

当在 WebGPU 中执行 GPUDevice 实例的 createShaderModule() 方法后会开始 着色器模块创建节点。

在渲染管线中，目前只有 顶点阶段 和 片元阶段 是可以供我们进行编程的，所以正常情况我们需要执行 2 次 createShaderModule() 方法，分别用于创建 顶点着色器模块 和 片元着色器模块。



<br>

请记住：着色器模块创建节点实际上包含 2 个内容，分别是 顶点着色器模块创建 和 片元着色器模块创建。



<br>

**第2个节点：管线创建(Pipeline creation)**

当在 WebGPU 中执行 GPUDevice 实例的 createComputePipeline() 或 createRenderPipeline() 方法后会开始 管线创建。

这两个方法会分别创建 计算管线 和 渲染管线。



<br>

> 补充：以上 2 个方法分别对应有他们的异步形式，并且 WebGPU 官方推荐使用他们的异步函数，因为这样可以一些避免阻塞。
>
> createComputePipeline() 对应的异步函数为 createComputePipelineAsync()
>
> createRenderPipeline() 对应的异步函数为 createRenderPipelineAsync()



<br>

请记住：管线创建节点实际上包含 2 个内容，分别是 计算管线创建 和 渲染管线创建。



<br>

> WebGPU 相关知识点回顾：
>
> * [WebGPU基础之着色器模块(GPUShaderModule)](https://github.com/puxiao/webgpu-tutorial/blob/main/10%20WebGPU%E5%9F%BA%E7%A1%80%E4%B9%8B%E7%9D%80%E8%89%B2%E5%99%A8%E6%A8%A1%E5%9D%97(GPUShaderModule).md)
> * [WebGPU基础之管线(GPUPipelineBase、GPUComputePipeline、GPURenderPipeline)](https://github.com/puxiao/webgpu-tutorial/blob/main/11%20WebGPU%E5%9F%BA%E7%A1%80%E4%B9%8B%E7%AE%A1%E7%BA%BF(GPUPipelineBase%E3%80%81GPUComputePipeline%E3%80%81GPURenderPipeline).md)



<br>

**第3个节点：着色器开始执行(Shader execution start)**

当在 WebGPU 中依次执行了下面操作：

1. 渲染通道编码器(GPURenderPassEncoder) 实例调用了 draw() 和 end() 方法

2. 命令编码器(GPUCommandEncoder) 实例调用了 finish() 方法，得到了 命令缓冲区(GPUCommandBuffer)

3. 最终 命令队列(GPUQueue) 通过 .submit() 将 命令缓冲区 提交给 GPU

4. 此时进入 着色器开始执行，进入该阶段的第一件事情就是去执行着色器模块中的入口函数

   > 着色器模块中的入口函数名是由 顶点/片元 着色器模块配置项中 `entryPoint` 属性值决定的。



<br>

**第4个节点：着色器执行完成(Shader execution end)**

当所有着色器中的工作都被完成时发生。

1. 所有的调用都完成
2. 所有对资源的访问都完成
3. 若有需要，输入已传递给下游管线阶段



<br>

#### 着色器可能发生的错误

在以上 4 个节点的发生过程中，着色器程序可能会发生错误。

这些错误可以简单归纳为 2 类。



<br>

**程序错误(program error)**：

着色器(.wgsl) 中的代码不符合 WGSL 或 WebGPU 规范时发生。

这里推荐在 VSCode 中安装一个名为 “wgsl-analyzer” 的插件，它可以帮助我们对 .wgsl 文件中的代码进行格式化，以及少量的语法检查。



<br>

**未分类错误(uncategorized erro)**：

着色器(.wgsl) 中的代码虽然符合 WGSL 或 WebGPU 规范，但或许因为 着色器太过复杂，超出了当前设备可实现的能力，这些错误就会被归纳为 "未分类错误"。

> 若真的发生这种错误，那么只能通过简化着色器代码来解决。



<br>

当然还有一种可能发生未分类错误的原因，那就是 WebGPU 本身存在的缺陷。

> 毕竟 WebGPU 目前还处于草案当中，并未完全成熟。



<br>

以上错误可能会发生在以下 3 个着色器生命周期阶段内：

1. 着色器模块创建阶段
2. 管线创建阶段
3. 着色器执行期间



<br>

**编译消息：**

在编译执行 WGSL 的过程中，除了错误(error)信息外，还会有 警告(warning) 和 信息(info)。

在这 3 种信息中还会包含 错误/警告 相对应的代码行号、具体字符位置 等其他信息。

> 这些信息和平时使用 React/Vue 给我们提供的编译信息是想通、相似的。



<br>

本文我们学习了 WGSL 着色器的 4 个生命周期，以及在这期间可能抛出的错误、警告、信息。

下一节，我们将开始学习 WGSL 代码中的一些规范和约定，例如 如何添加注释、如何标记处于哪个阶段、常见关键字、如何添加声明等等。
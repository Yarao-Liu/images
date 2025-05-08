### 搭建基于ollama+DeepSeek生成思维导图智能体

1. #### **环境准备**

- 已基于ollama搭建DeepSeek模型

- 已搭建并配置好dify服务

- 已搭建好读取markdown生成在线脑图服务

2. #### **工作流展示**
![doc2MindMap](https://github.com/Yarao-Liu/images/blob/main/2025-05/doc2MindMap_1746673315906.png)

3. #### **配置智能体**

  3.1 创建工作流应用

![image-20250508085834555](C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508085834555.png)

  ​	创建智能体应用，输入应用名称、图标和描述，点击`创建`完成应用的创建。

  3.2 添加选择输入字段

![image-20250508090829761](C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508090829761.png)

  ​	这里有两个场景，一个场景是将用户上传的文件转换为思维导图；另一个场景是从已有应用系统中获取文件转换为思维导图。因此这里的开始节点有两个字段：file和fileId。

  3.2.1 选择输入类型

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508091145159.png" alt="image-20250508091145159" style="zoom: 33%;" />

  ​	file的类型是单文件，fileId的类型是文本。

  3.3 添加条件分支

  ​	点击开始节点，再点击加号可选择后续节点。这里简单判断文件是否存在来决定工作流走哪个分支，生成环境可能需要更完备的条件判断。

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508092008445.png" alt="image-20250508092008445" style="zoom:33%;" />

  3.3 添加文档提取器

  大模型不能直接处理文件，需要通过文档提取器读取文件，将文档解析为LLM便于理解的文本内容。

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508092523880.png" alt="image-20250508092523880" style="zoom:33%;" />

  3.3.1 配置文档提取器的变量名称

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508092739850.png" alt="image-20250508092739850" style="zoom:33%;" />

  每一个节点的输入变量可以选择工作流当前节点所在分支的所有变量，这里只需要设置为开始节点的文件。

  3.4 添加大模型

  大模型的提示词：

  ```markdown
  你是文档总结大师，擅长提炼文档核心内容并生成结构化大纲。你会深入理解文档内容，而不仅仅依赖标题，确保总结准确反映关键信息，并以思维导图形式呈现层级关系。
  
  **输入内容**：
  {{#context#}}
  {{#1745489969309.text#}}
  **输出要求**：
  - 以Markdown格式输出，适合生成思维导图（如markmap工具）。
  - 使用层级结构：标题（#）表示主节点，无序列表（-）表示分支和子节点。
  - 确保内容精炼，关键信息通过有序或无序列表分条呈现。
  
  **参考格式**：
  # [中心主题]
  ## [一级分支1]
  - [二级分支1.1]
    - [三级分支1.1.1]
    - [三级分支1.1.2]
  - [二级分支1.2]
    - [三级分支1.2.1]
    - [三级分支1.2.2]
  ## [一级分支2]
  - [二级分支2.1]
    - [三级分支2.1.1]
    - [三级分支2.1.2]
  - [二级分支2.2]
    - [三级分支2.2.1]
    - [三级分支2.2.2]
  ## [一级分支3]
  - [二级分支3.1]
  - [二级分支3.2]
  ## 注意事项（可选）
  - [注意事项1]
  - [注意事项2]
  
  ```

  注意：大模型输入是文档提取节点的输出，需要再提示词中体现出来。示例中{{#context#}}和
  {{#1745489969309.text#}}。

  3.4.1 配置大模型节点

  ​	这里使用的大模型是qwen2.5:3b，需要设置大模型的温度、配置上下文和编写提示词，大模型节点的上下文是上个节点-文档提取器的输出。

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508103547425.png" alt="image-20250508103547425" style="zoom:33%;" />

  3.5 添加HTTP请求

  <img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508104603075.png" alt="image-20250508104603075" style="zoom: 33%;" />

  3.5.1 配置HTTP请求

  - 注意选择http请求
  - 填写环境准备中搭建好的markdown生成在线脑图服务
  - 注意在body中选择raw，并在点击{x}选择LLM输出的text

  ![image-20250508104449656](C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508104449656.png)

  3.6 添加直接回复

  在右侧回复框中点击{x}选择http节点的body输出。

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508104707426.png" alt="image-20250508104707426" style="zoom:33%;" />

<img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508111415768.png" alt="image-20250508111415768" style="zoom:33%;" />


4. #### 测试智能体

  4.1 预览功能

   <img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508105609538.png" alt="image-20250508105609538" style="zoom:33%;" />

   预览后点击上传文件，点击`开始运行`执行整个工作流。

  4.2 查看执行结果

   <img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508105534469.png" alt="image-20250508105534469" style="zoom:33%;" />

   运行过程中可以追踪，看到每个节点的运行情况，可以对工作流进行优化。

   <img src="C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508105510712.png" alt="image-20250508105510712" style="zoom:33%;" />

  4.3 查看脑图

   ![image-20250508105639724](C:\Users\13439\AppData\Roaming\Typora\typora-user-images\image-20250508105639724.png)
5. #### 分支说明

   ​	此文章只对用户上传的文件进行解析生成思维导图的路径进行描述。另一种方式只是在此基础上添加了用户验证的文件下载两个HTTP节点和一个代码执行节点，这里不再赘述。

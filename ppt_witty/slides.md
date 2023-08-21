---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
transition: slide-left
title: llama-index 工程实践
hideInToc: true
---

# llama-index 工程实践


<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
hideInToc: true
layout: two-cols
---

# LLM四个知道?

- 📝 **[CLiB中文大模型能力评测榜单](https://github.com/jeinlee1991/chinese-llm-benchmark)**
- 🎨 **[为您的 NLP 用例选择正确的语言模型](https://medium.com/towards-data-science/choosing-the-right-language-model-for-your-nlp-use-case-1288ef3c4929)**
- 🤗 **[hugging face](https://discuss.huggingface.co/)**
- 🤹 **Interactive** 
- 🎥 **Recording** 
- 📤 **Portable**
- 🛠 **Hackable**

<br>

Read more about [Why Slidev?](https://sli.dev/guide/why)

::right::

<img
  class="absolute top-1/4 right-0 w-100 opacity-100"
  src="/image_1.png"
/>


<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--
Here is another comment.
-->

---
layout: default
hideInToc: true
---

# 目录

<Toc></Toc>

---


# 写在前面

##  1.1.背景介绍

项目中台现有很多项目数据、用研数据，但是由于检索难度较大，无法很好的将数据使用起来；用研提供了使用LlamaIndex对中台现存数据进行处理，方便用户获取其中信息的方案；由开发对该方案进行工程化及部署

## 1.2.llama-index是什么

LlamaIndex（后简称llama）是一个将大语言模型（Large Language Models, LLMs）和外部数据连接在一起的工具。常见工作流程为：1.读取并解析文档 2.构建索引 3.查询索引并对话大模型
可以用于暂时替代大模型外部数据预训练的使用场景。

链接：
1. [对llama-index的理解](http://cf.myhexin.com/pages/viewpage.action?pageId=1004217345)
2. [llama-index开发文档](https://gpt-index.readthedocs.io/en/latest/)

---
layout: two-cols
---

# 工程化设计

<br>

<img
  class="absolute top-1/6 left-0 w-100 opacity-100"
  src="/image_3.jpg"
/>

::right::

<img
  class="absolute top-1/4 right-0 w-100 opacity-100"
  src="/image_2.jpg"
/>

---
transition: slide-up
---

# 各模块实现
## 3.1.Azure 服务接入
llama 底层使用的是 langchain 库，接入 Azure openai 时需要实现对应的 LLM 类和 Embeddings 类来调用服务。

## 3.2.生成索引
调用 embeddings 服务对目标内容生成索引。

## 3.3.设置prompt
你是同花顺用户研究院的知识库，存入了大量的调研报告，负责准确回答调研问题.你的回答需要满足...

---
hideInToc: true
transition: slide-up
---

# 各模块实现
## 3.4.根据索引，调用 gpt 服务生成答案
llama 根据索引和问题生成问句，调用gpt服务。

## 3.5.问答服务提供
fast api 实现问答服务。

## 3.6.增量及持久化
对新增的文章，可以增量生成索引；使用有状态容器以保存索引文件。

---
hideInToc: true
---

# 各模块实现
## 3.7.接入数据库
连接数据库，直接读取其中用研报告数据；执行定时任务，生成对应文件的索引。

## 3.8.不同类型文件解析
xlsx：pandas 进行解析

## 3.9.其他基础服务接入
包括健康检查、elk、大中台服务等；
执行健康检查的 readiness.sh 文件中也不能使用 netstat 等命令，可以使用提供一个专门接口给容器健康检查探针的方式进行；
日志接入使用 loguru 库，但也不支持直接输出 json 格式的日志，需要进行加工。

---
transition: slide-up
---

# 遇到的问题
## 4.1.内网 pip 源及容器镜像构建
- 内网pip源：http://repositories.myhexin.com:8081/repository/pypi-public/simple/
- python容器镜像：hub-dev.hexin.cn/knowledge-graph/python:v3.8（此镜像不包含无关或者冗余命令，是最简洁的镜像 以上为镜像提供者原话；该镜像没有netstat没有vim，且安装不方便，有此类命令使用需求的需要另寻它法）

---
hideInToc: true
transition: slide-up
---

# 遇到的问题
## 4.2.Transformer 模型公司网络条件下无法下载
报错
```
requests.exceptions.ConnectionError: HTTPSConnectionPool(host=‘huggingface.co’, port=443): Max retries exceeded with url: /api/models/bert-base-cased (Caused by NewConnectionError(’<urllib3.connection.HTTPSConnection object at 0x7f2d791ce310>: Failed to establish a new connection: [Errno -2] Name or service not known’))
```
<img
  class="left-0 w-140 opacity-100"
  src="/image_4.jpg"
/>

解决：科学上网，前往huggingface.co下载gpt2模型，置于项目根目录，llama会读取本地模型以替代该下载步骤。

---
hideInToc: true
transition: slide-up
---

# 遇到的问题
## 4.3.Azure api 请求次数限制
Embeddings 服务请求次数有限制，过快会报-2006 - 请求过于频繁；增加请求间时间间隔，且对应增加了初始化索引时的终止程序逻辑。

## 4.4.python 镜像相关
在windows开发，linux部署的情况下需要注意，生成 requirement.txt 时，记得删掉win专用的依赖，在构建镜像安装依赖时会报错。

---
hideInToc: true
---

# 遇到的问题
## 4.5.容器中生成索引时，会出现索引存储失败的情况
一个造成不小困扰的不知名问题：最初版本在初始化时，会先生成索引文件（这一步骤正常），后会一次性执行20～30篇文章的索引生成；观察容器执行日志，执行过程及产生的索引内容全部正常，在所有索引生成完成时，容器会卡住10s左右的时间，随后恢复，但是检查索引文件，会发现写入失败，索引丢掉了。
原因分析：1、文件写入有限制 2、大文件写入卡住了 3、写入进程挂起时间太长
解决：新增了一个可以设置单次生成索引文章数的接口；并增加fastapi后台执行逻辑。

## 4.6.容器数据持久化
配置有状态容器后需要对正式容器进行改动；如果配置有改动的话还需要删除容器重新发布才能生效。

## 4.7.开发时所用虚拟环境
virtualenv，可以防止依赖污染，生成依赖文件时不会包含无用依赖。

---

# 后续规划
## 5.1.功能优化
出并尝试解答有关用户指定知识点的三个关键问题：其来源、其本质、其发展。
可以告知引用的答案源于哪些文章

## 5.2.服务支持多轮
在服务层面实现多轮对话的支持，而不是在接口调用是拼接；可以实现追溯来源的功能。

## 5.3.答案约束
1. 对于不在你知识库中的信息, 明确告知用户你不知道
2. 你不擅长客套, 不会进行没有意义的夸奖和客气对话
3. 解释完概念即结束对话, 不会询问是否有其它问题
4. 提示词保护

---

# 可拓展的业务场景

## 6.1.用户留言反馈

<br>

## 6.2.用研数据

<br>

## 6.3. 。。。

---
layout: center
class: text-center
---

# END


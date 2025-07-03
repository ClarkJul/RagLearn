# RagLearn
learn graph rag and light rag



## AutoDL服务器操作

![image-20250703135248113](image\autodl.png)



使用eval "$(/root/miniconda3/bin/conda shell.bash hook)"  激活虚拟环境

```sh
eval "$(/root/miniconda3/bin/conda shell.bash hook)"

conda create -n graphrag python=3.11.7 #创建graphrag的python环境

```

![image-20250703135813579](image\autodl1.png)

```sh
conda activate graphrag

pip install graphrag
```

等待安装完成所有的依赖库



## 准备工作

###  graphrag基础概念

#### 定义

GraphRAG是微软研究院开发的一种创新型检索增强生成（RAG）方法，旨在提高大语言模型LLM在处理复杂信息和私有数据集时的推理能力    

#### 索引 indexing 

是一个数据管道和转换套件，旨在利用LLM从非结构化文本中提取有意义的结构化数据

核心功能：

**[1]** 从原始文本中提取实体、关系和声明 

**[2]** 在实体中执行社区检测

**[3]** 生成多级粒度的社群摘要和报告

**[4]** 将实体嵌入图向量空间

**[5]** 将文本块嵌入文本向量空间

**[6]** 管道的输出可以json和parquet等多种格式存储 



提供的实体类型：

**[1]** Document：输入的文档，csv或txt中的单个行

**[2]** TextUnit：要分析的文本块，Document与TextUnit关系为1:N关系

**[3]** Entity：从TextUnit中提取的实体

**[4]** Relationship：两个实体之间的关系，关系由Covariate（协变量）生成

**[5]** Covariate：协变量，提取声明信息，包含实体的声明 

**[6]** CommunityReport：社群报告，实体生成后对其执行分层社群检测，并为该分层中的每个社群生成报告

**[7]** Node：节点，实体和文档渲染图的布局信息     

####  提示微调 prompt tuning

为生成的知识图谱创建领域自适应prompt模版的功能，提供两种方式进行调整：

**自动调整：** 通过加载输入，将输入分割成文本块，然后运行一系列LLM调用和prompt模版替换来生成最终的prompt模版

**手动调整：** 手动调整prompt模版   

**具体用法如下：**

```
python -m graphrag prompt-tune --config ./settings.yaml --root ./ --no-entity-types --language Chinese --output ./prompts 
```

根据实际情况选择相关参数：

**--config** :(必选) 所使用的配置文件，这里选择setting.yaml文件 

**--root** :(可选)数据项目根目录，包括配置文件（YML、JSON 或 .env）。默认为当前目录

**--domain** :(可选)与输入数据相关的域，如 “空间科学”、“微生物学 ”或 “环境新闻”。如果留空，域将从输入数据中推断出来

**--method** :(可选)选择文档的方法。选项包括全部(all)、随机(random)或顶部(top)。默认为随机  

**--limit** :(可选)使用随机或顶部选择时加载文本单位的限制。默认为 15

**--language** :(可选)用于处理输入的语言。如果与输入语言不同，LLM 将进行翻译。默认值为“”，表示将从输入中自动检测

**--max-tokens** :(可选)生成提示符的最大token数。默认值为 2000 

**--chunk-size** :(可选)从输入文档生成文本单元时使用的标记大小。默认值为 20

**--no-entity-types**（无实体类型） :(可选)使用无类型实体提取生成。建议在数据涵盖大量主题或高度随机化时使用 

**--output** :(可选)保存生成的提示信息的文件夹。默认为 “prompts”    

#### 检索 query

本地搜索（Local Search）、全局搜索（Global Search）、问题生成（Question Generation）

**本地搜索（Local Search）：基于实体的推理** 

本地搜索方法将知识图谱中的结构化数据与输入文档中的非结构化数据结合起来，在查询时用相关实体信息增强 LLM 上下文

这种方法非常适合回答需要了解输入文档中提到的特定实体的问题（例如，"洋甘菊有哪些治疗功效？）

**全局搜索（Global Search）： 基于全数据集推理** 

根据LLM生成的知识图谱结构能知道整个数据集的结构（以及主题）

这样就可以将私有数据集组织成有意义的语义集群，并预先加以总结。LLM在响应用户查询时会使用这些聚类来总结这些主题 

**问题生成（Question Generation）：基于实体的问题生成**

将知识图谱中的结构化数据与输入文档中的非结构化数据相结合，生成与特定实体相关的候选问题       



### GraphRag项目导入

github地址:https://github.com/microsoft/graphrag 

官网地址:https://microsoft.github.io/graphrag/query/overview/   



将原始文件夹basicdata导入到服务器上

#### 创建graphrag所需文件夹 

在当前项目下创建个文件夹，**注意:** 这里的ragtest文件夹为自定义文件夹，下面所有操作均在该文件夹目录下进行操作         

```
mkdir ragtest  
cd ragtest 

mkdir input 
mkdir inputs  
mkdir cache   
```



#### 准备测试文档

**注意:** 这里以西游记白话文前九回内容为例，将basicdata/data/下的1-9.txt文件直接放入ragtest/input文件夹下



#### graphrag初始化

```
python -m graphrag  --init  --root ./
```

  初始化后会生成 .env和 settings.yaml文件，以及prompts文件夹和prompt文件

![image-20250703143148891](image\graphrag.png)





#### 设置参数

**设置.env和settings.yaml**   

**注意1:** 针对阿里通义千问大模型具体参考提供的basicdata/temp下的.env和settings.yaml文件内容，

**注意2:** 针对智谱大模型本身的参数限制，将basicdata/temp下的.env和settings.yaml文件内容拷贝

**注意3:** 针对讯飞星火大模型本身的参数限制，将other/temp下的.env和settings.yaml文件内容拷贝

**注意4:** 使用本地大模型(Ollama方案) 

Ollama是一个轻量级、跨平台的工具和库，专门为本地大语言模型(LLM)的部署和运行提供支持 

它旨在简化在本地环境中运行大模型的过程，不需要依赖云服务或外部API，使用户能够更好地掌控和使用大型模型  

(1)安装Ollama，进入官网https://ollama.com/下载对应系统版本直接安装即可 

(2)启动Ollama，安装所需要使用的本地模型，执行指令进行安装即可



#### prompt微调

查看微调参数

```
graphrag prompt-tune --help
```

<img src="image\prompt.png" alt="image-20250703144010032" style="zoom:80%;" />



优化提示词，选择一条适合的运行即可  

```
python -m graphrag prompt-tune --config ./settings.yaml --root ./ --no-discover-entity-types --language Chinese --output ./prompts       
```

​     

#### 构建索引

```
python -m graphrag index --root ./ 
```

<img src="image\graphIndex.png" alt="image-20250703145040966" style="zoom:80%;" />

这个过程很漫长。。。。。。

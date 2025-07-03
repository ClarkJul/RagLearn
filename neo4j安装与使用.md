# neo4j安装与使用

## Neo4j图数据库安装指南

### Neo4j图数据库简介

Neo4j是一个高性能的、完全事务性的、企业级的图数据库，它使用图模型而非传统的表结构来存储数据。Ne04j是目前最流行的图数据库之一，广泛应用于社交网络、推荐系统、知识图谱、欺诈检测等领域。

**Neo4j的主要特点：**

- 图数据模型：使用节点(Node)、关系(Relationship)和属性(Property)来表示和存储数据
- Cypher查询语言：专为图数据库设计的声明式查询语言，语法直观易学
- ACID事务支持：完全支持ACID(原子性、一致性、隔离性、持久性)事务
- 高性能：针对图遍历操作进行了优化，查询性能远超关系型数据库
- 可扩展性：支持集群部署，可以处理大规模图数据
- 可视化工具：内置浏览器界面，可以直观地查看和操作图数据

**Neo4j的核心概念：**

- 节点(Node)：图中的实体，可以包含多个标签和属性
- 标签(Label)：用于对节点进行分类，一个节点可以有多个标签
- 关系(Relationship)：连接两个节点的有向边，必须有一个类型和方向
- 属性(Property)：节点和关系都可以包含键值对形式的属性

> **注意：Neo4j是用Java编写的，因此需要Java运行时环境(JRE)或Java开发工具包(JDK)才能运行。**

### 安装Java 21

#### 下载Java 21

可以从以下官方渠道下载Java21：

OpenJDK: https://jdk.java.net/21/

推荐下载Windows平台的ZIP压缩包，例如：jdk-21_windows-x64_bin.zip

#### 解压安装

- 将下载的 jdk-21_windows-x64_bin.zip 解压到指定目录，例如：D:\java\jdk-21
- 确保解压后的目录结构包含bin、lib等子目录

#### 配置环境变量

- 右键点击"此电脑”，选择"属性"，然后点击"高级系统设置”
- 在弹出的"系统属性"窗口中，点击"环境变量"按钮
- 在"系统变量"区域，找到Path变量，点击"编辑"
- 点击"新建"，添加以下路径：D:\java\jdk-21\bin(根据你的实际安装路径修改)
- 点击"确定"保存所有更改

#### 验证安装

打开命令提示符(cmd)

输入以下命令：java -version

如果安装成功，将显示类似以下信息：

```
java version "21" 2023-09-19

Java(TM) SE Runtime Environment (bui1d 21+35-LTS-2513)

Java HotSpot(TM) 64-Bit Server VM (bui1d 21+35-LTS-2513, mixed mode, sharing)
```



### 安装Neo4j 5.26.8

#### 下载Neo4j

可以从Neo4j官方下载页面获取社区版：

官方下载:https://neo4j.com/download-center/#community

<img src="image\neo4j.png" alt="image-20250703231339523" style="zoom: 50%;" />

####  解压安装

- 将下载的 neo4j-community-5.26.8-windows.zip 解压到指定目录，例如：D:\neo4j\neo4j-community-5.26.8
- 确保解压后的目录结构包含 bin、conf、data 等子目录

#### 配置环境变量

- 打开"系统属性"→"环境变量"(同Java配置步骤)
- 在"系统变量"区域，找到 Path 变量，点击"编辑"
- 点击"新建"，添加Neo4j的bin目录路径，例如：D:\neo4j\neo4j-community-5.26.8\bin
- 点击"确定"保存所有更改

#### 验证安装

- 打开命令提示符(cmd)

- 输入以下命令：neo4j.bat --version

- 如果安装成功，将显示类似以下信息：

  ```
  C:\Users\30707>neo4j.bat --version
  5.26.8
  ```

### 启动方式

#### Neo4j提供多种启动方式：

| 命令              | 描述                                   | 适用场景                   |
| ----------------- | -------------------------------------- | -------------------------- |
| neo4j.bat console | 控制台模式启动，日志直接输出到当前窗口 | 开发调试，需要实时查看日志 |
| neo4j.bat start   | 后台服务模式启动                       | 生产环境，长期运行         |
| neo4j.bat stop    | 停止Ne04j服务                          | 需要停止服务时             |
| neo4j.bat restart | 重启Ne04j服务                          | 配置更改后需要重启         |
| neo4j.bat status  | 查看服务状态                           | 检查服务是否运行           |



#### 以控制台模式启动

- 打开命令提示符(cmd)
- 导航到Neo4j的bin目录，或确保已在环境变量中配置
- 执行以下命令：neo4j.bat console
- 等待启动完成，当看到类似以下信息时表示启动成功：

```
Started neo4j (pid 1234). It is avai1ab1e at http://1ocalhost:7474/
```

- 不要关闭此窗口，否则服务会停止

<img src="image\start.png" alt="image-20250703234352855" style="zoom:80%;" />

**初始密码：neo4j**

输入之后需要自己改密码

<img src="image\mima.png" alt="image-20250703234630659" style="zoom:67%;" />





## 数据导入neo4j数据库

批处理导入数据到neo4j

在Neo4j中，将数据批量导入到数据库中通常涉及到以下几个步骤：

### 1. 准备数据

首先，确保你的数据是适合导入到Neo4j的格式。常见的数据格式包括CSV、JSON或自定义格式。

### 2. 使用`neo4j-admin import`工具

Neo4j提供了一个非常强大的命令行工具`neo4j-admin import`，它专门用于高效地批量导入数据。这个工具可以处理大规模数据导入，并且比使用Cypher查询更快。

**停止Neo4j服务**：在导入数据之前，你需要停止Neo4j服务以避免冲突

```
@echo off

REM Neo4j Admin导入命令
REM 适用于Neo4j 2025.02.0及更高版本
REM 生成时间: 2025-03-21 17:08:23

neo4j-admin database import full neo4j --overwrite-destination ^
  --nodes=Product="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\product_nodes.csv" ^
  --nodes=Category="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\category_nodes.csv" ^
  --nodes=Supplier="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\supplier_nodes.csv" ^
  --nodes=Customer="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\customer_nodes.csv" ^
  --nodes=Employee="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\employee_nodes.csv" ^
  --nodes=Shipper="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\shipper_nodes.csv" ^
  --nodes=Order="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\order_nodes.csv" ^
  --nodes=Review="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\review_nodes.csv" ^
  --relationships=BELONGS_TO="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\product_category_edges.csv" ^
  --relationships=SUPPLIED_BY="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\product_supplier_edges.csv" ^
  --relationships=PLACED="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\customer_order_edges.csv" ^
  --relationships=PROCESSED="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\employee_order_edges.csv" ^
  --relationships=SHIPPED_VIA="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\order_shipper_edges.csv" ^
  --relationships=CONTAINS="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\order_product_edges.csv" ^
  --relationships=REPORTS_TO="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\employee_reports_to_edges.csv" ^
  --relationships=WROTE="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\customer_review_edges.csv" ^
  --relationships=ABOUT="E:\LLMlearn\lgAgent\origin_data\data\neo4j_admin\review_product_edges.csv" ^
  --delimiter="," ^
  --array-delimiter=";" ^
  --skip-bad-relationships=true ^
  --skip-duplicate-nodes=true
```

**启动服务器**：neo4j.bat console

<img src="image\res.png" alt="image-20250704000915141" style="zoom:50%;" />
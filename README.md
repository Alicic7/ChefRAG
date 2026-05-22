# ChefRAG — 智能菜谱问答助手

**ChefRAG** 是一个基于检索增强生成（RAG）的智能菜谱问答系统，能根据你的口味、食材或烹饪需求推荐菜品，并给出详细制作步骤。从此告别“今天吃什么”的选择困难！

## 特性

- 🍳 **结构化菜谱知识库** – 基于 300+ 份高度统一的 Markdown 菜谱，覆盖家常菜、硬菜、素菜等
- 🔍 **精准检索 + 完整上下文** – 父子文档块策略：用小粒度子块精确匹配问题，返回完整文档避免信息碎片
- 🤖 **智能问答** – 支持菜品做法查询、食材清单获取、按条件推荐菜品
- ⚙️ **即插即用的模型接口** – 默认接入 Kimi API，可轻松切换其他 LLM

## 快速上手

### 1. 环境准备

推荐使用 Python 3.12，创建虚拟环境并安装依赖：

```bash
conda create -n chefrag python=3.12.7
conda activate chefrag
cd ChefRAG
pip install -r requirements.txt
```

### 2. 配置 API Key

项目默认使用 [Kimi API](https://platform.moonshot.cn/console/api-keys) 作为生成模型。

注册后获取 API Key，并在项目根目录下新建`.env`文件并写入以下内容：

```
export MOONSHOT_API_KEY=your_key_here
```

配置完成后，运行：

```bash
python main.py
```

即可启动交互式问答界面。

## 项目背景

HowToCook 用 Markdown 记录了数百道中餐的做法，每份菜谱严格使用统一的小标题（如“必备原料和工具”“计算”“操作”“附加内容”）。  
这些文档**篇幅短、结构强**，极其适合直接按标题拆分后进行向量索引。

然而，如果严格按标题切分，会出现“只见操作，不见食材”的上下文断裂。  
为此，ChefRAG 采用了**父子文档块**的设计：

- **子块（小块）**：按标题切割，用于向量检索，保证精确匹配用户意图
- **父块（完整文档）**：检索命中后，返回整个菜谱文档，交给 LLM 生成完整回答

> 小块检索，大块生成 — 既保证了检索的精确性，又避免了上下文缺失。

## 系统架构

ChefRAG 的处理流程分为四个核心模块：

1. **数据准备** (`data_preparation`) – 加载 Markdown 菜谱，清洗并构建父子块关系
2. **索引构建** (`index_construction`) – 将子块向量化，存入向量数据库
3. **检索优化** (`retrieval_optimization`) – 接收用户查询，检索最相关子块并映射回父文档
4. **生成集成** (`generation_integration`) – 将完整菜谱作为上下文，调用 LLM 生成自然语言回答

## 项目结构

```text
ChefRAG/
├── config.py                   # API Key、模型参数等配置
├── main.py                     # 交互入口
├── requirements.txt            # Python 依赖
├── rag_modules/                # 核心 RAG 模块
│   ├── __init__.py
│   ├── data_preparation.py     # 数据加载与父子分块
│   ├── index_construction.py   # 向量索引构建
│   ├── retrieval_optimization.py # 检索与父文档映射
│   └── generation_integration.py # LLM 生成封装
└── vector_index/               # 向量索引持久化目录（自动生成）
```

## 使用示例

启动后，你可以这样提问：

- “宫保鸡丁怎么做？”
- “推荐几个简单的素菜”
- “红烧肉需要什么食材？”

系统将返回包含完整做法的回答。

## 依赖

主要依赖在 `requirements.txt` 中，关键库包括：

- `langchain`
- `chromadb` / `faiss`（向量存储）
- `openai`（用于调用 Kimi API）
- `tiktoken`（分块计数）

具体版本请查看文件。

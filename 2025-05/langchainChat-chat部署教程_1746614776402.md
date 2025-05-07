### LangChain-Chatchat0.3.1部署教程

![img](https://github.com/chatchat-space/Langchain-Chatchat/raw/master/docs/img/logo-long-chatchat-trans-v2.png)

1. 安装Langchain-Chatchat

   从 0.3.0 版本起，Langchain-Chatchat 提供以 Python 库形式的安装方式，具体安装请执行：

```shell
pip install langchain-chatchat -U
```

2. 模型推理框架并加载模型

​	从 0.3.0 版本起，Langchain-Chatchat 不再根据用户输入的本地模型路径直接进行模型加载，涉及到的模型种类包括 LLM、Embedding、Reranker 及后续会提供支持的多模态模型等，均改为支持市面常见的各大模型推理框架接入，如 [Xinference](https://github.com/xorbitsai/inference)、[Ollama](https://github.com/ollama/ollama)、[LocalAI](https://github.com/mudler/LocalAI)、[FastChat](https://github.com/lm-sys/FastChat)、[One API](https://github.com/songquanpeng/one-api) 等。

因此，请确认在启动 Langchain-Chatchat 项目前，首先进行模型推理框架的运行，并加载所需使用的模型。

3. 初始化项目配置与数据目录

   从 0.3.1 版本起，Langchain-Chatchat 使用本地 `yaml` 文件的方式进行配置，用户可以直接查看并修改其中的内容，服务器会自动更新无需重启。
   
   设置 Chatchat 存储配置文件和数据文件的根目录

```shell
# on linux or macos 若不设置该环境变量，则自动使用当前目录。
export CHATCHAT_ROOT=/root/LangChain-Chatchat-0.3.1/chatchat_data

# on windows
set CHATCHAT_ROOT=D:/LangChain-Chatchat-0.3.1/chatchat_data
```

执行初始化

```shell
chatchat init
```

该命令会执行以下操作：

- 创建所有需要的数据目录
- 复制 samples 知识库内容
- 生成默认 `yaml` 配置文件

修改配置文件：

- 配置模型（model_settings.yaml）

  ```yaml
  # 默认选用的 LLM 名称
  DEFAULT_LLM_MODEL: deepseek-r1:1.5b
  # 默认选用的 Embedding 名称
  DEFAULT_EMBEDDING_MODEL: quentinz/bge-large-zh-v1.5:latest
  
  # # 平台名称
  platform_name: ollama
  # # 平台类型
  # # 可选值：['xinference', 'ollama', 'oneapi', 'fastchat', 'openai', 'custom openai']
  platform_type: ollama
  
  # # 是否自动获取平台可用模型列表。设为 True 时下方不同模型类型可自动检测
  auto_detect_model: true
  MODEL_PLATFORMS:
    - platform_name: ollama
      platform_type: ollama
      api_base_url: http://127.0.0.1:10800/v1
      api_key: EMPTY
      api_proxy: ''
      api_concurrencies: 5
      auto_detect_model: false
      llm_models:
        - deepseek-r1:1.5b
      embed_models:
        - quentinz/bge-large-zh-v1.5:latest
      text2image_models: []
      image2text_models: []
      rerank_models: []
      speech2text_models: []
      text2speech_models: []
  ```

  ​	注意：配置文件中用到的`deepseek-r1:1.5b`、`quentinz/bge-large-zh-v1.5:latest`通过ollama命令`ollama pull deepseek-r1:1.5b`、`ollama pull quentinz/bge-large-zh-v1.5:latest`获取即可，教程中只列出了需要修改的配置。

- 配置知识库路径（basic_settings.yaml）（可选）

```yaml
# 知识库默认存储路径
KB_ROOT_PATH: D:\LangChain-Chatchat-0.3.1\data\knowledge_base

# 数据库默认存储路径。如果使用sqlite，可以直接修改DB_ROOT_PATH；如果使用其它数据库，请直接修改SQLALCHEMY_DATABASE_URI。
DB_ROOT_PATH: D:\LangChain-Chatchat-0.3.1\data\knowledge_base\info.db

# 知识库信息数据库连接URI
SQLALCHEMY_DATABASE_URI: sqlite:///D:\LangChain-Chatchat-0.3.1\data\knowledge_base\info.db
```

- 配置知识库（kb_settings.yaml）（可选）

​	默认使用 `FAISS` 知识库，如果想连接其它类型的知识库，可以修改 `DEFAULT_VS_TYPE` 和 `kbs_config`。

4. 初始化知识库

进行知识库初始化前，请确保已经启动模型推理框架及对应 `embedding` 模型。

```shell
chatchat kb -r
```

```shell
出现以下日志即为成功:
----------------------------------------------------------------------------------------------------
知识库名称      ：samples
知识库类型      ：faiss
向量模型：      ：bge-large-zh-v1.5
知识库路径      ：/root/anaconda3/envs/chatchat/lib/python3.11/site-packages/chatchat/data/knowledge_base/samples
文件总数量      ：47
入库文件数      ：42
知识条目数      ：740
用时            ：0:02:29.701002
----------------------------------------------------------------------------------------------------
总计用时        ：0:02:33.414425
```

5. 启动Langchain-Chatchat项目

```
chatchat start -a
```

出现以下界面即为启动成功:

![img](https://github.com/Yarao-Liu/images/blob/main/2025-05/image-20250214144048875_1746614594305.png)

6. 网络配置

   ```
   由于 chatchat 配置默认监听地址 DEFAULT_BIND_HOST 为 127.0.0.1, 所以无法通过其他 ip 进行访问。
   
   如需通过机器ip 进行访问(如 Linux 系统), 需要到 basic_settings.yaml 中将监听地址修改为 0.0.0.0。
   ```
   
   

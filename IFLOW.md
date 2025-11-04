# MonkeyOCR 项目概述

## 项目简介

MonkeyOCR 是一个基于结构-识别-关系三元组范式的文档解析系统，旨在简化传统模块化方法的多工具管道，同时避免使用大型多模态模型进行全页文档处理的低效性。该项目支持中英文文档解析，具有高效的模型推理速度，并支持多种 GPU 硬件。

使用方式：必须进入conda activate MonkeyOCR环境:source /environment/miniconda3/etc/profile.d/conda.sh && conda activate MonkeyOCR


## 技术架构

MonkeyOCR 采用 Structure-Recognition-Relation (SRR) 三元组范式：

1. **结构检测 (Structure Detection)**：使用 DocLayout-YOLO 或 PP-DocLayout_plus-L 模型检测文档布局结构
2. **内容识别 (Content Recognition)**：利用 Qwen-VL 系列模型进行文本、公式和表格等内容识别
3. **关系预测 (Relation Prediction)**：通过 LayoutReader 模型预测文档元素间的阅读顺序和关系

## 目录结构

```
MonkeyOCR/
├── api/                 # FastAPI 服务实现
├── demo/                # Gradio 演示应用
├── docker/              # Docker 部署相关文件
├── docs/                # 文档和安装指南
├── magic_pdf/           # 核心文档解析模块
├── model_weight/        # 模型权重文件
├── tools/               # 工具脚本
├── asserts/             # 静态资源文件
├── parse.py             # 命令行解析工具
├── model_configs.yaml   # 模型配置文件
├── requirements.txt     # 项目依赖
└── README.md            # 项目说明文档
```

## 安装和运行

### 依赖安装

```bash
pip install -r requirements.txt
```

### 模型权重下载

从 HuggingFace 下载模型权重：

```bash
pip install huggingface_hub
python tools/download_model.py -n MonkeyOCR-pro-3B
```

或从 ModelScope 下载：

```bash
pip install modelscope
python tools/download_model.py -t modelscope -n MonkeyOCR-pro-3B
```

### 命令行使用

必须进入conda activate MonkeyOCR环境:source /environment/miniconda3/etc/profile.d/conda.sh && conda activate MonkeyOCR

```bash
# 端到端解析
python parse.py input_path

# 单任务识别（文本/公式/表格）
python parse.py input_path -t text/formula/table

# 文件夹处理
python parse.py /path/to/folder -g 20

# 指定输出目录和配置文件
python parse.py input_path -o ./output -c config.yaml
```

### Gradio 演示

```bash
python demo/demo_gradio.py
```

访问地址：http://localhost:7860

### FastAPI 服务

```bash
uvicorn api.main:app --port 8000
```

API 文档地址：http://localhost:8000/docs

### Docker 部署

```bash
cd docker
docker compose build monkeyocr
docker compose up monkeyocr-demo
```

## 核心组件

### 1. parse.py
命令行工具，支持多种解析模式：
- 端到端文档解析
- 单任务识别（文本、公式、表格）
- 文件夹批量处理
- 页面分割输出
- 自定义配置

### 2. Gradio 演示 (demo/demo_gradio.py)
交互式 Web 界面，提供：
- PDF/图像文件上传和预览
- 文档解析和结果展示
- 页面翻页功能
- Markdown 结果渲染
- 结果文件下载

### 3. FastAPI 服务 (api/main.py)
RESTful API 服务，提供：
- OCR 任务接口（文本、公式、表格提取）
- 文档解析接口
- 异步处理支持
- 结果文件打包下载

### 4. 核心解析模块 (magic_pdf/)
包含文档解析的核心逻辑：
- 数据集处理 (dataset/)
- 模型接口 (model/)
- 预处理和后处理 (pre_proc/, post_proc/)
- 工具函数 (utils/, libs/)

## 配置说明

### model_configs.yaml
主要配置文件，包含：
- 设备配置（cuda/cpu/mps）
- 模型权重路径
- 模型目录
- 布局检测配置
- 聊天模型配置

### 主要配置项
```yaml
device: cuda
weights:
  doclayout_yolo: Structure/doclayout_yolo_docstructbench_imgsz1280_2501.pt
  PP-DocLayout_plus-L: Structure/PP-DocLayout_plus-L
  layoutreader: Relation
models_dir: model_weight
layout_config:
  model: PP-DocLayout_plus-L
  reader:
    name: layoutreader
chat_config:
  weight_path: model_weight/Recognition
  backend: transformers
  data_parallelism: 1
  model_parallelism: 1
  batch_size: 10
```

## 开发约定

1. **Python 版本**：建议使用 Python 3.8+
2. **依赖管理**：通过 `requirements.txt` 管理项目依赖
3. **代码风格**：遵循 PEP 8 规范
4. **类型提示**：使用类型注解提高代码可读性
5. **异步支持**：API 服务使用异步处理提高并发性能
6. **日志记录**：使用 loguru 进行日志记录
7. **配置管理**：通过 YAML 文件进行配置管理
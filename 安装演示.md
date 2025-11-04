# 启动命令

# 基础使用
python parse.py path/to/your.pdf

# 指定输出路径和配置文件
python parse.py path/to/your.pdf -o ./output -c config.yaml

# 指定模型路径和配置文件
python parse.py path/to/your.pdf -m model_weight/Recognition -c config.yaml

# 硬件限制：

批处理=10，应当使用rtx 4090

# 遇到paddle问题：
 PaddlePaddle 版本不兼容导致的。问题的根源是 PaddleX 使用了在较新版本 PaddlePaddle 3.x 中已被移除的 API。

 办法：
 安装兼容的 PaddlePaddle 3.0.0 版本(推荐)

# 卸载当前版本
pip uninstall paddlepaddle paddlepaddle-gpu -y

# 安装 PaddlePaddle 3.0.0 GPU 版本(CUDA 12.4 使用 cu118 索引,向后兼容)
python -m pip install paddlepaddle-gpu==3.0.0 -i https://www.paddlepaddle.org.cn/packages/stable/cu118/

# 验证安装
python -c "import paddle; print('PaddlePaddle version:', paddle.__version__)"

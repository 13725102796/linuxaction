命令持久化
nohup command &          # 输出到 nohup.out
nohup command > output.log 2>&1 &  # 自定义输出文件

command &
disown -h %1 
后台运行xinfer
nohup xinference-local --host=0.0.0.0 --port=9997 > ~/xinference.log 2>&1 & 

[1] 3967329
[1] 4067831
nohup python time.py > tips.log 2>&1 &
ps -ef|grep python # 查找nohup 用 python进程

kill 12345

pytorch 安装版本
https://pytorch.org/get-started/locally/ 要查找对应cuda版本 再安装

# 创建并激活虚拟环境
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate     # Windows

python npm 

feiseliuli





### 步骤1
# 生成源代码分发包（.tar.gz）和二进制分发包（.whl）
python setup.py sdist bdist_wheel
twine upload dist/*
# 创建或编辑 ~/.pypirc  （Linux/macOS）或 C:\Users\用户名\.pypirc（Windows）


pip install --upgrade npmname
pip show npmname
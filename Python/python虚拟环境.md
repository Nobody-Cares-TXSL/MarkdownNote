# python 虚拟环境

## venv
> Python 的 venv 模块是 Python 3.3 及以上版本内置的工具，用于创建和管理虚拟环境。  
> 它允许你为每个项目创建一个隔离的 Python 环境，避免不同项目之间的依赖冲突。

命令格式：`python3 -m venv 环境名称`
> 通常用 .venv 或 venv（以点开头可以隐藏文件夹，便于 Git 忽略）

激活虚拟环境: `source .venv/bin/activate`

退出虚拟环境: `deactivate`

导出当前环境的包列表: `pip freeze > requirements.txt`

文件安装依赖: `pip install -r requirements.txt`
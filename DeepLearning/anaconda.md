1. 创建虚拟环境：`conda create -n xxxxx python=3.6 `
2. 删除虚拟环境：`conda remove -n xxxxx --all` 
3. 进入虚拟环境：`conda activate xxxxx` 
4. 退出虚拟环境：`conda deactivate xxxxx` 
5. 查看环境列表：`conda env list1`
6. 安装ipykernel:`pip install ipykernel`
7. 添加虚拟环境:`kernel:python -m ipykernel install --user --name xxxxx`
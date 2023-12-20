
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes

配置文件为 ".condarc"

```bash
show_channel_urls: true

#配置清华的镜像服务器，以解决conda下载文件速度慢的问题。
channels:
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/Paddle/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/fastai/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/

```

### To activate this environment, use
```
$ conda activate word-tools

$ conda activate D:\conda-envs\word-tools
```

### To deactivate an active environment, use
`$ conda deactivate`


### 创建环境
```
conda create -n word-tools python=3.8

conda create --prefix D:/conda-envs word-tools python=3.8
```

### 查看环境
`conda env list`

### 删除环境
`conda remove -n test --all`




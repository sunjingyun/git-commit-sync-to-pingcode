# git-commit-sync-to-pingcode

### 效果展示:

![commit关联PingCode工作项](https://z3.ax1x.com/2021/11/03/IAzFyV.jpg)

### 配置PingCode的权限
#### 新建凭据

进入PingCode的企业后台->凭据管理->新建应用

![新建应用](https://z3.ax1x.com/2021/11/03/IAzuWR.jpg)

应用名称为：Git，鉴权方式为：Client Credentials，权限：只需要将“开发”设置为读写即可。

#### 拷贝凭据

回到列表页面，鼠标悬停在ClientId和Secret上，然后点击“点击复制”将ClientId和Secret拷贝出来备用。

### 配置Git的服务端
#### 1. 在服务器中安装`python`环境

要求版本3.6.9+：[官网下载地址](https://www.python.org/)

#### 2. 配置Python脚本

将`send-commit.py`文件拷贝到服务器中，例如`/data`路径下，然后对脚本进行配置：
```
chmod +x /data/send-commit.py
vim /data/send-commit.py
```
将文件第10行`client_id`的值替换为上文拷贝的ClientId值，
将文件第11行`client_secret`的值替换为上文拷贝的Secret值，
如果脚本用于将数据发送到私有部署环境的PingCode，将文件第12行`rest_api_root`的值替换为私有部署的REST API地址。

#### 3. 配置Git脚本

进入一个Git代码仓库中，例如：`/data/git/project.git`。
```
cd /data/git/project.git/hooks
```
将`post-receive`文件拷贝到当前路径下，然后对脚本进行配置：
```
chmod +x post-receive
vim post-receive
```
将文件第3行`repo_name`的值替换为当前目录所属仓储的项目名，
将文件第4行`repo_path`的值替换为当前目录所属仓库的根路径，
将文件第5行`script_path`的值替换为`第2步. 配置Python脚本`里确定的脚本路径，例如`/data`。

### 客户端提交代码

向代码仓库推送分支，如果branch name中提及PingCode工作项（#工作项编号），那么这个分支将自动关联到这个PingCode工作项上，
向代码仓库推送代码，如果commit message中提及PingCode的工作项即可，那么这个提交将自动关联到这个PingCode工作项上。
需要注意的是，如果分支已经关联到工作项上，那么这个分支上后续的代码提交将自动和这个工作项关键。

### 一些已知问题
1. 如果在本地创建分支，然后本地提交代码，最后一起向远程推送（new branch + new commits），那么这个脚本只会关联分支，而会忽略同批次推送的commits(related branch, ignore commits)。建议先向远程推送分支，在进行代码开发，然后推送代码。
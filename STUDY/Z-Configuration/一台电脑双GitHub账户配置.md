

## 问题描述
- 一个电脑项配置两个GitHub账户，一个账户是cdongt，对应邮箱是 cdongt@163.com；
- 另一个账户是muwinter，对应邮箱是 2124413584@qq.com 。
- 各自都有远程仓库，想要拉取远程仓库，并绑定对应账户，并用对应账号进行git操作。


## 任务目标

1.拉取cdongt账户的远程私人仓库neetcode75 地址是：(https://github.com/cdongt/neetcode75)
  - 本地创建一个与远程仓库同名的文件夹，并将远程仓库中的所有文件和目录都下载到该文件夹中。存放在：D:\Git_Repo
  - 保证本地的neetcode75仓库与cdongt账户绑定，之后都是用cdongt账户进行git操作
  - 保证muwinter不能操作neetcode75仓库，只能通过cdongt账户操作

<br>

---

## 配置过程
### 步骤一：配置两个GitHub账户

#### 1. 生成SSH密钥对

##### 1.1 为cdongt账户生成密钥
打开Git Bash，执行以下命令：
```sh
ssh-keygen -t rsa -C "cdongt@163.com" -f %USERPROFILE%\.ssh\cdongt_rsa
```

##### 1.2 为muwinter账户生成密钥
```sh
ssh-keygen -t rsa -C "2124413584@qq.com" -f %USERPROFILE%\.ssh\muwinter_rsa
```

#### 2. 配置SSH Agent
打开Git Bash，确保SSH Agent正在运行：
```sh
eval "$(ssh-agent -s)"
```

添加密钥到SSH Agent：
```sh
ssh-add %USERPROFILE%\.ssh\cdongt_rsa
ssh-add %USERPROFILE%\.ssh\muwinter_rsa
```

#### 3. 编辑SSH配置文件
打开Notepad：
```sh
notepad %USERPROFILE%\.ssh\config
```

粘贴以下内容并保存：
```sh
# cdongt account
Host github.com-cdongt
  HostName github.com
  User git
  IdentityFile %USERPROFILE%\.ssh\cdongt_rsa

# muwinter account
Host github.com-muwinter
  HostName github.com
  User git
  IdentityFile %USERPROFILE%\.ssh\muwinter_rsa
```

#### 4. 将SSH公钥添加到GitHub

##### 4.1 登录cdongt账户
前往https://github.com/settings/keys，点击“New SSH key”。
输入标题（如“cdongt’s laptop”），粘贴%USERPROFILE%\.ssh\cdongt_rsa.pub的内容。
点击“Add SSH key”。

#### 4.2 登录muwinter账户
前往https://github.com/settings/keys，点击“New SSH key”。
输入标题（如“muwinter’s laptop”），粘贴%USERPROFILE%\.ssh\muwinter_rsa.pub的内容。
点击“Add SSH key”。

<br>

---

### 步骤二：拉取远程仓库并绑定账户

#### 1. 拉取cdongt账户的远程仓库

##### 1.1 先在github上创建仓库（如果已有仓库，略过此步骤）

##### 1.2 使用HTTPS URL克隆仓库

- 先定位到要克隆仓库的本地文件夹中
- 用`HTTPS`的方式克隆

```sh
git clone git@github.com-cdongt:cdongt/neetcode75.git . # 这是SSH
git clone https://github.com/cdongt/EXAM.git # 这是HTTPS
```

#### 2. 设置本地仓库的用户配置
在仓库根目录下，设置user.name和user.email为cdongt账户的配置，使用--local选项：
```sh
git config --local user.name "cdongt"
git config --local user.email "cdongt@163.com"

##顺便记一下 muwinter的
git config --local user.name "muwinter"
git config --local user.email "2124413584@qq.com"
```


#### 3. 本地仓库和远程仓库绑定

将本地文件夹直接推送到cdongt账户的github变成新的仓库，并且与cdongt账户绑定

```sh
   git init

   git config --local user.name "cdongt"
   git config --local user.email "cdongt@163.com"

   git remote add origin git@github.com-cdongt:cdongt/ob_repo_sync.git
   # @后面是config 文件中的Host
   git add .
   git commit -m "Initial commit"
   git push -u origin main

```

#### 特别注意

##### 出现 erro ：fatal: detected dubious ownership in repository at 'D:/Git_Repo/neetcode75'
- 错误原因：错误信息再次指出Git检测到仓库目录的所有权存在问题
- 解决方法：通过将该目录添加到Git的安全目录列表来绕过这个所有权检查
- 命令操作：`git config --global --add safe.directory 'D:/Git_Repo/neetcode75'`
- 结果说明：这条命令会让Git把D:/Git_Repo/neetcode75目录标记为安全的，即使所有权不匹配，也不会阻止Git命令的执行。






## 推送过程的特殊情况
### 情况1：推送失败：某些文件过大（超过 Git 托管平台的限制）

> [!cite]+ 场景描述
>`git push`的时候发现文件太大，无法推送，但是已经`git commit`了，需要撤回最新提交


#### 操作步骤
#####  步骤一：撤销最近一次提交（保留工作区文件）

```
git reset --mixed HEAD~1
```

- `--mixed` 会保留工作区的修改，但清空暂存区
- 操作对象：仅针对本地仓库，与远程仓库无关。
- 具体效果 
	- 撤销最近一次 `git commit` 的内容，HEAD 指针回退到上一个提交。
	- 暂存区被重置为回退后的状态（即丢弃最后一次 `git add` 的内容）。
	- 工作目录中的文件保持不变（修改仍存在，但需要重新 `git add`）。


##### 步骤二： 添加 .gitignore

##### 步骤三：重新提交和推送



## git常用命令

### 强制覆盖本地代码，本地仓库更新到最新状态
#### 强制覆盖 
```python
git fetch --all
git reset --hard origin/master
git pull
```

#### 强制覆盖本地命令（单条执行）
```python
git fetch --all && git reset --hard origin/master && git pull
```

#### 确保仓库创建的文件（尚未被添加到版本控制中）也被删除
```python
git clean -n   # 预览将被删除的文件 
git clean -f   # 实际删除未跟踪的文件
``### git commit 后撤销某个文件的提交

### 常用更新3件套
```python
git add . 
git commit -m "note"
git push
```



## 多SSHkey时仓库初始化

配置完成之后,每次初始化仓库需要额外做的事情

```shell
#初始化仓库
git config user.email "xxx@xxx.com"
git config user.name "name"

#添加远程仓库,example按照~/.ssh/config 文件中的设置来
git remote add origin git@example.github.com:example/reponame.git
```

### 实际操作过程
#### 1.生成SSH公钥和私钥

```sh
ssh-keygen -t rsa -b 4096 -C "email@addr.com"
```

* 注意在保存路径的时候需要自己定义新的路径，文件名随意。
* ssh默认的文件是id_rsa。

> eg: 存储路径为 `~/.ssh/github_username2`
则会生成 `github_username2`和`github_username2.pub`文件。

#### 2.将生成的SSH公钥添加到github服务器
两种方法。
* 打开`github_username2.pub`文件,复制粘贴
* 在bash中执行`pbcopy < ~/.ssh/github_username2.pub`就可以放到剪切板，然后粘贴。

#### 3.向SSH的配置文件添加配置。

```sh
# 打开SSH的配置文件
vim ~/.ssh/config

# 添加如下字段
'''
Host username2.github.com # 匹配的域名（非实际的
	HostName github.com # 匹配到域名之后，替换为的实际域名
	IdentityFile ~/.ssh/github_username2 # 定位私钥文件
	User git@username2.github.com # 执行命令时输入的用户名
'''
# github给出的是以下字段
'''
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa
'''

# 启动ssh-agent
eval "$(ssh-agent -s)"
```

* `ssh-agent`的作用是接管私钥，在使用github给出的配置文件的情况下会自动架在config文件，并在SSH连接的时候使用适合的私钥。
* 在`config`文件中，
  * `Host`字段是匹配输入的域名。
  * `HostName`是连接的真正域名。

#### 4.测试配置文件的有效性

```sh
# 执行测试
ssh -T git@username2.github.com

# 还需要测试一下其他用户是否受影响
ssh -T git@username1.github.com
```

#### 5.初始化本地仓库
* 由于有多个github用户，为了避免混乱需要手动设定git的用户名和邮箱

```sh
git config  user.email "you@example.com"
git config  user.name "Your Name"
```

* 然后在添加远程仓库的时候需要注意替换用户名

```sh
git init
git add .
git commit -m "xxx"

# 这里需要注意
git remote add origin git@username2.github.com:username2/reponame.git

# 如果仓库已经添加licence，需要merge
git fetch

# allow的注意事项在这里：
'''
https://stackoverflow.com/questions/39761024/refusing-to-merge-unrelated-histories-failure-while-pulling-to-recovered-repos
'''
git pull origin master --allow-unrelated-histories

git push -u origin master
```


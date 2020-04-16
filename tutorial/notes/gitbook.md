### 1. 安装环境
去官网下载`node`的安装包.

由于`node`中自带的`npm`在默认情况下从属于root用户, 因此需要手动修改`npm`的文件权限属性. Mac用户默认安装路径的情况下修改`/usr/local/lib/node_modules/`文件夹及子文件夹的属性,添加当前的非root用户对文件夹的读写权限.

修改完毕后,即可不用`sudo`执行安装:

```shell
npm install -g gitbook-cli
```

### 2. 初始化
安装成功后,进入要生成gitbook的文件夹,初始化目录:

```shell
gitbook init
```

生成的文件中,`SUMMARY.md`是gitbook的目录文件, 使用缩进来控制从属关系.

### 3. 生成gitbook
在添加完成markdown文件之后,可以开始生成gitbook文件.

* 仅生成

```shell
# 默认生成一个 _book/ 文件夹
gitbook build
# 将生成的文件夹改名为 docs/
gitbook build ./ ./docs
```

* 生成并自带预览

```shell
gitbook serve
```

### 4. github page 的配置
创建一个public仓库, 仓库名决定 github page 的默认url, 格式如下:

> `<username>.github.io/<repo_name>/`

#### 4.1 设置 github page 的根目录
从仓库主页面点击 'Settings', 在'Options'标签栏下找到'GitHub Pages'项.

可以看到, 可以有多种方式选择根目录.
我为了方便,将gitbook放在`master`分支的`./docs/`文件夹中.

#### 4.2 设置git
需要注意的是添加`.gitignore`文件.注意过滤的文件如下:
* `_book/`
* `node_modules/`
* Mac下: `.DS_Store`

其他设置照常.

#### 4.3 生成gitbook
每次生成gitbook时,使用以下命令:

```shell
gitbook build ./ ./docs
```
生成好之后就可以`push`了.

### 5. VS Code 设置
Mac用户,平时写笔记时使用Typora. 在检查与生成gitbook时,使用VS Code比命令行方便许多.

1. 首先,使用 VS Code 打开项目文件夹.
2. 菜单栏`Terminal`\-`Run Task`, 点击新建一个task, 会生成`./.vscode/tasks.json`.
3. 编辑此task文件,实现一键生成gitbook, 省去打命令行的烦恼.

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "gitbook build ./ ./docs",
            "presentation": {
                "reveal": "always",
                "panel": "new"
            },
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```
4. 菜单栏,`Terminal`\-`Run Build Task` 即可生成.

此外, VS Code 支持文件右键拷贝路径, 这样方便`SUMMARY.md`的修改与编辑.
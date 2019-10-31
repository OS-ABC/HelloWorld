# git以及提交PR的指引

<!-- TOC -->
- [安装Git](#安装git)
    - [Windows](#windows)
    - [MacOS](#macos)
    - [Linux](#linux)
- [创建一个github账户](#创建一个github账户)
- [在本机上设置邮箱和用户名](#在本机上设置邮箱和用户名)
- [配置SSH key](#配置ssh-key)
- [检出仓库](#检出仓库)
- [修改内容](#修改内容)
- [提交PR](#提交pr)
- [PR步骤总结](#pr步骤总结)
<!-- /TOC -->

## 安装Git

### Windows

在链接 https://git-scm.com/downloads 下载git并安装, 安装过程中记得要勾选git bash工具, 默认编辑器推荐使用vscode

### MacOS

一般在mac中是自带git的, 若无git, 则可通过包管理器homebrew安装git, 具体见MacOS系统使用指引章节(TODO)

### Linux

Linux各发行版一般是自带git的, 若无git, 则用各发行版特定的包管理器安装, 具体见Linux各发行版使用指引章节(TODO)

## 创建一个github账户

使用邮箱注册https://github.com/

## 在本机上设置邮箱和用户名

git bash中执行
```
# 设置github账户名
git config --global user.name "你的github账户名"

# 设置github注册时的邮箱
git config --global user.email "你的邮箱"

```

## 配置SSH key

git bash中执行

```

ssh-keygen -t rsa -C “你的邮箱”
# 按回车均采取默认配置

cd ~/.ssh
cat id_rsa.pub
# 复制公钥内容
（cat  ~/.ssh/id_rsa.pub复制输出内容到github（windows在用户/用户/.ssh/id_rsa.pub））
```

打开github设置秘钥的界面 https://github.com/settings/keys


点击"New SSH key", 将公钥内容粘贴进去, 标题自己起一个. 之后就可以使用ssh进行clone/pull/push等操作了, 省去了繁琐的账号密码输入流程.


## 检出仓库
1. 将原仓库fork一份到自己账号下, 打开自己fork后的仓库页面

![fork](images/fork.png)

2、打开命令行窗口，输入 git clone 复制地址(使用ssh地址, 注意, **得是自己fork后的仓库地址, 下图中的地址是笔者自己的仓库地址, 每个人fork后的仓库地址都是不一样的**)

![使用ssh地址](images/use-ssh-03.png)

```bash
git clone git@github.com:js00070/HelloWorld.git
```


3、git命令执行结束后，在当前目录下出现了github上的工程项目源码

```bash
$ ls
HelloWorld
$ cd HelloWorld/
$ ls
code  doc  LICENSE  README.md

```


## 修改内容
1. 创建并切换分支

    ```bash
    git status # 查看当前git仓库状态, 确认处于master分支中

    git branch pr-test # 从master分支分出为pr-test的分支

    git checkout pr-test # 切换至pr-test分支

    ```

2. 修改内容

    使用任何自己喜欢用的编辑器/IDE修改内容
    
3. add commit push

    ```bash
    git status # 查看当前做了哪些修改

    git add . # . 表示当前目录 git add . 是把当前目录的所有修改添加到暂存区里

    git status # 确认下修改

    git commit -m 'this is a commit' # 输入commit信息, 简要概括下本次修改

    git log # 查看commit历史

    git push # 提交到自己的远程仓库

    ```

## 提交PR

到github自己的仓库主页, 发现会有一个`Compare&Pull Request`选项, 点击即可填写PR说明.

PR的标题最好以自己修改的模块文件路径开头, 方便维护者辨识(如本文件是`doc/Start:`), 然后简要说明下自己为什么做这部分修改, 以及做了什么修改, 达到了怎样的效果.

![](images/pr-01.png)



## PR步骤总结 
    1、将他人的仓库Fork成自己的仓库（访问该仓库页面，点击fork） 
    2、将自己的仓库clone到本地（git clone 自己仓库的URL） 
    3、创建特性分支（在GitHub上发送Pull Request时，一般都是发送特性分支。这样一来，Pull Request就拥有了更明确的特性[主题]，让对方了解自己修改代码的意图，有助于提高代码的审查效率） 
    4、做出自己需要的修改(可以用自己喜欢的编辑器修改)
    5、提交修改（git add… & git commit -m “…”） 
    6、创建远程分支（要从GitHub发送Pull Request，GitHub端的仓库中必须有一个包含了修改后代码的分支。git push origin 远程分支名） 
    7、发送Pull Request（登陆GitHub，切换到相应分支，点击Compare可查看分支之间的差别。点击New Pull Request，在随后显示的表单中填写本次进行Pull Request的理由，并提交即可）
    
更多git使用方法详见git章节(TODO)
或者参考https://git-scm.com/book/zh/v2

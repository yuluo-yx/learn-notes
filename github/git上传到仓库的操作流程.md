

# git上传到仓库的操作流程

- git常用命令

  - | 命令名称                             | 作用             |
    | ------------------------------------ | ---------------- |
    | git config --global user.name 用户名 | 设置用户签名     |
    | git config --global user.email 邮箱  | 设置用户邮箱     |
    | git init                             | 初始化本地仓库   |
    | git status                           | 查看本地库的状态 |
    | git add 文件名                       | 添加文件到暂存区 |
    | git commit -m "日志信息" 文件名      | 提交文件到本地库 |
    | git reflog                           | 查看历史记录     |
    | git reset --hard 版本号              | 版本穿梭         |

  - 分支命令

    - | 命令名称            | 作用                             |
      | ------------------- | -------------------------------- |
      | git branch 分支名   | 创建分支                         |
      | git branch -v       | 查看分支                         |
      | git checkout 分支名 | 切换分支                         |
      | git  merge 分支名   | 把指定位置的分支合并到当前分支上 |

## 1 设置用户签名（区分操作的人的身份）

**在第一次使用git的时候设置，只需要设置一次**

```git
git config --global user.name yuluo
git config --global user.emial yuluo829@aliyun.com
```

**文件位置在windows的家目录下的.gitconfig文件中 此处设置的邮箱git不会校验**

## 2 初始化本地仓库

`git init`

## 3 向本地仓库中添加文件到暂存区

`git add 文件名`

**此时的warning为：git自动替换了末尾的换行符，在windows中换行是CRLF Linux中是LF**

## 4 从暂存区撤回

`git rm --cached 文件名`

## 5 提交本地库

`git commit -m "日志信息" 文件名`

## 6 给远程仓库创建别名

`https://github.com/829yuluo/code.git` 仓库的远程地址太长记不住的时候，可以通过别名对远程提交和拉取

`git remote add 别名 地址`

设置别名之后出现的又两个 **fetch是拉取  push是推送**

## 7 推送到远程仓库

`git push 远程仓库别名/地址名 分支名`

git push notes <file>

## 8 问题总结

### 1 fatal: the remote end hung up unexpectedly

Total 1472 (delta 472), reused 0 (delta 0), pack-reused 0

```md
fatal: the remote end hung up unexpectedly
```

Everything up-to-date

空间不够，执行

```md
 git config --local http.postBuffer 524288000
```

再次上传

### 2 Everything up-to-date

加入git commit -m "msg"

之后再次推送即可


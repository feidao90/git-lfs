# git-lfs (Large File Storage)
git lfs管理

##  mac安装
brew install git-lfs
git lfs install

## 初始化 lfs的配置文件
git lfs track "<目录>/*"



## 卸载git lfs
sudo apt --purge remove git-lfs

## 删除lfs相关的filter
 #查看 git的全局配置
git config -l 
 #删除和lfs有关的配置
## 查看lfs相关的配置
git config -l|grep lfs
## 挨个删除
git config --global --unset filter.lfs.clean
git config --global --unset filter.lfs.smudge
git config --global --unset filter.lfs.process
git config --global --unset filter.lfs.required
git config --global --unset filter.lfs.required
 #删除 lfs的仓库地址  按需删除
git config --global --unset lfs.https://github.com/&&&&&&&


配置
使用 git lfs track 追踪需要使用 Git LFS 管理的文件。如：

git lfs track "*.psd"
也可以手动编辑 Git 仓库根目录下的 .gitattributes 文件，如：

*.psd filter=lfs diff=lfs merge=lfs -text
常用 Git LFS 命令
# 查看当前使用 Git LFS 管理的匹配列表
git lfs track

# 使用 Git LFS 管理指定的文件
git lfs track "*.psd"

# 不再使用 Git LFS 管理指定的文件
git lfs untrack "*.psd"

# 类似 `git status`，查看当前 Git LFS 对象的状态
git lfs status

# 枚举目前所有被 Git LFS 管理的具体文件
git lfs ls-files

# 检查当前所用 Git LFS 的版本
git lfs version

# 针对使用了 LFS 的仓库进行了特别优化的 clone 命令，显著提升获取
# LFS 对象的速度，接受和 `git clone` 一样的参数。 [1] [2]
git lfs clone https://github.com/user/repo.git
[1] git lfs clone 通过合并获取 LFS 对象的请求，减少了 LFS API 的调用，并行化 LFS 对象的下载，从而达到显著的速度提升。git lfs clone 命令同样也兼容没有使用 LFS 的仓库。即无论要克隆的仓库是否使用 LFS，都可以使用 git lfs clone 命令来进行克隆。
[2] 目前最新版本的 git clone 已经能够提供与 git lfs clone 一致的性能，因此自 Git LFS 2.3.0 版本起，git lfs clone 已不再推荐使用。

Git LFS 进阶使用
使用 Git LFS 的核心思想就是把需要进行版本管理、但又占用很大空间的那部分文件独立于 Git 仓库进行管理。从而加快克隆仓库本身的速度，同时获得灵活的管理 LFS 对象的能力。

默认情况下，只有当前 commit 下的 LFS 对象的当前版本才会被获取。

只获取仓库本身，而不获取任何 LFS 对象

如果自己的相关工作不涉及到被 Git LFS 所管理的文件的话，可以选择只获取 Git 仓库自身的内容，而完全跳过 LFS 对象的获取。

GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/user/repo.git
# 或
git -c filter.lfs.smudge= -c filter.lfs.required=false clone https://github.com/user/repo.git
注：GIT_LFS_SKIP_SMUDGE=1 及 git -c filter.lfs.smudge= -c filter.lfs.required=false 同样使用于其他 git 命令，如 checkout, reset 等。

获取当前 commit 下包含的 LFS 对象的当前版本

如果起初获取代码时，没有一并获取 LFS 对象，而随后又需要这些被 LFS 管理的文件时，可以单独执行 LFS 命令来获取并签出 LFS 对象：

git lfs fetch
git lfs checkout
# 或
git lfs pull
仅获取指定目录下的 LFS 对象

比如说，我们有一仓库，里面包含了许多源代码文件，以及一些图像、视频等资源文件，其目录结构如下：

zzz.buzz
├── images
│   ├── cat.png
│   ├── dog.gif
│   └── rabbit.webp
├── src
│   ├── buzz.css
│   ├── index.html
│   └── zzz.js
└── videos
├── chameleon.mp4
└── iguana.webm
其中的 images/** 以及 videos/** 是被 LFS 所管理的。

但是，如果只想取 images 文件夹，而不想获取 videos 文件夹下的文件的话，我们就可以选择配置 LFS 下载对象时仅包含 images 文件夹：

git config lfs.fetchinclude 'images/**'
随后，git checkout, git reset, git lfs fetch, git lfs pull 等命令就都会只处理所指定的文件夹。

类似地，我们也可以选择仅排除指定的文件夹：

git config lfs.fetchexclude 'videos/**'
也可以同时使用黑白名单规则，这样只有同时满足 include 规则和 exclude 规则的大文件才会被获取：

git config lfs.fetchinclude 'videos/**'
git config lfs.fetchexclude 'videos/chameleon.mp4'
# 在此例中，如此配置将只会获取 videos/iguana.webm 一个文件。
一次获取 LFS 对象的最近版本

Git LFS 相关命令在获取 LFS 对象时，默认仅会获取该对象当前被引用的版本，如果想要一次获取 LFS 对象的当前及最近版本的话，我们首先需要对最近进行定义：

git config lfs.fetchrecentcommitsdays 7
7 表示同时下载过去 7 天内的版本（相对于获取的 LFS 对象的时间），该项配置默认值为 0，即不获取过去的版本，而仅获取指定的版本。

有了对最近的定义后，我们可以选择在执行 git lfs fetch 命令时，加上 --recent 参数以同时获取最近版本；

或者配置

git config lfs.fetchrecentalways true
从而总是同时获取 LFS 对象的最近版本。

常见问题
在安装 Git LFS 之前，克隆了使用 Git LFS 的仓库，则被 Git LFS 管理的文件会被显示为文本指针，而非具体的文件。

查看这些文件指针，会发现类似如下内容：

version https://git-lfs.github.com/spec/v1
oid sha256:4b99dbe6fe6f646b2026de93481045bbf34f995559db15fce34d192f1f320ef4
size 156154
解决办法就是，手动执行获取 Git LFS 对象的命令：

git lfs fetch
git lfs checkout
# 或
git lfs pull
Git LFS 对象在本地仓库的存放位置？

通过 Git LFS 所管理的对象实际在本地的存储位置是在 .git/lfs/objects 目录下，该目录根据对象的 sha256 值来组织。

作为对比，Git 自身所管理的对象则是存储在 .git/objects 目录下，根据 commit, tree, blob, tag 的 sha1 值来组织。

已经使用 git lfs track somefile 追踪了某个文件，但该文件并未以 LFS 存储。

如果被 LFS 追踪管理的文件的大小为 0 的话，则该文件不会以 LFS 的形式存储起来。

只有当一个文件至少有 1 个字节时，其才会以 LFS 的形式存储。

注：一般使用 LFS 时，我们也不会用其追踪空文件，即使追踪了空文件，对于使用也没有任何影响。提到这点主要是为了消除在测试使用 LFS 时可能遇到的困惑。

执行 git lfs fetch 或 git lfs pull 时报错

batch request: exit status 255: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
如果在克隆仓库时使用了 SSH 协议，而本地的 SSH 私钥又有密码保护，那么向服务器获取文件时就会报错，因为目前 Git LFS 不会向用户请求密码，从而导致认证失败。

解决办法是使用 ssh-add 命令，预先加载好本地的 SSH 私钥，从而使得 Git LFS 能够访问到私钥。

使用 Git LFS 时，报错缺失协议 (missing protocol)或协议不支持 (unsupported protocol scheme)。

出现这种错误通常有两种原因：

其中第一种是克隆仓库时使用的地址没有包含用户名，如克隆时使用了类似 git clone github.com:user/repo.git 的命令，从而导致 Git LFS 错误地将服务器地址当作协议名来看待，而报出协议不支持的错误：

[z@zzz.buzz lfs-test]$ git push
Git LFS: (0 of 1 files) 0 B / 1 B
Post git.zzz.buzz:z/lfs-test.git/info/lfs: unsupported protocol scheme "git.zzz.buzz"
Post git.zzz.buzz:z/lfs-test.git/info/lfs: unsupported protocol scheme "git.zzz.buzz"
error: failed to push some refs to 'git.zzz.buzz:z/lfs-test.git'
解决办法就是在仓库地址中加上 git 用户名，如：

git remote set-url origin git@github.com:user/repo.git
第二种原因则是克隆仓库时使用的是 SSH 协议，而使用的 Git 服务器不支持在 SSH 下使用 Git LFS（如低于 8.12 版本的 GitLab），其解决办法为将克隆仓库时使用的 SSH 协议换为 HTTPS 协议即可。

如原先 origin 设置为 git@github.com:user/repo.git，则可以运行如下命令：

git remote set-url origin https://github.com/user/repo.git
随后再执行 Git LFS 相关的命令。

或使用 HTTPS 协议重新克隆仓库：

git clone https://github.com/user/repo.git
Cygwin 环境下使用 Git LFS 的注意事项
在 Cygwin 下我们可以使用专门为 Cygwin 编译的 git，也可以使用为 Windows 编译的 git，两者在功能上本质是相同的。

但其实一般推荐在 Cygwin 下使用专门为 Cygwin 编译的 git，因为至少有以下好处：

生成/处理的文件有正确的可执行位，core.filemode 也会和 linux 下一样被默认设为 true；
core.symlinks 和 linux 下一样不会被设定为 false（默认为 true）；
且 core.ignorecase 和 Windows 下一样会被设置为 true。
但是可惜的是，Git LFS 并没有专门为 Cygwin 编译的版本；而又幸运的是，为 Windows 编译的 Git LFS 也可以在 Cygwin 下使用，且在大部分情况下可以和专门为 Cygwin 编译的 Git 混合使用。

但是在执行 git lfs pull 或 git lfs checkout 命令时，混用为 Cygwin 编译的 Git 和为 Windows 编译的 Git LFS 就会由于两种环境对路径的不同解析而出错。

Error updating the git index:
error: ...: cannot add to the index - missing --add option?
fatal: Unable to process path ...
因而在执行以上两个命令时，必须同时使用为 Windows 编译的 Git 和 Git LFS，才不会出错。

要想执行为 Cygwin 编译的 git，可以执行命令 git 或 /usr/bin/git；
而要执行为 Windows 编译的 git，则执行命令 "/cygdrive/c/Program Files/Git/cmd/git"。
我们可以通过 git lfs env | head -2 来查看所用到的 Git 命令和 Git LFS 命令是来自 Windows 还是 Cygwin。

如使用 Cygwin 的 Git 和 Windows 的 Git LFS 会有以下结果：

git-lfs/1.5.4 (GitHub; windows amd64; go 1.7.4; git 71b637f1)
git version 2.5.1
而使用 Windows 的 Git 和 Windows 的 Git LFS 会有以下结果：

git-lfs/1.5.4 (GitHub; windows amd64; go 1.7.4; git 71b637f1)
git version 2.11.0.windows.1


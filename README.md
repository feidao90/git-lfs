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



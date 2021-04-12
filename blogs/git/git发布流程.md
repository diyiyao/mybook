#git发布流程

#开发环境
##1.1创建feature_v1.0分支
> + 创建分支名:`feature_v1.0` 命令:`git checkout master` `git checkout -b feature_v1.0`
> + 上架:`feature_v1.0` 需创建git临时分支
> + 更新:`feature_v1.0` 临时分支合并`feature_v1.0`

##1.2上架
> + 创建临时分支名:`dev_temp_0001` 命令: `git checkout master` `git checkout -b dev_temp_0001`
> + 合并分支:`git merge feature_v1.0`
> + 删除历史临时分支:`git branch -d  dev_temp_0000`

##1.3开发环境更新

> + 合并分支:`git checkout dev_temp_0001` `git merge feature_v1.0`


##2.1创建feature_v1.1分支
> + 创建分支名:`feature_v1.1` 命令:`git checkout master` `git checkout -b feature_v1.1`
> + 上架:`feature_v1.0` 需创建git临时分支
> + 更新:`feature_v1.0` 临时分支合并`feature_v1.0`

##2.2上架
> + 方案1:创建临时分支名:`dev_temp_0002` 命令: `git checkout master` `git checkout -b dev_temp_0002`
> + 合并分支:`git merge feature_v1.0` `git merge feature_v1.1` 合并顺序 按上架顺序来合并 先合并v1.0 再合并v1.1 
> + 方案2:创建临时分支名:`dev_temp_0002` 命令: `git branch dev_temp_0001` `git checkout -b dev_temp_0002`
    以上一个临时分支(dev_temp_0001)为基准，拉取新的临时分支
> + 合并分支:`git merge feature_v1.1` 合并顺序 按上架顺序来合并 先合并v1.0 再合并v1.1
> + 合并之前准备工作: `git log` 获取版本号【57955c461ca41dff144e2d1a1a6193e4377ce393】
> + 合并无冲突:删除历史临时版本
> + 合并出现冲突:在合并v1.1冲突时，获取合并冲突记录`git diff` 显示记录冲突内容
> + 撤销merge操作:`git reset --head 【merge前的版本号】` 
> + 解决合并冲突:将源分支代码合并到目标分支，在目标分支上解决冲突后提交push, 在gitlab上点合并冲突的Merge Rquests，再在penglai上点击重试。 是重试，不是重新部署。
    `git merge feature_v1.1` 或 `git pull feature_v1.1` 手动处理冲突后 git add '冲突文件' 然后git commit  '解决冲突' 
> + 删除历史临时分支:`git branch -d  dev_temp_0001`

#预发环境

##上架
> +  与开发环境上架流程一致 临时分支名pre_temp_XXXX

##更新

> + 与开发环境更新流程一致 临时分支名pre_temp_XXXX


#生产环境

## 发布
> + 以预发分支为基准，拉取创建分支Vxxxxx_release分支，然后以该版本为准，编译构建、
    系统部署
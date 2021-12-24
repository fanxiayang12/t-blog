# 医鸣

    emdata-links / emdata-elink-vue / emdata-dc-web 三个项目分支创建完成了， jenkins也同步修改了

            master(生产环境)
    preview(预发布)         dev(测试环境)

    dev_A(开发环境)
    dev_B

分支说明：
* master：主分支，生产与本地环境发布打包依赖该分支；（不能滞后preview分支太多）
* preview：预发布分支，预发布环境构建在该分支上；（稳定版本）
* dev：公共开发分支，持续测试环境代码在该分支上构建；（非稳定版本）
* dev_*：功能开发分支，功能或bug修复的临时分支；（从preview分支拉取）

开发流程（建议）：
    单人模式：
1. 从preview分支检出新的代码分支（dev_A）；
2. 进行开发或修复bug，开发完成后合并（merge）到dev分支，并发布测试环境提交测试；
3. 测试有bug需要修复，重复步骤2；
4. 若开发中依赖后续其它人员提交到preview分支的代码，执行rebase操作，指向preview最新的代码；
5. 测试完成后，先将开发分支（dev_A）rebase到preview分支，然后再进行合并；
6. 最后删除dev_A分支；
    多人模式：
7. 由其中一人从preview分支检出新的代码分支（dev_B）并推送到origin，然后其他人拉取该分支代码到本地进行开发；
8. 开发完成后，由其中一人执行单人模式下的2～5操作；
9. 若测试有问题需要修改，其他人需要重新检出origin/dev_B分支到本地后，开发，再提交，再重复2步骤，直到功能开发完毕；
10. 最后删除dev_B分支；

线上BUG修复：
1. 从master（或preview）分支检出代码，修复后，合并到master（或preview），同时合并到dev分支；
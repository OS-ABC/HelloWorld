有时候当不小心上传了一个很大的文件到github上时，后面在git clone的时候需要等很长的时间（这是因为git克隆代码库时会将所有历史的文件都保存下来），即使你删除了这个文件，但是为了实现正常的分支切换，这个文件的信息依然保存在代码库里。或者有时将一些私密的文件不小心上传了（会泄露个人隐私），所以为了解决这个问题，可以考虑重置代码库，即将所有的历史提交信息删除。

- 尝试 运行 git checkout --orphan latest_branch （拉分支）
- 添加所有文件git add -A
- 提交更改git commit -am "commit message"（提交）
- 删除分支git branch -D master（删除主分支）
- 将当前分支重命名git branch -m master
- 最后，强制更新存储库。git push -f origin master

————————————————
版权声明：本文为CSDN博主「Sleep_god」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Sleep_god/article/details/87870174

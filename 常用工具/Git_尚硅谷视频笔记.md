视频地址：[02 - Git - 为什么学习Git软件_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1wm4y1z7Dg/?p=2&spm_id_from=pageDriver&vd_source=cfd5f037105c34b1c9b697da20845d60)

#### 1. 为什么学习Git软件

- 资源管理：（SCM）软件配置管理---是对工作成果的一种有效保护
- SCM的软件：
	- Visual SourceSafe 集中式版本控制系统--收费，开发效率低
	- Concurrent Version System 
	- Subversion
 以上软件基于中央服务器管理代码
---
**Git：开源的分布式版本控制系统，基于网络管理代码**
perfoece：对美术资源的管理系统

#### 2.  Git的一些概念

##### 2.1 版本控制

1. 软件版本
- JDK 1.8
- MYSQL 5.7 / 8.0

2. 文件版本
- 保存重要的历史记录
- 恢复数据

3. 版本控制软件的基础功能
- 保存和管理文件
	- 这个软件需要能自动管理文件版本

- 提供客户端工具进行访问

- 提供不同版本文件的**比对功能**

##### 2.2 集中式、分布式

1. 集中式

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230414222610.png)

对文件加锁修改，一段时间内只有一个人修改。

2. 分布式版本控制（依赖网络）

#### 3. Git客户端（GitDesktop）
40个16进制的数字组成的版本号


##### 分支原理
![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230415110555.png)

在一个版本库中修改会产生很多的版本文件，需要解决多次冲突

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230415110752.png)

开发人员先在副本修改，最后解决一次冲突

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230415111033.png)

文件名相同，内容不同怎么办？
需要手动比对两个文件的内容，解决冲突之后再commit

##### 标签
Create the tag ，给上一个标签，比如合并操作，我们可以写上【合并订单】

##### 远程仓库（gitee同理）
> gitee是国内的，访问会更快，但是创建时只能私有
1. github创建仓库、创建时可写readme文件
2. 与github Desktop逻辑类似，可以执行创建文件、分支、等操作
3. 与本地的github Desktop联动，clone代码到Desktop，这需要自己的github账号登录到Desktop，点击按钮即可；在本地修改代码后也可以通过pull，将代码上传到远程。

##### readme、ignore
备份文件可以直接忽略掉，也可以直接忽略带某一后缀名（例如.bak）的文件。

##### 文件图标与比对功能

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230416102613.png)
- 红减-代表删除文件
- 黄加-代表更新文件
- 绿加-代表新增文件

#### 4. Git 版本号

##### 文件操作
SHA-1：40位
![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/20230416161034.png)

定位仓库中的文件 2 + 38 （文件夹 + 文件名）

1. 创建项目时的初始信息
![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417160140.png)

三种状态 对应 三个版本号
这是创建项目时的初始信息

2. 当新建一个文件时

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417160555.png)
 
- 新建文件会指向上次提交的文件
- 其文件状态表示了两份文件内容

3. 修改文件时

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417161352.png)

- 修改文件会指向上次提交的文件
- 其文件状态会指向修改前指向的文件的指向文件内容 和 修改后的文件内容

4. 删除文件时

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417161948.png)

- 删除文件就是不指向文件内容了
- 而是直接只与指向的文件的指向文件内容关联

##### 分支操作

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417162902.png)


![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417163437.png)

#### 5. Git 命令

![image.png](https://rainbowdj-1311333461.cos.ap-guangzhou.myqcloud.com/obsidian/research/20230417164018.png)


git init 初始化仓库，与工具区别在与更“空”

git clone https://github.com/upup99/test-git-vs.git 从网页上下载代码

	- git clone https://github.com/upup99/test-git-vs.git rename-vs
	- 重命名


git config (--global) user.name daic

git config (--global) user.email 1204938483@qq.com



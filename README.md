# Git随手写

[1.原理](#原理)
[2.基本操作](#基本操作)
[3.分支管理](#分支管理)
[4.标签管理](#标签管理)
[5.时光穿梭](#时光穿梭)
[6.工作流](#工作流)

## 原理

### 1. [二叉树(多叉树)的最近公共祖先](https://github.com/yangyiRunning/DataStructureAlgorithmsJava)

LeetCode 236题
```
package ds.tree.leetcode236;

/**
 * 二叉树的最近公共祖先
 * LeetCode 236 https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/
 *
 * @author yangyi 2021年02月17日23:43:14
 */
public class Solution {

    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        } else if (left == null && right != null) {
            return right;
        } else if (left != null && right == null) {
            return left;
        } else if (left == null && right == null) {
            return null;
        }
        return null;
    }

    private TreeNode createTree() {
        TreeNode node_3 = new TreeNode(3);
        TreeNode node_5 = new TreeNode(5);
        TreeNode node_1 = new TreeNode(1);
        TreeNode node_6 = new TreeNode(6);
        TreeNode node_2 = new TreeNode(2);
        TreeNode node_0 = new TreeNode(0);
        TreeNode node_8 = new TreeNode(8);
        TreeNode node_7 = new TreeNode(7);
        TreeNode node_4 = new TreeNode(4);
        node_3.left = node_5;
        node_3.right = node_1;
        node_5.left = node_6;
        node_5.right = node_2;
        node_2.left = node_7;
        node_2.right = node_4;
        node_1.left = node_0;
        node_1.right = node_8;
        return node_3;
    }

    private TreeNode p() {
        return new TreeNode(5);
    }

    private TreeNode q() {
        return new TreeNode(1);
    }

    public static void main(String[] args) {
        System.out.println(new Solution().lowestCommonAncestor(new Solution().createTree(), new Solution().p(), new Solution().q()).val);
    }

}
```

### 2. 分布式仓库

相比SVN等中央仓库的两个特点:

1. 脱离中央式仓库做版本管理(提交时不用联网)，提交并push之前可以继续写代码
2. 仓库比较大(不是问题)
3. SVN还在，比如游戏开发(大量媒体文件，仓库巨大)

### 3. 4个分区

工作区 --> 暂存区 --> 本地仓库 -> 远程仓库

## 基本操作

### 1. clone(远端git仓库地址统一用SSH格式)

#### 普通克隆

```
git clone git@github.com:yangyiRunning/git-up.git
```

#### 递归克隆

```
git clone --recursive git@github.com:yangyiRunning/git-up.git
```

### 2. 关联仓库

#### 将你的本地代码库关联到一个新建的空白的git远端仓库

```
git remote add origin git@github.com:yangyiRunning/git-up.git
```

#### 将你的本地代码库关联到一个已有的git远端仓库

```
git remote set-url --add origin git@github.com:yangyiRunning/git-up.git
```

### 3.查看日志

#### 普通查看

```
git log
```

#### 带图表查看

```
git log --graph
```
#### 带图标查看且只显示每次commit的核心摘要信息，去除commit的多余的废话

```
git log --oneline --graph
```

### 4. 查看/追溯【你的】操作记录

```
git reflog
```

### 5. 查看当前状态

```
git status
```

### 6. 将改动从工作区添加到暂存区

```
git add .
```

```
git add xxxx.java
```

**.**代表添加全部
**xxxx.java**代表添加文件名
注:  如果按照严格意义上的规范，git add时要精确到单个的文件，除非对自己的这波add特别有信息，保证不会add上本不应该add上的文件，可以使用git add .

### 7. 将改动从暂存区撤回到工作区

```
git reset HEAD xxxx.java
```

注: 只是撤回至工作区，改动本身并不会丢失。

### 8. 将改动从回滚，从工作区抹去

```
git checkout xxxx.java
```

注: 谨慎操作，除非确定自己的某个文件的改动不想要了，再进行操作。checkout后改动消失。

### 9. 将改动从暂存区提交至本地仓库

```
git commit -m "本次提交的信息"
```

```
git commit 
```

注: 
提交信息简单时，一行就能描述清楚，采用**git commit -m "本次提交的信息"**。
提交信息复杂时，长篇大论或者必须保持有一定的多行的格式，采用**git commit **，此时会进入到一个二级界面，在此界面编辑commit message，编辑完后**:wq**保存并退出。**:q**是不存在直接退出。(shell命令行)

### 10. 将改动从本次仓库推送至远程仓库

普通push

```
git push origin branch_name
```

强制push

```
git push origin -f branch_name
```

注: 要确保本地完全无误之后再进行推送，合并分支，擦除log，修改log，追加commit，rebase等一系列操作，它们的前提都是在未进行push之前，在本地随便折腾，但是一旦push之后再进行上述等一系列操作，除非强制push，否则无法推送。(用Gerrit等源码review工具除外，因为此类工具可以将commit提交打回到提交者手中令其重提)。而强制推送是危险操作，有些repo并不开放此权限。比如PHA。但强制推送并非一无是处，后续介绍rebase时会详细阐述。

### 11. 拉取仓库

```
git pull --rebase origin branch_name
```

注: 强烈推荐使用**git pull --rebase origin branch_name**作为拉取习惯。

原因: 
1. 默认的拉取操作**git pull origin branch_name**相当于git fetch origin branch_name + git merge branch_name，语义为**拉取且合并分支**，会造成大量形如**Merge branch 'sealmic_dev' of ssh://pha.rongcloud.net:2222/source/dSMANW into sealmic_dev**这种新生的废弃的空的节点，丑陋且毫无意义，还会让分支分叉。

![](https://tva1.sinaimg.cn/large/008i3skNly1gr6d1g1048j30wo034t9m.jpg)

2. 推荐的**git pull --rebase origin branch_name**相当于git fetch origin branch_name + git rebase branch_name，语义为**拉取且rebase分支**，保持一个分支干净，不分叉，昂首挺胸一条线向前走的关键奥义就是rebase。后续会针对rebase进行详细表述。

![](https://tva1.sinaimg.cn/large/008i3skNly1gr6d5ebbdrj307y0pftca.jpg)

## 分支管理

### 1. 拉取分支

```
git pull
```

注: 拉取此项目的全部内容，包括文件、分支、tag、log等近乎关乎项目的所有，之所以推荐此操作是因为我们往往不知道远端仓库都有哪些分支，加之如果远端仓库分支较多，需要一个一个拉取。显然操作很费时费力。

### 2. 切换已有分支(包括从远端直接pull下来的分支)

```
git checkout branch_name
```

注: chekout有2个语义，后面跟file_name代表回滚改动代码，后面跟branch_name代表切换分支。切勿记混。

### 3. 查看本地分支

```
git branch
```

### 4. 查看远端分支

```
git branch -r
```

注: r语义为remote

### 4. 查看所有分支(包括本地和远端)

```
git branch -a
```

注: a语义为all

### 5. 新建分支

```
git branch branch_name 
```

### 6. 切换的同时新建分支

```
git checkout -b branch_name
```

注: 操作等价于 git checkout branch_name + git branch branch_name

### 7. 删除本地分支

```
git branch -D branch_name
```

### 8. 删除远程分支

```
git push origin -d branch_name
```

### 9. 合并分支

```
git merge --no-commit --squash branch_name
```

注: 当B分支的feature功能开发完毕后，要合并至A分支，强烈建议使用上述命令。语义为: 合并分支，且不自动产生提交记录，合并过来之后由自己重新commit，且将此次新生的commit message压缩成一条commit id记录。此操作进一步保证了git log的干净整洁。

### 10. pick分支

pick 单条commit记录

```
git cherry-pick commit_id
```

注: 可以将**本仓库内的**任何一个commit_id pick至当前分支的尾部，commit message保持不变，无需重新commit。

pick 多条commit记录

```
git cherry-pick commit_id1...commit_id2
```

注: 语义同单条pick，多条commit_id的规则为前开后闭区间，即 **(commit_id1...commit_id2]**。

## 标签管理

### 1. 给当前停留的commit_id打tag

```
git tag tag_name
```

### 2. 给指定的commit_id打tag

```
git tag -a commit_id
```

### 3. 查看tag

```
git tag
```

### 4. 查看某个tag的详情

```
git show tag_name
```

### 5. 删除本地仓库tag

```
git tag -d tag_name
```

### 6. 推送全部tag至远端仓库

```
git push origin --tags
```

### 7.推送指定tag至远端仓库

```
git push origin tag_name
```

### 8. 删除远端指定tag

```
git push origin :refs/tags/tag_name
```

### 9. 切换至某条tag

```
git checkout tag_name
```

## 时光穿梭

### 1. 回退到指定commit_id并保留当前工作区的修改

```
git reset --soft commit_id
```

### 2. 回退到指定commit_id不保留当前工作区的修改

```
git reset --hard commit_id
```

### 3. 回退到指定commit_id并且生成一次新的提交引用

```
git revert commit_id
```

## 工作流

### 多人协作一般步骤（推荐），以一次普通的提交为例

#### 1. 将自己当前工作区的改动收起

```
git stash
```

#### 2. fetch远程仓库代码同时rebase

```
git pull --rebase origin branch_name
```

#### 3. 检查并合并冲突

#### 4. 将自己当前工作区的改动弹出

```
git stash pop
```

#### 5. 将当前工作区改动加入至暂存区

```
git add .
```

#### 6. 将当前暂存区改动提交至本地仓库

```
git commit -m "commit message"
```

#### 7. 将本地仓库的commit_id提交至远端仓库

```
git push origin branch_name
```

### 在push之前修改log当中的某条commit message

#### 1. 找到想要被修改的commit_id的上一个commit_id

```
git rebase -i commit_id
```

注：此处的commit_id为要修改的commit_id的上一个id，因为修改的区间为 ( commit_id, commit_id ]

#### 2. 在弹窗的界面输入 -i 进去shell编辑模式

![](https://tva1.sinaimg.cn/large/008i3skNly1gs2m2pjahlj307y0ezgmo.jpg)

#### 3. 将想要修改的某一个commit_id前的pick改为edit

![](https://tva1.sinaimg.cn/large/008i3skNly1gs2mnbdryfj608i0fsq4602.jpg)

#### 4. 输入 :wq 保存并退出，此时进入rebase状态

![](https://tva1.sinaimg.cn/large/008i3skNly1gs2m419zl6j60kl072aas02.jpg)

#### 5. 将修改添加并提交

```
git add .
```

```
git commit --amend
```

#### 6. 正式修改曾经提交过的commit message

![](https://tva1.sinaimg.cn/large/008i3skNly1gs2m5b2hz3j308j0ekmy6.jpg)

#### 7. 完成commit message的修改

```
git rebase --continue
```

#### 8. 本地仓库完成log的修改，推送至远端仓库

```
git push origin branch_name
```

注:

1. 如果在上述任何一个环节想放弃rebase，则

```
git rebase --abort
```

2. 如果在上述任何一个环节想忽略本次rebase，则

```
git rebase --skip
```

3. 以上rebase操作的前提是**push之前**，也就是说rebase修改的是本地仓库的log，如果rebase的commit_id之前就已经push到远端仓库了，则此时无法直接push到远端仓库，除非强制push。**强制推送**为危险操作。有些平台并不允许。

```
git push origin -f branch_name
```
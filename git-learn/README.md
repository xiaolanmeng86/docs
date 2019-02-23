# [查看官方文档](https://git-scm.com/doc)
# 入门请点这里这里-->>>># [git入门概念](./git-base.pdf)
# 进阶 1: HEAD、master与branch
这一节主要是几个概念的解释：HEAD、master 以及 Git 中非常重要的一个概念： branch。
### 引用: commit### 的快捷方式

首先，再看一次 log：
git log
![](http://otlk76aak.bkt.clouddn.com/images/pastedGraphic.png)

第一行的 commit 后面括号里的 HEAD -> master, origin/master, origin/HEAD ，是几个指向这个 commit 的引用。在 Git 的使用中，经常会需要对指定的 commit 进行操作。每一个 commit 都有一个它唯一的指定方式——它的 SHA-1 校验和，也就是上图中每个黄色的 commit 右边的那一长串字符。两个 SHA-1 值的重复概率极低，所以你可以使用这个 SHA-1 值来指代 commit，也可以只使用它的前几位来指代它（例如第一个 78bb0ab7d541…16b77，你使用 78bb0ab 甚至 78bb 来指代它通常也可以），但毕竟这种没有任何含义的字符串是很难记忆的，所以 Git 提供了「引用」的机制：使用固定的字符串作为引用，指向某个 commit，作为操作 commit 时的快捷方式。
### HEAD:当前commit的引用

上一段里说到，图中括号里是指向这个 commit 的引用。其中这个括号里的 HEAD 是引用中最特殊的一个：它是**指向当前****commit****的引用**。所谓**当前 commit**这个概念很简单，它指的就是当前工作目录所对应的 commit。
例如上图中的当前 commit 就是第一行中的那个最新的 commit。每次当有新的 commit 的时候，工作目录自动与最新的 commit 对应；而与此同时，HEAD 也会转而指向最新的 commit。事实上，当使用 checkout、reset 等指令手动指定改变当前 commit 的时候，HEAD 也会一起跟过去。
总之，当前 commit 在哪里，HEAD 就在哪里，这是一个永远自动指向当前 commit 的引用，所以你永远可以用 HEAD 来操作当前 commit。
### branch
HEAD 是 Git 中一个独特的引用，它是唯一的。而除了 HEAD 之外，Git 还有一种引用，叫做 branch（分支）。HEAD 除了可以指向 commit，还可以指向一个 branch，当它指向某个 branch 的时候，会通过这个 branch 来间接地指向某个 commit；另外，当 HEAD 在提交时自动向前移动的时候，它会像一个拖钩一样带着它所指向的 branch 一起移动。
例如上面的那张图里，HEAD -> master 中的 master 就是一个 branch 的名字，而它左边的箭头 -> 表示 HEAD 正指向它（当然，也会间接地指向它所指向的 commit）。
![](http://otlk76aak.bkt.clouddn.com/images/1__%23$!@%25!%23__pastedGraphic.png)

如果我在这时创建一个 commit，那么 HEAD 会带着 master 一起移动到最新的 commit：
git commit
![](http://otlk76aak.bkt.clouddn.com/images/15fd779f983c81e7.gif)

通过查看 log，可以对这个逻辑进行验证：
git log
![](http://otlk76aak.bkt.clouddn.com/images/2__%23$!@%25!%23__pastedGraphic.png)

从图中可以看出，最新的 commit （提交信息："Add feature1"）被创建后，HEAD 和 master 这两个引用都指向了它，而在上面第一张图中的后两个引用 origin/master 和 origin/HEAD 则依然停留在原先的位置。
### master:默认branch
上面的这个 master ，其实是一个特殊的 branch：它是 Git 的默认 branch（俗称主 branch / 主分支）。
所谓的「默认 branch」，主要有两个特点：
	1	新创建的 repository（仓库）是没有任何 commit 的。但在它创建第一个 commit 时，会把 master 指向它，并把 HEAD 指向 master。
![](http://otlk76aak.bkt.clouddn.com/images/15fd779f5c66ac9e.gif)
	2	

	3	当有人使用 git clone 时，除了从远程仓库把 .git 这个仓库目录下载到工作目录中，还会 checkout （签出） master（checkout 的意思就是把某个 commit 作为当前 commit，把 HEAD 移动过去，并把工作目录的文件内容替换成这个 commit 所对应的内容）。
![](http://otlk76aak.bkt.clouddn.com/images/15fd779f5c191a3f.gif)
	4	

另外，需要说一下的是，大多数的开发团队会规定开发以 master 为核心，所有的分支都在一定程度上围绕着 master 来开发。这个在事实上构成了 master 和其它分支在地位上的一个额外的区别。
### branch的通俗化理解

尽管在 Git 中，branch 只是一个指向 commit 的引用，但它有一个更通俗的理解：你还可以把一个 branch 理解为从初始 commit 到 branch 所指向的 commit 之间的所有 commits 的一个「串」。例如下面这张图：
![](http://otlk76aak.bkt.clouddn.com/images/3__%23$!@%25!%23__pastedGraphic.png)

master 的本质是一个指向 3 的引用，但你也可以把 master 理解为是 1 2 3 三个 commit 的「串」，它的起点是 1，终点是 3。
这种理解方式比较符合 branch 这个名字的本意（branch 的本意是树枝，可以延伸为事物的分支），也是大多数人对 branch 的理解。不过如果你选择这样理解 branch，需要注意下面两点：
	1	所有的 branch 之间都是平等的。
![](http://otlk76aak.bkt.clouddn.com/images/4__%23$!@%25!%23__pastedGraphic.png)
	2	

例如上面这张图，branch1 是 1 2 5 6 的串，而不要理解为 2 5 6 或者 5 6 。其实，起点在哪里并不是最重要的，重要的是你要知道，所有 branch 之间是平等的，master 除了上面我说的那几点之外，并不比其他 branch 高级。这个认知的理解对于 branch 的正确使用非常重要。
换个角度来说，上面这张图我可以用别的画法来表达，它们的意思是一样的：
![](http://otlk76aak.bkt.clouddn.com/images/15fd779ff346fbd7.gif)
	3	

通过这张动图应该能够对「平等」这个概念更好地理解了吧？
	4	branch 包含了从初始 commit 到它的所有路径，而不是一条路径。并且，这些路径之间也是彼此平等的。
![](http://otlk76aak.bkt.clouddn.com/images/15fe3354a1d3cd26.gif)
	5	

像上图这样，master 在合并了 branch1 之后，从初始 commit 到 master 有了两条路径。这时，master 的串就包含了 1 2 3 4 7 和 1 2 5 6 7 这两条路径。而且，这两条路径是平等的，1 2 3 4 7 这条路径并不会因为它是「原生路径」而拥有任何的特别之处。
如果你喜欢用「树枝」的概念来理解 Git 的 branch，一定要注意上面说的这两点，否则在今后使用 branch 的时候就可能与出现理解偏差或者使用方式不当的问题。事实上我本人并不喜欢用这种方式来理解 branch，因为觉得它有点舍近求远的味道：我为了「直观」地思考，给它了一个形象的比喻，但由于它的本质含义其实更加简单，导致我的这种比喻反而增加了思考它时的复杂度，未免有点画蛇添足。不过这是我自己的感受，怎么理解 branch 是个个人偏好的问题，这两种理解方式你选一个喜欢的就好。
### branch### 的创建、切换和删除

**创建****branch**
如果你想在某处创建 branch ，只需要输入一行 git branch 名称。例如你现在在 master 上：
![](http://otlk76aak.bkt.clouddn.com/images/5__%23$!@%25!%23__pastedGraphic.png)

你想在这个 commit 处创建一个叫做 "feature1" 的 branch，只要输入：
git branch feature1
你的 branch 就创建好了：
![](http://otlk76aak.bkt.clouddn.com/images/6__%23$!@%25!%23__pastedGraphic.png)

**切换****branch**
不过新建的 branch 并不会自动切换，你的 HEAD 在这时依然是指向 master 的。你需要用 checkout 来主动切换到你的新 branch 去：
git checkout feature1
然后 HEAD 就会指向新建的 branch 了：
![](http://otlk76aak.bkt.clouddn.com/images/7__%23$!@%25!%23__pastedGraphic.png)

除此之外，你还可以用 git checkout -b 名称 来把上面两步操作合并执行。这行代码可以帮你用指定的名称创建 branch 后，再直接切换过去。还以 feature1 为例的话，就是：
git checkout -b feature1
在切换到新的 branch 后，再次 commit 时 HEAD 就会带着新的 branch 移动了：
...
git commit
![](http://otlk76aak.bkt.clouddn.com/images/15fe3354a2a32692.gif)

而这个时候，如果你再切换到 master 去 commit，就会真正地出现分叉了：
git checkout master
...
git commit
![](http://otlk76aak.bkt.clouddn.com/images/15fe3354ab0861a7.gif)

**删除****branch**
删除 branch 的方法非常简单：git branch -d 名称。例如要删除 feature1 这个 branch：
git branch -d feature1
![](http://otlk76aak.bkt.clouddn.com/images/16006b7e3d35fe54.gif)

需要说明的有两点：
	1	HEAD 指向的 branch 不能删除。如果要删除 HEAD 指向的 branch，需要先用 checkout 把 HEAD 指向其他地方。
	2	由于 Git 中的 branch 只是一个引用，所以删除 branch 的操作也只会删掉这个引用，并不会删除任何的 commit。（不过如果一个 commit 不在任何一个 branch 的「路径」上，或者换句话说，如果没有任何一个 branch 可以回溯到这条 commit（也许可以称为野生 commit？），那么在一定时间后，它会被 Git 的回收机制删除掉。）
	3	出于安全考虑，没有被合并到 master 过的 branch 在删除时会失败（因为怕你误删掉「未完成」的 branch 啊）：
![](http://otlk76aak.bkt.clouddn.com/images/8__%23$!@%25!%23__pastedGraphic.png)
	4	

这种情况如果你确认是要删除这个 branch （例如某个未完成的功能被团队确认永久毙掉了，不再做了），可以把 -d 改成 -D，小写换成大写，就能删除了。
![](http://otlk76aak.bkt.clouddn.com/images/9__%23$!@%25!%23__pastedGraphic.png)
	5	

### 「引用」的本质

所谓「引用」（reference），其实就是一个个的字符串。这个字符串可以是一个 commit 的 SHA-1 码（例：c08de9a4d8771144cd23986f9f76c4ed729e69b0），也可以是一个 branch（例：ref: refs/heads/feature3）。
Git 中的 HEAD 和每一个 branch 以及其他的引用，都是以文本文件的形式存储在本地仓库 .git 目录中，而 Git 在工作的时候，就是通过这些文本文件的内容来判断这些所谓的「引用」是指向谁的。
### 小结

这一节介绍了 Git 中的一些「引用」：HEAD、master、branch。这里总结一下：
	1	HEAD 是指向当前 commit 的引用，它具有唯一性，每个仓库中只有一个 HEAD。在每次提交时它都会自动向前移动到最新的 commit 。
	2	branch 是一类引用。HEAD 除了直接指向 commit，也可以通过指向某个 branch 来间接指向 commit。当 HEAD 指向一个 branch 时，commit 发生时，HEAD 会带着它所指向的 branch 一起移动。
	3	master 是 Git 中的默认 branch，它和其它 branch 的区别在于：
	1	新建的仓库中的第一个 commit 会被 master 自动指向；
	2	在 git clone 时，会自动 checkout 出 master。
	4	branch 的创建、切换和删除：
	1	创建 branch 的方式是 git branch 名称 或 git checkout -b 名称（创建后自动切换）；
	2	切换的方式是 git checkout 名称；
	3	删除的方式是 git branch -d 名称。













# 进阶 2: push的本质

在之前的内容里，我粗略地说过，push 指令做的事是把你的本地提交上传到中央仓库去，用本地的内容来覆盖掉远端的内容。这个说法其实是不够准确的，但 Git 的知识系统比较庞大，在你对 Git 了解比较少的时候，用「上传本地提交」来解释会比较好理解；而在你知道了 branch，并且明白了 branch 的具体含义以后，我就可以告诉你 push 到底是什么了。
### push:把### branch### 上传到远端仓库

实质上，push 做的事是：把当前 branch 的位置（即它指向哪个 commit）上传到远端仓库，并把它的路径上的 commits 一并上传。
例如，我现在的本地仓库有一个 master ，它超前了远程仓库两个提交；另外还有一个新建的 branch 叫 feature1，远程仓库还没有记载过它。具体大概像这样：
![](http://otlk76aak.bkt.clouddn.com/images/10__%23$!@%25!%23__pastedGraphic.png)

这时我执行 git push，就会把 master 的最新位置更新到远端，并且把它的路径上的 5 6 两个 commits 上传：
git push
![](http://otlk76aak.bkt.clouddn.com/images/1600725e9973f71d.gif)

而如果这时候我再切到 feature1 去后再执行一次 push，就会把 feature1 以及它的 commit 4 上传到远程仓库：
git checkout feature1
git push origin feature1
这里的 git push 和之前有点不同：多了 origin feature1 这两个参数。其中 origin 是远程仓库的别名，是你在 git clone 的时候 Git 自动帮你起的；feature1 是远程仓库中目标 branch 的名字。这两个参数合起来指定了你要 push 到的目标仓库和目标分支，意思是「我要 push 到 origin 这个仓库的 feature1 分支」。
在 Git 中（2.0 及它之后的版本），默认情况下，你用不加参数的 git push 只能上传那些之前从远端 clone 下来或者 pull 下来的分支，而如果需要 push 你本地的自己创建的分支，则需要手动指定目标仓库和目标分支（并且目标分支的名称必须和本地分支完全相同），就像上面这样。
你可以通过 git config 指令来设置 push.default 的值来改变 push 的行为逻辑，例如可以设置为「所有分支都可以用 git push 来直接 push，目标自动指向 origin 仓库的同名分支」（对应的 push.default 值：current），或者别的什么行为逻辑，你甚至可以设置为每次执行 git push 时就自动把所有本地分支全部同步到远程仓库（虽然这可能有点耗时和危险）。如果希望详细了解，你可以到 [这里](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgit-config%23git-config-pushdefault) 看看。
![](http://otlk76aak.bkt.clouddn.com/images/160073ccda56ef07.gif)

细心的人可能会发现，在 feature1 被 push 时，远程仓库的 HEAD 并没有和本地仓库的 HEAD 一样指向 feature1。这是因为，push 的时候只会上传当前的 branch 的指向，并不会把本地的 HEAD 的指向也一起上传到远程仓库。事实上，远程仓库的 HEAD 是永远指向它的默认分支（即 master，如果不修改它的名称的话），并会随着默认分支的移动而移动的。
### 小结

这一节介绍了 push 这个指令的本质。总结一下关键点：
	1	push 是把当前的分支上传到远程仓库，并把这个 branch 的路径上的所有 commits 也一并上传。
	2	push 的时候，如果当前分支是一个本地创建的分支，需要指定远程仓库名和分支名，用 git push origin branch_name 的格式，而不能只用 git push；或者可以通过 git config 修改 push.default 来改变 push 时的行为逻辑。
	3	push 的时候之后上传当前分支，并不会上传 HEAD；远程仓库的 HEAD 是永远指向默认分支（即 master）的。




























# 进阶 3: merge# ：合并# commits
前面说到，pull 的内部操作其实是把远程仓库取到本地后（使用的是 fetch），再用一次 merge 来把远端仓库的新 commits 合并到本地。这一节就说一下，merge 到底是什么。
### 含义和用法

merge 的意思是「合并」，它做的事也是合并：指定一个 commit，把它合并到当前的 commit 来。具体来讲，merge 做的事是：
**从目标****commit****和当前****commit****（即****HEAD****所指向的****commit）分叉的位置起，把目标****commit****的路径上的所有****commit****的内容一并应用到当前****commit，然后自动生成一个新的****commit。**
例如下面这个图中：
![](http://otlk76aak.bkt.clouddn.com/images/11__%23$!@%25!%23__pastedGraphic%201.png)

HEAD 指向了 master，所以如果这时执行：
git merge branch1
Git 会把 5 和 6 这两个 commit 的内容一并应用到 4 上，然后生成一个新的提交，并跳转到提交信息填写的界面：
![](http://otlk76aak.bkt.clouddn.com/images/12__%23$!@%25!%23__pastedGraphic%201.png)

merge 操作会帮你自动地填写简要的提交信息。在提交信息修改完成后（或者你打算不修改默认的提交信息），就可以退出这个界面，然后这次 merge 就算完成了。
![](http://otlk76aak.bkt.clouddn.com/images/15fddc2aad5a0279%201.gif)

### 适用场景

merge 有什么用？最常用的场景有两处：
	1	合并分支
当一个 branch 的开发已经完成，需要把内容合并回去时，用 merge 来进行合并。
那 branch 又应该怎么用呢？
下节就说。

	2	pull 的内部操作
之前说过，pull 的实际操作其实是把远端仓库的内容用 fetch 取下来之后，用 merge 来合并。
### 特殊情况### 1:冲突

merge 在做合并的时候，是有一定的自动合并能力的：如果一个分支改了 A 文件，另一个分支改了 B 文件，那么合并后就是既改 A 也改 B，这个动作会自动完成；如果两个分支都改了同一个文件，但一个改的是第 1 行，另一个改的是第 2 行，那么合并后就是第 1 行和第 2 行都改，也是自动完成。
![](http://otlk76aak.bkt.clouddn.com/images/15fddc2a9d759d8e%201.gif)

![](http://otlk76aak.bkt.clouddn.com/images/15fddc2a9c151a4c%201.gif)

但，如果两个分支修改了同一部分内容，merge 的自动算法就搞不定了。这种情况 Git 称之为：冲突（Conflict）。
![](http://otlk76aak.bkt.clouddn.com/images/13__%23$!@%25!%23__pastedGraphic%201.png)

直白点说就是，你的两个分支改了相同的内容，Git 不知道应该以哪个为准。如果在 merge 的时候发生了这种情况，Git 就会把问题交给你来决定。具体地，它会告诉你 merge 失败，以及失败的原因：
git merge feature1
![](http://otlk76aak.bkt.clouddn.com/images/14__%23$!@%25!%23__pastedGraphic%201.png)

提示信息说，在 shopping list.txt 中出现了 "merge conflict"，自动合并失败，要求 "fix conflicts and then commit the result"（把冲突解决掉后提交）。那么你现在需要做两件事：
	1	解决掉冲突
	2	手动 commit 一下
**1.****解决冲突**解决掉冲突的方式有多个，我现在说最直接的一个。你现在再打开 shopping list.txt 看一下，会发现它的内容变了：
![](http://otlk76aak.bkt.clouddn.com/images/15__%23$!@%25!%23__pastedGraphic%201.png)

可以看到，Git 虽然没有帮你完成自动 merge，但它对文件还是做了一些工作：它把两个分支冲突的内容放在了一起，并用符号标记出了它们的边界以及它们的出处。上面图中表示，HEAD 中的内容是 移动硬盘（已买），而 feature1 中的内容则是 移动硬盘（不买了）。这两个改动 Git 不知道应该怎样合并，于是把它们放在一起，由你来决定。假设你决定保留 HEAD 的修改，那么只要删除掉 feature1 的修改，再把 Git 添加的那三行 <<< === >>> 辅助文字也删掉，保存文件退出，所谓的「解决掉冲突」就完成了。
![](http://otlk76aak.bkt.clouddn.com/images/16__%23$!@%25!%23__pastedGraphic%201.png)

你也可以选择使用更方便的 merge 工具来解决冲突，这个你可以自己搜索一下。
**2.****手动提交**解决完冲突以后，就可以进行第二步—— commit 了。
git add shopping\ list.txt # 嗯是的，这里 commit 前也需要先 add 一下
git commit
![](http://otlk76aak.bkt.clouddn.com/images/17__%23$!@%25!%23__pastedGraphic%201.png)

可以看到，被冲突中断的 merge，在手动 commit 的时候依然会自动填写提交信息。这是因为在发生冲突后，Git 仓库处于一个「merge 冲突待解决」的中间状态，在这种状态下 commit，Git 就会自动地帮你添加「这是一个 merge commit」的提交信息。
**放弃解决冲突，取消****merge****？**同理，由于现在 Git 仓库处于冲突待解决的中间状态，所以如果你最终决定放弃这次 merge，也需要执行一次 merge --abort 来手动取消它：
git merge --abort
输入这行代码，你的 Git 仓库就会回到 merge 前的状态。
### 特殊情况### 2: HEAD### 领先于目标### commit
如果 merge 时的目标 commit 和 HEAD 处的 commit 并不存在分叉，而是 HEAD 领先于目标 commit：
![](http://otlk76aak.bkt.clouddn.com/images/18__%23$!@%25!%23__pastedGraphic%201.png)

那么 merge 就没必要再创建一个新的 commit 来进行合并操作，因为并没有什么需要合并的。在这种情况下， Git 什么也不会做，merge 是一个空操作。
### 特殊情况### 3: HEAD### 落后于
### 目标### commit——fast-forward
而另一种情况：如果 HEAD 和目标 commit 依然是不存在分叉，但 HEAD 不是领先于目标 commit，而是落后于目标 commit：
![](http://otlk76aak.bkt.clouddn.com/images/19__%23$!@%25!%23__pastedGraphic%201.png)

那么 Git 会直接把 HEAD（以及它所指向的 branch，如果有的话）移动到目标 commit：
git merge feature1
![](http://otlk76aak.bkt.clouddn.com/images/15fddc2b2486758a%201.gif)

这种操作有一个专有称谓，叫做 "fast-forward"（快速前移）。
一般情况下，创建新的 branch 都是会和原 branch （例如上图中的 master ）并行开发的，不然没必要开 branch ，直接在原 branch 上开发就好。但事实上，上图中的情形其实很常见，因为这其实是 pull 操作的一种经典情形：本地的 master 没有新提交，而远端仓库中有同事提交了新内容到 master：
![](http://otlk76aak.bkt.clouddn.com/images/20__%23$!@%25!%23__pastedGraphic%201.png)

那么这时如果在本地执行一次 pull 操作，就会由于 HEAD 落后于目标 commit （也就是远端的 master）而造成 "fast-forward"：
git pull
![](http://otlk76aak.bkt.clouddn.com/images/15fddc2b46c69d46%201.gif)

简单解释一下上图中的 origin/master 和 origin/HEAD 是什么鬼：它们是对远端仓库的 master 和 HEAD 的本地镜像，在 git pull 的「两步走」中的第一步——git fetch 下载远端仓库内容时，这两个镜像引用得到了更新，也就是上面这个动图中的第一步：origin/master 和 origin/HEAD 移动到了最新的 commit。
为什么前面的图里面从来都没有这两个「镜像引用」？因为我没有画呀！其实它们是一直存在的。
而 git pull 的第二步操作 merge 的目标 commit ，是远端仓库的 HEAD，也就是 origin/HEAD ，所以 git pull 的第二步的完整内容是：
git merge origin/HEAD
因此 HEAD 就会带着 master 一起，也指向图中绿色的最新 commit 了。
### 小结

本节对 merge 进行了介绍，内容大概有这么几点：
	1	merge 的含义：从两个 commit「分叉」的位置起，把目标 commit 的内容应用到当前 commit（HEAD 所指向的 commit），并生成一个新的 commit；
	2	merge 的适用场景：
	1	单独开发的 branch 用完了以后，合并回原先的 branch；
	2	git pull 的内部自动操作。
	3	merge 的三种特殊情况：
	1	冲突
	1	原因：当前分支和目标分支修改了同一部分内容，Git 无法确定应该怎样合并；
	2	应对方法：解决冲突后手动 commit。
	2	HEAD 领先于目标 commit：Git 什么也不做，空操作；
	3	HEAD 落后于目标 commit：fast-forward。















# 高级# 1# ：不喜欢# merge# 的分叉？用# rebase# 吧

有些人不喜欢 merge，因为在 merge 之后，commit 历史就会出现分叉，这种分叉再汇合的结构会让有些人觉得混乱而难以管理。如果你不希望 commit 历史出现分叉，可以用 rebase 来代替 merge。
### rebase——### 在新位置重新提交

rebase ，又是一个中国人看不懂的词。这个词的意思，你如果查一下的话：
![](http://otlk76aak.bkt.clouddn.com/images/21__%23$!@%25!%23__pastedGraphic%201.png)

哈？玩个 Git 就弯了？
其实这个翻译还是比较准确的。rebase 的意思是，给你的 commit 序列重新设置基础点（也就是父 commit）。展开来说就是，把你指定的 commit 以及它所在的 commit 串，以指定的目标 commit 为基础，依次重新提交一次。例如下面这个 merge：
git merge branch1
![](http://otlk76aak.bkt.clouddn.com/images/15fddc2aad5a0279%203.gif)

如果把 merge 换成 rebase，可以这样操作：
git checkout branch1
git rebase master
![](http://otlk76aak.bkt.clouddn.com/images/1600abd620a8e28c%201.gif)

可以看出，通过 rebase，5 和 6 两条 commits 把基础点从 2 换成了 4 。通过这样的方式，就让本来分叉了的提交历史重新回到了一条线。这种「重新设置基础点」的操作，就是 rebase 的含义。
另外，在 rebase 之后，记得切回 master 再 merge 一下，把 master 移到最新的 commit：
git checkout master
git merge branch1
![](http://otlk76aak.bkt.clouddn.com/images/160149e054fe485c%201.gif)

为什么要从 branch1 来 rebase，然后再切回 master 再 merge 一下这么麻烦，而不是直接在 master 上执行 rebase？
从图中可以看出，rebase 后的 commit 虽然内容和 rebase 之前相同，但它们已经是不同的 commits 了。如果直接从 master 执行 rebase 的话，就会是下面这样：
![](http://otlk76aak.bkt.clouddn.com/images/16014b5a6919c0b7%201.gif)

这就导致 master 上之前的两个最新 commit 被剔除了。如果这两个 commit 之前已经在中央仓库存在，这就会导致没法 push 了：
![](http://otlk76aak.bkt.clouddn.com/images/22__%23$!@%25!%23__pastedGraphic%201.png)

所以，为了避免和远端仓库发生冲突，一般不要从 master 向其他 branch 执行 rebase 操作。而如果是 master 以外的 branch 之间的 rebase（比如 branch1 和 branch2 之间），就不必这么多费一步，直接 rebase 就好。
### 小结

本节介绍的是 rebase 指令，它可以改变 commit 序列的基础点。它的使用方式很简单：
git rebase 目标基础点
需要说明的是，rebase 是站在需要被 rebase 的 commit 上进行操作，这点和 merge 是不同的。














# 高级# 2# ：刚刚提交的代码，发现写错了怎么办？

刚提交了一个代码，发现有几个字写错了：
![](http://otlk76aak.bkt.clouddn.com/images/23__%23$!@%25!%23__pastedGraphic.png)

怎么修复？
当场再写一个修复这几个错别字的 commit？可以是可以，不过还有一个更加优雅和简单的解决方法：commit -—amend。
"amend" 是「修正」的意思。在提交时，如果加上 --amend 参数，Git 不会在当前 commit 上增加 commit，而是会把当前 commit 里的内容和暂存区（stageing area）里的内容合并起来后创建一个新的 commit，**用这个新的****commit****把当前****commit****替换掉**。所以 commit --amend 做的事就是它的字面意思：对最新一条 commit 进行修正。
具体地，对于上面这个错误，你就可以把文件中的错别字修改好之后，输入：
git add 笑声.txt
git commit --amend
![](http://otlk76aak.bkt.clouddn.com/images/24__%23$!@%25!%23__pastedGraphic.png)

Git 会把你带到提交信息编辑界面。可以看到，提交信息默认是当前提交的提交信息。你可以修改或者保留它，然后保存退出。然后，你的最新 commit 就被更新了。
![](http://otlk76aak.bkt.clouddn.com/images/15fdf0187f2f4b2d.gif)

### 小结

这一节的内容只有一点：用 commit --amend 可以修复当前提交的错误。使用方式：
git commit --amend
需要注意的有一点：commit --amend 并不是直接修改原 commit 的内容，而是生成一条新的 commit。








# 高级# 3# ：写错的不是最新的提交，而是倒数第二个？

commit --amend 可以修复最新 commit 的错误，但如果是倒数第二个 commit 写错了，怎么办？
### rebase -i:交互式### rebase
如果不是最新的 commit 写错，就不能用 commit --amend 来修复了，而是要用 rebase。不过需要给 rebase 也加一个参数：-i。
rebase -i 是 rebase --interactive 的缩写形式，意为「交互式 rebase」。
所谓「交互式 rebase」，就是在 rebase 的操作执行之前，你可以指定要 rebase 的 commit 链中的每一个 commit 是否需要进一步修改。
那么你就可以利用这个特点，进行一次「原地 rebase」。
例如你是在写错了 commit 之后，又提交了一次才发现之前写错了：
git log
![](http://otlk76aak.bkt.clouddn.com/images/25__%23$!@%25!%23__pastedGraphic.png)

**开启交互式****rebase****过程**现在再用 commit --amend 已经晚了，但可以用 rebase -i：
git rebase -i HEAD^^
说明：在 Git 中，有两个「偏移符号」： ^ 和 ~。
^ 的用法：在 commit 的后面加一个或多个 ^ 号，可以把 commit 往回偏移，偏移的数量是 ^ 的数量。例如：master^ 表示 master 指向的 commit 之前的那个 commit； HEAD^^ 表示 HEAD 所指向的 commit 往前数两个 commit。
~ 的用法：在 commit 的后面加上 ~ 号和一个数，可以把 commit 往回偏移，偏移的数量是 ~ 号后面的数。例如：HEAD~5 表示 HEAD 指向的 commit往前数 5 个 commit。
上面这行代码表示，把当前 commit （ HEAD 所指向的 commit） rebase 到 HEAD 之前 2 个的 commit 上：
![](http://otlk76aak.bkt.clouddn.com/images/15fdf5fd00522381.gif)

如果没有 -i 参数的话，这种「原地 rebase」相当于空操作，会直接结束。而在加了 -i 后，就会跳到一个新的界面：
![](http://otlk76aak.bkt.clouddn.com/images/26__%23$!@%25!%23__pastedGraphic.png)

**编辑界面：选择****commit****和对应的操作**这个编辑界面的最顶部，列出了将要「被 rebase」的所有 commits，也就是倒数第二个 commit 「增加常见笑声集合」和最新的 commit「增加常见哭声集合」。需要注意，这个排列是正序的，旧的 commit 会排在上面，新的排在下面。
这两行指示了两个信息：
	1	需要处理哪些 commits；
	2	怎么处理它们。
你需要修改这两行的内容来指定你需要的操作。每个 commit 默认的操作都是 pick （从图中也可以看出），表示「直接应用这个 commit」。所以如果你现在直接退出编辑界面，那么结果仍然是空操作。
但你的目标是修改倒数第二个 commit，也就是上面的那个「增加常见笑声集合」，所以你需要把它的操作指令从 pick 改成 edit 。 edit 的意思是「应用这个 commit，然后停下来等待继续修正」。其他的操作指令，在这个界面里都已经列举了出来（下面的 "Commands…" 部分文字），你可以自己研究一下。
![](http://otlk76aak.bkt.clouddn.com/images/27__%23$!@%25!%23__pastedGraphic.png)

把 pick 修改成 edit 后，就可以退出编辑界面了：
![](http://otlk76aak.bkt.clouddn.com/images/28__%23$!@%25!%23__pastedGraphic.png)

上图的提示信息说明，rebase 过程已经停在了第二个 commit 的位置，那么现在你就可以去修改你想修改的内容了。
**修改写错的****commit**
修改完成之后，和上节里的方法一样，用 commit --amend 来把修正应用到当前最新的 commit：
git add 笑声
git commit --amend
![](http://otlk76aak.bkt.clouddn.com/images/29__%23$!@%25!%23__pastedGraphic.png)

**继续****rebase****过程**在修复完成之后，就可以用 rebase --continue 来继续 rebase 过程，把后面的 commit 直接应用上去。
git rebase --continue
![](http://otlk76aak.bkt.clouddn.com/images/30__%23$!@%25!%23__pastedGraphic.png)

然后，这次交互式 rebase 的过程就完美结束了，你的那个倒数第二个写错的 commit 就也被修正了：
![](http://otlk76aak.bkt.clouddn.com/images/15fdf5fd4e7d5257.gif)

实质上，交互式 rebase 并不是必须应用在「原地 rebase」上来修改写错的 commit ，这只不过是它最常见的用法。你同样也可以把它用在分叉的 commit 上，不过这个你就可以自己去研究一下了。
### 小结

这节介绍了交互式 rebase，它可以在 rebase 开始之前指定一些额外操作。交互式 rebase 最常用的场景是修改写错的 commit，但也可以用作其他用途。它的大致用法：
	1	使用方式是 git rebase -i 目标commit；
	2	在编辑界面中指定需要操作的 commits 以及操作类型；
	3	操作完成之后用 git rebase --continue 来继续 rebase 过程。




# 高级# 4# ：比错还错，想直接丢弃刚写的提交？

有的时候，刚写完的 commit 写得实在太烂，连自己的都看不下去，与其修改它还不如丢掉重写。这种情况，就可以用 reset 来丢弃最新的提交。
### reset --hard### 丢弃最新的提交

比如你刚写了一个 commit：
![](http://otlk76aak.bkt.clouddn.com/images/15fe19c8a6101d7f.gif)

![](http://otlk76aak.bkt.clouddn.com/images/31__%23$!@%25!%23__pastedGraphic.png)

写完回头看了看，你觉得「不行这得重新写」。那么你可以用 reset --hard 来撤销这条 commit。
git reset --hard HEAD^
HEAD 表示 HEAD^ 往回数一个位置的 commit ，上节刚说过，记得吧？
HEAD^ 表示你要恢复到哪个 commit。因为你要撤销最新的一个 commit，所以你需要恢复到它的父 commit ，也就是 HEAD^。那么在这行之后，你的最新一条就被撤销了：
![](http://otlk76aak.bkt.clouddn.com/images/15fe19c8a3235853.gif)

不过，就像图上显示的，你被撤销的那条提交并没有消失，只是你不再用到它了。如果你在撤销它之前记下了它的 SHA-1 码，那么你还可以通过 SHA-1 来找到他它。
### 小结

这一节的内容是撤销最新的提交，方式是通过 reset --hard：
git reset --hard 目标commit









# 高级# 5# ：想丢弃的也不是最新的提交？

假如有一个 commit ，你在刚把它写完的时候并没有觉得它不好，可是在之后又写了几个提交以后，你突然灵光一现：「哎呀，那个 commit 不该写，我要撤销！」
不是最新的提交，就不能用 reset --hard 来撤销了。这种情况的撤销，就要用之前介绍过的一个指令：交互式 rebase ——rebase -i。
### 用交互式### rebase### 撤销提交

之前介绍过，交互式 rebase 可以用来修改某些旧的 commits。其实除了修改提交，它还可以用于撤销提交。比如下面这种情况：
![](http://otlk76aak.bkt.clouddn.com/images/32__%23$!@%25!%23__pastedGraphic.png)

![](http://otlk76aak.bkt.clouddn.com/images/33__%23$!@%25!%23__pastedGraphic.png)

你想撤销倒数第二条 commit，那么可以使用 rebase -i：
git rebase -i HEAD^^
![](http://otlk76aak.bkt.clouddn.com/images/34__%23$!@%25!%23__pastedGraphic.png)

然后就会跳到 commit 序列的编辑界面，这个在之前的第 10 节里已经讲过了。和第 10 节一样，你需要修改这个序列来进行操作。不过不同的是，之前讲的修正 commit 的方法是把要修改的 commit 左边的 pick 改成 edit，而如果你要撤销某个 commit ，做法就更加简单粗暴一点：直接删掉这一行就好。
![](http://otlk76aak.bkt.clouddn.com/images/35__%23$!@%25!%23__pastedGraphic.png)

pick 的直接意思是「选取」，在这个界面的意思就是应用这个 commit。而如果把这一行删掉，那就相当于在 rebase 的过程中跳过了这个 commit，从而也就把这个 commit 撤销掉了。
![](http://otlk76aak.bkt.clouddn.com/images/15fe243fce5804fd.gif)

现在再看 log，就会发现之前的倒数第二条 commit 已经不在了。
git log
![](http://otlk76aak.bkt.clouddn.com/images/36__%23$!@%25!%23__pastedGraphic.png)

### 用### rebase --onto### 撤销提交

除了用交互式 rebase ，你还可以用 rebase --onto 来更简便地撤销提交。
rebase 加上 --onto 选项之后，可以指定 rebase 的「起点」。一般的 rebase，告诉 Git 的是「我要把当前 commit 以及它之前的 commits 重新提交到目标 commit 上去，这其中，rebase 的「起点」是自动判定的：选取当前 commit 和目标 commit 在历史上的交叉点作为起点。
例如下面这种情况：
![](http://otlk76aak.bkt.clouddn.com/images/37__%23$!@%25!%23__pastedGraphic.png)

如果在这里执行：
git rebase 第3个commit
那么 Git 会自动选取 3 和 5 的历史交叉点 2 作为 rebase 的起点，依次将 4 和 5 重新提交到 3 的路径上去。
而 --onto 参数，就可以额外给 rebase 指定它的起点。例如同样以上图为例，如果我只想把 5 提交到 3 上，不想附带上 4，那么我可以执行：
git rebase --onto 第3个commit 第4个commit branch1
--onto 参数后面有三个附加参数：目标 commit、起点 commit（注意：rebase 的时候会把起点排除在外）、终点 commit。所以上面这行指令就会从 4 往下数，拿到 branch1 所指向的 5，然后把 5 重新提交到 3 上去。
![](http://otlk76aak.bkt.clouddn.com/images/15fe24400d7d73d0.gif)

同样的，你也可以用 rebase --onto 来撤销提交：
git rebase --onto HEAD^^ HEAD^ branch1
上面这行代码的意思是：以倒数第二个 commit 为起点（起点不包含在 rebase 序列里哟），branch1 为终点，rebase 到倒数第三个 commit 上。
也就是这样：
![](http://otlk76aak.bkt.clouddn.com/images/15fe243fce5804fd%201.gif)

### 小结

这节的内容是「撤销过往的提交」。方法有两种：
	1	用 git rebase -i 在编辑界面中删除想撤销的 commits
	2	用 git rebase --onto 在 rebase 命令中直接剔除想撤销的 commits
方法有两种，理念是一样的：在 rebase 的过程中去掉想撤销的 commit，让他它消失在历史中。







# 高级# 6# ：代码已经# push# 上去了才发现写错？

有的时候，代码 push 到了中央仓库，才发现有个 commit 写错了。这种问题的处理分两种情况：
### 1.### 出错的内容在你自己的### branch
假如是某个你自己独立开发的 branch 出错了，不会影响到其他人，那没关系用前面几节讲的方法把写错的 commit 修改或者删除掉，然后再 push 上去就好了。不过……
![](http://otlk76aak.bkt.clouddn.com/images/38__%23$!@%25!%23__pastedGraphic.png)

由于你在本地对已有的 commit 做了修改，这时你再 push 就会失败，因为中央仓库包含本地没有的 commits。但这个和前面讲过的情况不同，这次的冲突不是因为同事 push 了新的提交，而是因为你刻意修改了一些内容，这个冲突是你预料到的，你本来就希望用本地的内容覆盖掉中央仓库的内容。那么这时就不要乖乖听话，按照提示去先 pull 一下再 push 了，而是要选择「强行」push：
git push origin branch1 -f
-f 是 --force 的缩写，意为「忽略冲突，强制 push」。
![](http://otlk76aak.bkt.clouddn.com/images/39__%23$!@%25!%23__pastedGraphic.png)

这样，在本地修改了错误的 commits，然后强制 push 上去，问题就解决了。
### 2.### 出错的内容已经合并到### master
这就不能用上面那招了。同事的工作都在 master 上，你永远不知道你的一次强制 push 会不会洗掉同事刚发上去的新提交。所以除非你是人员数量和行为都完全可控的超小团队，可以和同事做到无死角的完美沟通，不然一定别在 master 上强制 push。
在这种时候，你只能退一步，选用另一种策略：增加一个新的提交，把之前提交的内容抹掉。例如之前你增加了一行代码，你希望撤销它，那么你就做一个删掉这行代码的提交；如果你删掉了一行代码，你希望撤销它，那么你就做一个把这行代码还原回来的提交。这种事做起来也不算麻烦，因为 Git 有一个对应的指令：revert。
它的用法很简单，你希望撤销哪个 commit，就把它填在后面：
git revert HEAD^
上面这行代码就会增加一条新的 commit，它的内容和倒数第二个 commit 是相反的，从而和倒数第二个 commit 相互抵消，达到撤销的效果。
在 revert 完成之后，把新的 commit 再 push 上去，这个 commit 的内容就被撤销了。它和前面所介绍的撤销方式相比，最主要的区别是，这次改动只是被「反转」了，并没有在历史中消失掉，你的历史中会存在两条 commit ：一个原始 commit ，一个对它的反转 commit。
### 小结

这节的内容是讲当错误的 commit 已经被 push 上去时的解决方案。具体的方案有两类：
	1	如果出错内容在私有 branch：在本地把内容修正后，强制 push (push -f）一次就可以解决；
	2	如果出错内容在 master：不要强制 push，而要用 revert 把写错的 commit 撤销。









# 高级# 7: reset# 的本质# ——# 不止可以撤销提交

前面讲到，在最新的 commit 写错时，可以用 reset --hard 来把 commit 撤销：
git reset --hard HEAD^
用这行代码可以撤销掉当前 commit
在这节里，就对 reset 多说点，说说它的本质，说说它在撤销提交之外的用途。
### reset### 的本质：移动### HEAD### 以及它所指向的### branch
实质上，reset 这个指令虽然可以用来撤销 commit ，但它的实质行为并不是撤销，而是移动 HEAD ，并且「捎带」上 HEAD 所指向的 branch（如果有的话）。也就是说，reset 这个指令的行为其实和它的字面意思 "reset"（重置）十分相符：它是用来重置 HEAD 以及它所指向的 branch 的位置的。
而 reset --hard HEAD^ 之所以起到了撤销 commit 的效果，是因为它把 HEAD 和它所指向的 branch 一起移动到了当前 commit 的父 commit 上，从而起到了「撤销」的效果：
![](http://otlk76aak.bkt.clouddn.com/images/15fe19c8a3235853%201.gif)

Git 的历史只能往回看，不能向未来看，所以把 HEAD 和 branch 往回移动，就能起到撤回 commit 的效果。
所以同理，reset --hard 不仅可以撤销提交，还可以用来把 HEAD 和 branch 移动到其他的任何地方。
git reset --hard branch2
![](http://otlk76aak.bkt.clouddn.com/images/15fe333cb605b0de.gif)

不过……reset 后面总是跟着的那个 --hard 是什么意思呢？
reset 指令可以重置 HEAD 和 branch 的位置，不过在重置它们的同时，对工作目录可以选择不同的操作，而对工作目录的操作的不同，就是通过 reset 后面跟的参数来确定的。
### reset --hard:重置工作目录

reset --hard 会在重置 HEAD 和 branch 的同时，重置工作目录里的内容。当你在 reset 后面加了 --hard 参数时，你的工作目录里的内容会被完全重置为和 HEAD 的新位置相同的内容。换句话说，就是你的未提交的修改会被全部擦掉。
例如你在上次 commit 之后又对文件做了一些改动：
git status
![](http://otlk76aak.bkt.clouddn.com/images/40__%23$!@%25!%23__pastedGraphic.png)

然后，你执行了 reset 并附上了 --hard 参数：
git reset --hard HEAD^
你的 HEAD 和当前 branch 切到上一条 commit 的同时，你工作目录里的新改动也一起全都消失了，不管它们是否被放进暂存区：
git status
![](http://otlk76aak.bkt.clouddn.com/images/41__%23$!@%25!%23__pastedGraphic.png)

可以看到，在 reset --hard 后，所有的改动都被擦掉了。
### reset --soft:保留工作目录

reset --soft 会在重置 HEAD 和 branch 时，保留工作目录和暂存区中的内容，并把重置 HEAD 所带来的新的差异放进暂存区。
什么是「重置 HEAD 所带来的新的差异」？就是这里：
![](http://otlk76aak.bkt.clouddn.com/images/15fe333cb5c6a249.gif)

由于 HEAD 从 4 移动到了 3，而且在 reset 的过程中工作目录的内容没有被清理掉，所以 4 中的改动在 reset 后就也成了工作目录新增的「工作目录和 HEAD 的差异」。这就是上面一段中所说的「重置 HEAD 所带来的差异」。
所以在同样的情况下：
git status
![](http://otlk76aak.bkt.clouddn.com/images/40__%23$!@%25!%23__pastedGraphic%201.png)

假设此时当前 commit 的改动内容是新增了 laughters.txt 文件：
git show --stat
![](http://otlk76aak.bkt.clouddn.com/images/42__%23$!@%25!%23__pastedGraphic.png)

如果这时你执行：
git reset --soft HEAD^
那么除了 HEAD 和它所指向的 branch1 被移动到 HEAD^ 之外，原先 HEAD 处 commit 的改动（也就是那个 laughters.txt 文件）也会被放进暂存区：
git status
![](http://otlk76aak.bkt.clouddn.com/images/43__%23$!@%25!%23__pastedGraphic.png)

这就是 --soft 和 --hard 的区别：--hard 会清空工作目录的改动，而 --soft 则会保留工作目录的内容，并把因为保留工作目录内容所带来的新的文件差异放进暂存区。
### reset### 不加参数：保留工作目录，并清空暂存区

reset 如果不加参数，那么默认使用 --mixed 参数。它的行为是：保留工作目录，并且清空暂存区。也就是说，工作目录的修改、暂存区的内容以及由 reset 所导致的新的文件差异，都会被放进工作目录。简而言之，就是「把所有差异都混合（mixed）放在工作目录中」。
还以同样的情况为例：
git status
![](http://otlk76aak.bkt.clouddn.com/images/40__%23$!@%25!%23__pastedGraphic%202.png)

修改了 games.txt 和 shopping list.txt，并把 games.txt 放进了暂存区。
git show --stat
![](http://otlk76aak.bkt.clouddn.com/images/42__%23$!@%25!%23__pastedGraphic%201.png)

最新的 commit 中新增了 laughters.txt 文件。
这时如果你执行无参数的 reset：
git reset HEAD^
工作目录的内容和 --soft 一样会被保留，但和 --soft 的区别在于，它会把暂存区清空：
git status
![](http://otlk76aak.bkt.clouddn.com/images/44__%23$!@%25!%23__pastedGraphic.png)

### 小结

本节内容讲了 reset 指令的本质：重置 HEAD 以及它所指向的 branch 的位置。同时，介绍了 reset 的三种参数：
	1	--hard：重置位置的同时，清空工作目录的所有改动；
	2	--soft：重置位置的同时，保留工作目录和暂存区的内容，并把重置 HEAD 的位置所导致的新的文件差异放进暂存区。
	3	--mixed（默认）：重置位置的同时，保留工作目录的内容，并清空暂存区。
除了上面这三种参数，还有一些没有列出的较为不常用的参数；另外除了我讲的功能外，reset 其实也还有一些别的功能和用法。不过 reset 最关键的功能、用法和本质原理就是上面这些了，想了解更多的话，可以去官网了解一下。





















# 高级# 8: checkout# 的本质

在前面的 branch 的部分，我说到 checkout 可以用来切换 branch：
git checkout branch2
![](http://otlk76aak.bkt.clouddn.com/images/160089d53b4f65a5.gif)
checkout
不过实质上，checkout 并不止可以切换 branch。checkout 本质上的功能其实是：签出（ checkout ）指定的 commit。
git checkout branch名 的本质，其实是把 HEAD 指向指定的 branch，然后签出这个 branch 所对应的 commit 的工作目录。所以同样的，checkout 的目标也可以不是 branch，而直接指定某个 commit：
git checkout HEAD^^
git checkout master~5
git checkout 78a4bc
git checkout 78a4bc^
这些都是可以的。
另外，如果你留心的话可能会发现，在 git status 的提示语中，Git 会告诉你可以用 checkout -- 文件名 的格式，通过「签出」的方式来撤销指定文件的修改：
![](http://otlk76aak.bkt.clouddn.com/images/45__%23$!@%25!%23__pastedGraphic.png)

### 小结

这节的内容是对 checkout 的本质进行简述：
checkout 的本质是签出指定的 commit，所以你不止可以切换 branch，也可以直接指定 commit 作为参数，来把 HEAD 移动到指定的 commit。
### checkout### 和### reset### 的不同

checkout 和 reset 都可以切换 HEAD 的位置，它们除了有许多细节的差异外，最大的区别在于：reset 在移动 HEAD 时会带着它所指向的 branch 一起移动，而 checkout 不会。当你用 checkout 指向其他地方的时候，HEAD 和 它所指向的 branch 就自动脱离了。
事实上，checkout 有一个专门用来只让 HEAD 和 branch 脱离而不移动 HEAD 的用法：
git checkout --detach
执行这行代码，Git 就会把 HEAD 和 branch 脱离，直接指向当前 commit：
![](http://otlk76aak.bkt.clouddn.com/images/1600acce7b90b009.gif)
git checkout --detach





# 高级# 9# ：紧急情况：「立即给我打个包，现在马上！」

前面在讲 branch 的时候讲到，利用 branch 可以实现一人多任务的需求，从而可以轻松应对「嘿，这个先别做了，给你个新活」的情况。但有时，尤其是在互联网公司，你可能会遇到比这更紧急的情况：你正对着电脑发呆，忽然见到一个同事屁股着着火就跑来找你了：「快快快，立即给我打个包，现在马上，拜托拜托！」
这种情况和「这个 branch 先放放吧」不同，你没时间、也没必要当场慌慌张张把文件的所有改动做个临时的 commit 然后去救同事的火，救完火再重新把 commit 撤销回来。这时候你只要先把所有文件一股脑扔在一边就可以去给同事打包了，打完包再把刚才扔到一边的文件重新取过来就好。
这一「扔」一「取」，用的是 Git 的 stash 指令。
### stash:临时存放工作目录的改动

"stash" 这个词，和它意思比较接近的中文翻译是「藏匿」，是「把东西放在一个秘密的地方以备未来使用」的意思。在 Git 中，stash 指令可以帮你把工作目录的内容全部放在你本地的一个独立的地方，它不会被提交，也不会被删除，你把东西放起来之后就可以去做你的临时工作了，做完以后再来取走，就可以继续之前手头的事了。
具体说来，stash 的用法很简单。当你手头有一件临时工作要做，需要把工作目录暂时清理干净，那么你可以：
git stash
就这么简单，你的工作目录的改动就被清空了，所有改动都被存了起来。
然后你就可以从你当前的工作分支切到 master 去给你的同事打包了……
打完包，切回你的分支，然后：
git stash pop
你之前存储的东西就都回来了。很方便吧？
注意：没有被 track 的文件（即从来没有被 add 过的文件不会被 stash 起来，因为 Git 会忽略它们。如果想把这些文件也一起 stash，可以加上 `-u` 参数，它是 `--include-untracked` 的简写。就像这样：
git stash -u











# 高级# 10: branch# 删过了才想起来有用？

branch 用完就删是好习惯，但有的时候，不小心手残删了一个还有用的 branch ，或者把一个 branch 删掉了才想起来它还有用，怎么办？
### reflog:引用的### log
reflog 是 "reference log" 的缩写，使用它可以查看 Git 仓库中的引用的移动记录。如果不指定引用，它会显示 HEAD 的移动记录。假如你误删了 branch1 这个 branch，那么你可以查看一下 HEAD 的移动历史：
git reflog
![](http://otlk76aak.bkt.clouddn.com/images/46__%23$!@%25!%23__pastedGraphic.png)

从图中可以看出，HEAD 的最后一次移动行为是「从 branch1 移动到 master」。而在这之后，branch1 就被删除了。所以它之前的那个 commit 就是 branch1 被删除之前的位置了，也就是第二行的 c08de9a。
所以现在就可以切换回 c08de9a，然后重新创建 branch1 ：
git checkout c08de9a
git checkout -b branch1
这样，你刚删除的 branch1 就找回来了。
注意：不再被引用直接或间接指向的 commits 会在一定时间后被 Git 回收，所以使用 reflog 来找回删除的 branch 的操作一定要及时，不然有可能会由于 commit 被回收而再也找不回来。
### 查看其他引用的### reflog
reflog 默认查看 HEAD 的移动历史，除此之外，也可以手动加上名称来查看其他引用的移动历史，例如某个 branch：
git reflog master
![](http://otlk76aak.bkt.clouddn.com/images/47__%23$!@%25!%23__pastedGraphic.png)






# 额外说点: .gitignore——排除不想被管理的文件和目录


在 Git 中有一个特殊的文本文件：.gitignore。这个文本文件记录了所有你希望被 Git 忽略的目录和文件。

如果你是在 GitHub 上创建仓库，你可以在创建仓库的界面中就通过选项来让 GitHub 帮你创建好一个符合项目类型的 .gitignore 文件，你就不用再自己麻烦去写一大堆的配置了。不过如果你不是在 GitHub 上创建的项目，或者你对 GitHub 帮你创建的 .gitignore 文件有一些额外的补充，那么你可以自己来编辑这个文件。

.gitignore大概长这样： 
这是 Android 项目的 .gitignore 文件，每种项目的 .gitignore 文件的内容都不一样。

文件中 # 打头的是注释文件，其他的都是对忽略文件的配置。匹配规则从上图中就能看出个大致，具体的匹配规则可以去官网的相关条目查看：
 [https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore) 
















# 几个「不难但却很有用」的# Git# 技能点

### 总结

Git 内容非常多，这本小册我已经尽量克制，可是还是写了二十多节出来。尽管这样，有些很有用的内容我依然没有写出来。因为我写这本小册的目的是解决大部分人「学不会 Git」和「用了很久却总用不好 Git」这两个问题，所以我在这本小册里重点讲的也是 Git 的学习和使用中那些既重要又困难的关键点。
如果你在整个阅读过程中是边读边练的，相信读到这里，你对 Git 已经有一个较为全面和深刻的认识了。接下来你只要在平时使用 Git 的过程中多留心一些，找机会把这本小册中的内容应用在实战，很快就可以成为众人眼中的「Git 高手」了。当然，到时候你也许也会发现，其实大家眼中的「Git 高手」远没有那么神秘，并不一定比别人懂很多，只是更加了解 Git 的工作原理和一些关键概念罢了。
除了这本小册里讲到的那些「关键点」，还有些 Git 的相关知识虽然也比较有用，但属于稍微研究一下就可以学会的内容，我就不讲了，只在这里做一个简单的列举，你在平时使用 Git 的时候记得抽空学习一下就好。
### tag:不可移动的branch
tag 是一个和 branch 非常相似的概念，它和 branch 最大的区别是：tag 不能移动。所以在很多团队中，tag 被用来在关键版本处打标记用。
更多关于 tag： [git-scm.com/docs/git-ta…](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgit-tag) 
### cherry-pick:把选中的### commits### 一个个合并进来

cherry-pick 是一种特殊的合并操作，使用它可以点选一批 commits，按序合并。
更多关于 cherry-pick： [git-scm.com/docs/git-ch…](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgit-cherry-pick) 
### git config: Git的设置

git config 可以对 Git 做出基础设置，例如用户名、用户邮箱，以及界面的展示形式。内容虽然多，但都不难，整体看一遍，把 Git 设置成你最舒服的样子，从此就再也不用管它了。属于「一次付出，终身受用」的高性价比内容。
更多关于 config： [git-scm.com/docs/git-co…](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgit-config) 
### Git Flow:复杂又高效的工作流

除了前面讲到的 "Feature Branching"，还有一个也很流行的工作流：Git Flow。Git Flow 的机制非常完善，很适合大型团队的代码管理。不过由于它的概念比较复杂（虽然难度并不高），所以并不适合新手直接学习，而更适合在不断的自我研究中逐渐熟悉，或者在团队合作中慢慢掌握。基于这个原因，我最终也没有在这本小册里讲 Git Flow，但我推荐你自己在有空的时候了解一下它。
更多关于 Git Flow： [www.atlassian.com/git/tutoria…](https://link.juejin.im/?target=https%3A%2F%2Fwww.atlassian.com%2Fgit%2Ftutorials%2Fcomparing-workflows%2Fgitflow-workflow) 
以上这几个内容都不难，而且挺有用，所以虽然我没有讲，但都建议你把它们了解一下，会有好处的。
### 想学习更多的Git知识？

如果看完这本小册觉得不够，希望得到更多的 Git 知识补充，可以到它的官网去查看详细的文档：
 [git-scm.com/](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2F) 

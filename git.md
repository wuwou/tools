版本控制有什么用？

> 可以用它来查看编写的旧代码，找出原因。在解决问题时发送代码补丁很有用。

目录称为“tree”它将名称映射到blob或树（因此目录可以包含其他目录），文件称为“ blob”，它只是一堆字节array<byte>。所以是一个递归结构，树可以包含其他树，或者文件，但是文件不能包含树.

```
type blob = array
type tree = map
type commit = struct {
    parent: array
    author: string
    message: string
    snapshot: tree
}
```

```cpp
type object = blob | tree | commit

objects = map

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

我们要知道这里的sha1哈希，意思是将大数据转换为short string。

上面的commit函数，`parent:array<commit>`里其实是一些parent commit id。包括snapshot也不是实际的tree，而是快照的id。

sha1会生成40位16进制的id，总共160byte。

```cpp
references = map

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

关于在服务器中建立git

```git
mkdir demo
cd demo
git init
ls -a
git help init
git status //这里可以说明发生了什么事
echo "hello world" > hello.txt //这时候只是发生了更改，但并没有commit
git add hello.txt //这时候才有显示
git commit//首次运行需要绑定email和user name，输入就行了，然后会让你输入一段commit description。写完后:z就能退出来，能看到最后的命令行显示结果为：[master (root-commit) 5fb923f] Add hello.txt

git log commit //帮助你可视化历史提交记录
上面有sha1值5fb923f，可以用git cat-file -p 5fb923f会打印该提交的内容
```

```cpp
(base) wuy@inspur-NF5568M4:~/demo$ git cat-file -p 5fb923f

tree 68aba62e560c0ebc3396e8ae9335232cd93a3f60

author wuwou <123@qq.com> 1596535532 +0800

committer wuwou <123@qq.com> 1596535532 +0800


Add hello.txt

```

```git
使用git cat-file -p来打印sha1 id包含的内容
```

git add 不能提交全部，如果想要提交全部，得用命令`git commit -a`

```git
git log可以看到历史版本，这是一个非常有用的命令
git log --all --graph --decorate   //可视化历史版本信息

```

```git
echo "another line" >> hello.txt

cat hello.txt

```

`git add`和`git commit`的区别：

> `git add`和`git commit`的区别就在于：
>  `git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
>  `git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。
>  因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支。所以，git commit就是往master分支上提交更改。
>  你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。
> 
> 所以要想将修改提交到master中一定要先`git add`到暂存区中，再`git commit`到master分支。

```git
git checkout f5b69: 这样可以将工作目录的状态更改为它的状态，这时候查看git log graph，可以看到head和master分离了。
git checkout master：返回master
如果在hello.txt中修改了文件，还没有add，就进行git checkout，会显示错误，因为有修改，可以使用checkout -f来强制切换。
git diff命令可以显示自上次快照以来发生的变化，有助于了解项目的进展情况。
也可以在git diff中假如额外参数限制类型，比如从id为5fb92开始一直到最后的diff情况。
git diff 5fb92 hello.txt

(base) wuy@inspur-NF5568M4:~/demo$ git diff 5fb92 hello.txt

diff --git a/hello.txt b/hello.txt

index 3b18e51..23c818e 100644

--- a/hello.txt

+++ b/hello.txt

@@ -1 +1,3 @@

 hello world

+another line

+asdf


git branch:会列出在本地存储库中的分支。
git branch -vv:还会打印额外的信息
git branch cat:新建分支，叫cat，可以使用git log --all --graph --decorate来查看
如果在branch cat上更改，并提交，会发现只有Head->cat在新的commit节点上，master滞留在原地
git log --all --graph --decorate --oneline    //得到的视图更加紧凑

(base) wuy@inspur-NF5568M4:~/demo$ git log --all --graph --decorate --oneline

* bcace6d (HEAD -> cat) Add cat funuction

* 555d2da (master) Add animal.py

* 8d583b4 Modify hello.txt

* 5fb923f Add hello.txt


但是这时候git checkout master，切回master分支，再使用git log打印，会发现变化是一样的，但是head指向了master。


创建一个branch并切换到新建的分支：git checkout -b dog
这时候对dog进行修改并commit。

最后想要让master进行合并操作，使用git merge cat；git merge dog

git remote基本上是与其他人合作的方式，会列出所有git知道当前存储库的远程。
```

```git
建立远程仓库：mkdir remote; cd remote; git init --bare

让本地知道远程仓库，使用git remote add      
比如我在demo文件夹下，使用git remote add origin ../remote   这就使本地remote充当此存储库的git远程分支并给这个远程分支取名为origin


将本地分支推送到远程分支，可以用如下命令：
git push  :
意思是远程分支名字
本地将要推送分支的名字
新建一个分支并update该分支
比如：git push origin master:master   这就是想将本地master分支推送到远程计算机origin上用master名称创建一个分支。


这时候再git log，可以看到不同颜色的，显示远程存储库的分支，这时候在本地分支上进行更改并commit，不会影响到远程分支。


git clone是可以从某人的某个副本开始的命令，存放在某个地方并制作自己的本地副本
git clone格式是：git clone    里面的folder name是文件夹名称，以将其克隆到哪里
比如：git clone ./remote demo2   将./remote中的内容全部克隆到demo2中。

git branch --set-upstream-to=/：在本地和远程分支之间建立对应关系,方便在后面进行git push的时候不输入后续参数。
比如：git branch --set-upstream-to=origin/master
这样在本地进行push的时候可以直接git push

git fetch：从远程检索对象/引用,如果有多个远程分支，要在后面加上名字。fetch不会更改本地仓库的代码，只是拉取了远程commit数据，将远程的commit id更新为最新，如果想与本地分支进行合并，要用merge，
git merge origin/master

如果发现remote上有内容更改，也可以使用git pull来拉取，会直接合并到本地分支上。

git remote可以查看远程分支

vim ~/.gitconfg来配置文件夹中的文件，比如username， useremail。

如果一个文件很大，有很多次提交记录，你可以使用轻量级的clone来克隆：git clone --shallow   这不会在本地计算机上拥有版本历史记录

git add -p animal.py   会交互式的设置要提交的文件(比如当有一些交互式的调试语句不想提交的时候)
按s，这时候会将一些改变一个个输出，来确保是否要更改，用y或者n


git diff --cached  会告诉我要进行提交的更改，即在暂存区的更改。

git blame可以看到具体的谁更改了哪一行，比如我可以使用 git blame animal.py看见我改了哪些行。
git show 提交号 比如 git show a88b4eac  会获取该特定提交的信息。

git stash会隐藏更改。使用git stash pop会撤销隐藏

gitignore文件可以指定忽略文件

```

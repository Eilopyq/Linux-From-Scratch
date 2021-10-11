# Linux From Scratch 11.0-systemed

LFS──Linux from Scratch，就是一种从网上直接下载源码，从头编译Linux的安装方式。它不是发行版，只是一个菜谱，告诉你到哪里去买菜（下载源码），怎么把这些生东西( raw code) 作成符合自己口味的菜肴──个性化的Linux，不单单是个性的桌面。

[官方中文文档](https://bf.mengyan1223.wang/lfs/zh_CN/11.0-systemd/LFS-SYSD-BOOK.html)     [官方英文文档](https://www.linuxfromscratch.org/lfs/view/stable/index.html)      [FAQ](https://www.linuxfromscratch.org/faq/)      [镜像](https://www.linuxfromscratch.org/mirrors.html)     [Linux操作教程](https://www.runoob.com/linux/linux-tutorial.html)     [LFS视频教程(English-version)](https://www.youtube.com/watch?v=9TYr1mCzMcg&list=PLyc5xVO2uDsAlIkKBIGauDQ6LejoQovyL)    [LFS视频教程(Ubuntu-as-Host-Machine)](https://www.youtube.com/watch?v=5tRJgDJC7kY)

## 第Ⅰ部分 前言

### 1.1 Linux

Linux is a **family** of **open-source Unix-like systems based on the Linux kernel**, an operating system kernel first released on September 17,1991,by Linus Torvalds.

Some Linux distributions: Unbuntu, Cent OS, Debian, Kali......

### 1.2 基础命令

1. ls                          列出该目录下文件
2. cd                          切换目录
3. pwd                         显示当前目录位置
4. mkdir  *directory_name*       新建文件夹
5. rmkdir *directory_name*       删除文件夹
6. rm -r  *directory-name*       删除文件夹
7. touch  *file_name*            新建文件
8. rm     *file_name*            删除文件
9. cp *file_name* *file_name1*     在本目录下构造该文件的副本，并命名为*file_name1* 
10. cp *file_name* *directory_name* 将文件复制至另外一个目录 
11. cat                         查看文件内容
12. mv *file_name* *directory_name* 移动文件至另外一个目录 
13. mv *file_name* *file_name1*     重命名文件
14. ln *file_name* *link_name*      新建硬链接，即直接指向数据块，可通过ls -l发现两者一致
15. ln -s *file_name* *link_name*   新建软链接，即直接指向文件路径
16. gedit *file_name*             编辑文件，适用于GUI界面
17. vim   *file_name*             编辑文件，适用于Terminal，相较于vi，vim拥有更多的优势，[vim编辑命令](https://www.runoob.com/linux/linux-vim.html)
18. [Linux压缩命令](https://blog.csdn.net/weixin_44901564/article/details/99682926?ops_request_misc=&request_id=&biz_id=102&utm_term=Linux%E6%89%93%E5%8C%85&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-99682926.pc_search_result_hbase_insert&spm=1018.2226.3001.4187)

### 1.3 学习LFS的意义

相较于安装传统的Linux发行版，学习以及构建属于自己的Linux，拥有以下优势：

1. 学习Linux系统内部是如何运行的，构建LFS系统的过程将展示Linux系统的工作原理，以及其他各组成部分的协作和依赖关系。
2. 允许用户更好的控制自己的系统，而不用依赖于其他人的Linux实现。
3. 允许用户创建独属于自己的非常紧凑的Linux系统，在安装传统的 Linux 发行版时，您往往不得不安装一大堆可能永远不会用到，甚至完全无法理解其必要性的程序。它们会浪费系统资源。
4. 自行定制的 Linux 系统在安全方面也具有优势。

## 第Ⅱ部分 准备工作

### 2.1 宿主系统需求

以下搭建均在虚拟机中进行，发行版Linux为Ubuntu 20.04.2.0 LTS。

在搭建前需检查宿主机是否拥有必须的软件，并且版本达到最低要求（[软件及版本查看](https://bf.mengyan1223.wang/lfs/zh_CN/11.0-systemd/LFS-SYSD-BOOK.html#ch-partitioning-hostreqs)），切换至root用户并运行如下脚本。

```shell
cat > version-check.sh << "EOF"
#!/bin/bash
# Simple script to list version numbers of critical development tools
export LC_ALL=C
bash --version | head -n1 | cut -d" " -f2-4
MYSH=$(readlink -f /bin/sh)
echo "/bin/sh -> $MYSH"
echo $MYSH | grep -q bash || echo "ERROR: /bin/sh does not point to bash"
unset MYSH

echo -n "Binutils: "; ld --version | head -n1 | cut -d" " -f3-
bison --version | head -n1

if [ -h /usr/bin/yacc ]; then
  echo "/usr/bin/yacc -> `readlink -f /usr/bin/yacc`";
elif [ -x /usr/bin/yacc ]; then
  echo yacc is `/usr/bin/yacc --version | head -n1`
else
  echo "yacc not found" 
fi

bzip2 --version 2>&1 < /dev/null | head -n1 | cut -d" " -f1,6-
echo -n "Coreutils: "; chown --version | head -n1 | cut -d")" -f2
diff --version | head -n1
find --version | head -n1
gawk --version | head -n1

if [ -h /usr/bin/awk ]; then
  echo "/usr/bin/awk -> `readlink -f /usr/bin/awk`";
elif [ -x /usr/bin/awk ]; then
  echo awk is `/usr/bin/awk --version | head -n1`
else 
  echo "awk not found" 
fi

gcc --version | head -n1
g++ --version | head -n1
ldd --version | head -n1 | cut -d" " -f2-  # glibc version
grep --version | head -n1
gzip --version | head -n1
cat /proc/version
m4 --version | head -n1
make --version | head -n1
patch --version | head -n1
echo Perl `perl -V:version`
python3 --version
sed --version | head -n1
tar --version | head -n1
makeinfo --version | head -n1  # texinfo version
xz --version | head -n1

echo 'int main(){}' > dummy.c && g++ -o dummy dummy.c
if [ -x dummy ]
  then echo "g++ compilation OK";
  else echo "g++ compilation failed"; fi
rm -f dummy.c dummy
EOF
```

并输入`bash version-check.sh`运行脚本进行检查，本文使用的Ubuntu还有较多软件未下载，通过`sudo apt-get install package_name`下载即可。

<img src="Linux From Scratch.assets/image-20211010201124791.png"/>

本宿主机检查软件版本所发现的问题如下：

- Ubuntu默认的/bin/sh指向dash，此处需要改成bash，通过`dpkg-reconfigure dash`并选择否即可。
- Binutils缺乏，并且版本须在2.25~2.37。
- Bison缺乏，并且版本须高于2.7；缺乏/usr/bin/yacc。
- Gawk缺乏，并且版本须高于4.0.1；同时/usr/bin/awak->/usr/bin/gawk（本宿主机指向mawk）。
- GCC/G++缺乏，并且版本须在6.2~11.2.0。
- M4缺乏，并且版本须高于1.4.10
- Make缺乏，并且版本须高于4.0
- Texinfo缺乏，并且版本须高于4.7

> 官方文档中说明链接指向其他软件（如dash或mawk等）可能不会引发问题，但最好还是修改为相应的符号链接。

### 2.2 创建新的分区

Ubuntu在默认配置下只拥有一个硬盘，此处我们添加一个新的硬盘来构建LFS系统，配置为20GB，SCSI，磁盘内容存储在单个文件中。

可通过Ubuntu自带的磁盘管理工具或通过命令`lsblk`查看本宿主机磁盘情况。

<img src="Linux From Scratch.assets/image-20211011200902437.png">

我们可以看到两块硬盘中sda被分成了3个分区，而sdb未被分区，因此我们选择sdb通过`sudo cfdisk /dev/sdb`命令进行操作，并选择GPT(GUID)分区表。

GUID分区表的优势：

- 支持2TB以上的大硬盘。
- 每个磁盘的分区个数几乎没有限制，唯一限制的是操作系统，比如Windows系统最多只允许划分128个分区。
- 分区大小几乎没有限制。
- 分区表自带备份。
- 每个分区可以有一个名称（不同于卷标）。

本文划分了3个分区，分别作为根分区（Root Partition），主分区（Main Partition），交换分区(Swap Partition)，具体容量分配如下。

<img src="Linux From Scratch.assets/image-20211011201016033.png">

### 2.3 在分区上建立文件系统

现在我们建立好了空白分区，可以在分区上建立文件系统。LFS 可以使用 Linux 内核能够识别的任何文件系统，最常见的是 ext3 和 ext4。文件系统的选型是一个复杂的问题，要综合考虑分区的大小，以及其中所存储文件的特征。

本文中，根分区（sdb1）使用ext2；主分区（sdb2）采用ext4；此外由于新建了一个新的交换分区，我们需要初始化它。具体命令如下。

```shell
sudo mkfs -v -t ext2 /dev/sdb1
sudo mkfs -v -t ext4 /dev/sdb2
sudo mkswap /dev/sdb3
```

### 2.4 设置$LFS环境变量

后续搭建中将经常使用环境变量LFS，因此我们需要将该变量定义且设置在所构建LFS使用的目录中。

```shell
sudo mkdir /mnt/lfs
export LFS=/mnt/lfs
echo $LFS
```

> 无论何时离开并重新进入了工作环境（例如切换至root或其他用户），需要执行`echo $LFS`确认环境变量的设置是否正确，若不正确则使用上述命令进行配置。

始终保持LFS变量正确，我们可以编辑`/root/.bash_profile`。

### 2.5 挂载新的分区

我们已经在分区上建立了文件系统，为了访问分区，我们需要把分区挂载到选定的挂载点上。

**有关磁盘挂载问题的引入**

我们大多数人用惯了Windows系统，对linux系统中磁盘的管理就先入为主，不太好理解挂载这一动作。在linux系统中添加一块新磁盘后，要进行分区、格式化（分配文件系统）、挂载。当执行`ll /dev/sd*`时，可以看到相关的磁盘信息。大多数人会觉得硬盘添加，且分区、格式化了，可以用了。其实不然，还没有挂载好的硬盘就像新修的房子没有门一样，挂载就是将磁盘和某个文件夹捆绑在一起，做成一道磁盘的大门。

```shell
sudo mount -v -t ext4 /dev/sdb2 $LFS
sudo mkdir $LFS/boot
sudo mount -v -t ext2 /dev/sdb1 $LFS/boot
sudo /sbin/swapon -v /dev/sdb3
```

此时我们通过`cd $LFS`进入搭建文件夹，可以看到已完成分区已挂载完成。

**Lost+Found文件夹**

- 这个目录是使用标准的ext2/ext3档案系统格式才会产生的一个目录，目的在于当档案系统发生错误时， 将一些遗失的片段放置到这个目录下。这个目录通常会在分割槽的最顶层存在， 例如你加装一颗硬盘于/disk中，那在这个系统下就会自动产生一个这样的目录/disk/lost+found。

- lost+found这个目录一般情况下是空的，当系统非法关机后,如果你丢失了一些文件，在这里能找回来用来存放fsck过程中部分修复的文件的。
- lost+found：几乎每个被格式化过的Linux分区都会有，意外后找回的文件一般在这里面。
- 这个目录是储存发生意外后丢失的文件的。只有root用户才能打开。

### 3.1 软件包和补丁

在搭建基本的Linux系统时需要下载必要的软件包，同时下载好的软件包和补丁需要保存在一个合适的位置，使得整个构建过程中都能容易地访问它们。另外，还需要一个工作目录，以便解压和编译软件包。我们可以将*$LFS/sources*既用于保存软件包和补丁，又作为工作目录。这样，我们需要的所有东西都在 LFS 分区中，因此在整个构建过程中都能够访问。

此外我们还需为该目录添加写入权限和 sticky 标志。“Sticky” 标志使得即使有多个用户对该目录有写入权限，也只有文件所有者能够删除其中的文件。

获取软件包以及补丁的方法：

- 在后续的两节中，单独下载这些文件。
- 从 https://www.linuxfromscratch.org/mirrors.html#files 中列出的某个镜像站下载包含所有所需文件的压缩包。
- 使用`wget`和下面描述的[wget-list](https://bf.mengyan1223.wang/lfs/zh_CN/11.0-systemd/wget-list)下载这些文件（将wget-list下载至$LFS/sources）。

```shell
sudo mkdir -v $LFS/sources
cd $LFS/sources
sudo chmod -v a+wt $LFS/sources
sudo wget --input-file=wget-list
```

在软件下载完毕后，我们通过[md5sums](https://bf.mengyan1223.wang/lfs/zh_CN/11.0-systemd/md5sums)用来检查所有软件包的正确性。将该文件复制到 *$LFS/sources*，运行以下命令即可得到检查结果（缺少的文件可在官方文档单个文件下载地址进行下载）：

```shell
pushd $LFS/sources
  md5sum -c md5sums
popd
```

在第6章中，会使用交叉编译器编译程序。为了将这个交叉编译器和其他程序分离，它会被安装在一个专门的目录。

```shell
sudo mkdir -pv $LFS/tools
cd $LFS
sudo ln -sv $LFS/tools /
```

## [Linux](http://www.runoob.com/linux/linux-command-manual.html)

#### 查看系统的磁盘分区以及磁盘空间
- fdisk -l (磁盘分区情况)
- df (KB)
- df -m (MB)
- df -h (KB以上为单位)
- df -H (以1000 Bytes为换算单位)

#### 查看文件和目录的磁盘使用空间
- du -h
- du -s (仅显示总和)
- du -ah DirName (文件和目录都显示)

#### 查看当前目录的绝对路径
- pwd (有连接文件时，直接显示连接文件的路径)
- pwd -P (有连接文件时，不使用连接路径，直接显示连接文件所指向的文件)

#### 创建、删除目录
- mkdir
- rmdir (只能删除空目录)
- rm -rf (略过不存在的文件，不显示任何信息)

#### 创建、删除文件
- touch
- rm filename [filename ...]

#### 重命名文件及文件夹移动到指定文件夹
- mv file1 file2
- mv file1 dir1 (将文件f1移动到目录d1下，文件名仍为f1)
- mv dir1 dir2 (若d2存在则将d1及其所有文件和子目录移动到d2;若d2不存在则将d1更改为d2)


#### 复制文件/文件夹到指定的文件/文件夹
- cp file1 file2
- cp -r dir1 dir2

#### 查看文件
- cat -n (显示行号)
- head -n # filename
- tail -n # filename

#### 查看目录下的文件
- ls
- ls -a (包括以"."开头的隐藏文件)
- ls -s (包括文件的大小)
- ls -l (详细信息,文件的读写权限列表等)
- ls -R (包括子目录)
- ls -t (按时间排序)
- ls -S (按文件大小排序)

#### 搜索文件
- whereis xx (快速、模糊查找)
- locate xx (最推荐、最快、最好)
- find / -name xx (准确定位、慢、消耗大)

#### 显示两个文件的差异
- diff -y filename filename

#### 文件链接
>硬链接(hard link)与软链接(symbolic link)，硬链接的意思是一个档案可以有多个名称，而软链接的方式则是产生一个特殊的档案，该档案的内容是指向另一个档案的位置。硬链接是存在同一个文件系统中，而软链接却可以跨越不同的文件系统。
不论是硬链接或软链接都不会将原本的档案复制一份，只会占用非常少量的磁碟空间。
- 软链接：
>1.软链接，以路径的形式存在。类似于Windows操作系统中的快捷方式
2.软链接可以 跨文件系统 ，硬链接不可以
3.软链接可以对一个不存在的文件名进行链接
4.软链接可以对目录进行链接
- 硬链接：
>1.硬链接，以文件副本的形式存在。但不占用实际空间。
2.不允许给目录创建硬链接
3.硬链接只有在同一个文件系统中才能创建

- ln sourcefile file (其中一个修改了另一个也会被修改，删除源文件时目的文件不会被删除
- ln -s sourcefile file (删除源文件，目的文件也会被删除)

#### 关于进程
- ps -ef | grep "进程关键字"
- ps -aux (所有进程的状态)
- kill -s 19 pid (暂停)
- kill -s 18 pid (恢复暂停)
- kill -s 9 pid (强制杀死)

#### grep

#### awk

##### netstat.txt
```
Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)
tcp4       0      0  bogon.55008            117.122.217.19.https   ESTABLISHED
tcp4       0      0  bogon.54946            114.118.16.159.https   ESTABLISHED
tcp4       0      0  bogon.54945            101.201.173.208.https  ESTABLISHED
tcp4       0      0  bogon.54943            119.39.146.129.https   ESTABLISHED
tcp4       0      0  bogon.54874            119.39.204.33.https    ESTABLISHED
tcp4       0      0  bogon.54873            ti-in-f138.1e100.https SYN_SENT
tcp4       0      0  bogon.54868            ti-in-f138.1e100.https SYN_SENT
tcp4      37      0  bogon.53911            111.206.37.70.https    CLOSE_WAIT
```
- awk '{printf "%-8s %-22s\n",$1,$5}' netstat.txt
```
bogon:Array hades$ awk '{printf "%-8s %-22s\n",$1,$5}' netstat.txt
Proto    Address
tcp4     117.122.217.19.https
tcp4     114.118.16.159.https
tcp4     101.201.173.208.https
tcp4     119.39.146.129.https
tcp4     119.39.204.33.https
tcp4     ti-in-f138.1e100.https
tcp4     ti-in-f138.1e100.https
tcp4     111.206.37.70.https
```
- awk '{printf "%-8s %-22s\n",$1,$5}' netstat.txt
- 
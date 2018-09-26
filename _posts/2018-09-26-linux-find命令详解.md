---
layout:     post
title:      linux-find命令详解
subtitle:   linux、find
date:       2018/9/26
author:     Mfan
header-img: img/post-bg-debug.png
catalog: true
tags:
    linux
---

### find命令---文件查找命令

Linux系统文件中常用属性为以下内容：名称，大小，权限，属主，属组，修改时间，访问时间等，
在庞大的系统中，需要在prompt中查询文件，需要借助其查找工具实现，以此可以查询相同或要求指定属性的文件，
这里详细学习一下find命令，以备日后翻阅学习。

参考链接：https://www.jianshu.com/p/6e17ab338478

**优点**

1. 实时查找
2. 准确查找，遍历整个目录下所有文件
3. 可以对查询到的文件进行指定动作，即查看、删除、移动等操作

**find命令的格式**

    find pathname -options [ -print -exec -ok ...]

* pathname: find命令要查找的目录路径，使用.代表当前目录，/表示根目录
* -print  : 表示find命令将匹配到的文件输出到标准输出中
* -exec   : 表示find命令对匹配的文件执行该参数给出的shell命令。相应命令的形式为 ‘cmd’ {} \;
* -ok     : 它的作用和-exec一样，只是需要用户交互，更安全

**find命令的选项**

1. 以文件名查找

    * -name pattern : 以文件名查找
    * -iname pattern: 不区分文件名大小写，只支持glob风格的查找方式：*,？,[],[^]
    * -regex pattern: 基于正则表达式查找文件，精确匹配文件名

2. 以文件从属关系查找

    * -user USERNAME : 以用户名查找
    * -group GROUPNAME : 以组名查找
    * -uid UID :以UID号查找
    * -gid GID :以GID查找
    * -nouser : 查找没有属主的文件
    * -nogroup : 查找没有属组的文件

3. 以文件类型查找

    * -type TYPE : 以文件类型查找

    ```
    f : 普通文件
    d : 目录文件
    b : 块设备文件
    c : 字符设备文件
    l : 连接文件
    s : 套接字文件
    p : 管道文件
    ```
    
4. 根据文件大小查找

    * -size [+|-]SIZE : 以文件大小查询,大小包含K，M，G的单位
    
    ```
    -size 5M ： 精确查找大小为5M的文件，大小上面浮动稍微有偏差
    -size -5M : 查询大小小于5M的文件
    -size +5M : 查询大小大于5M的文件
    ```

5. 根据时间查找

    * -atime [+|-]TIME : 以访问时间（天）查找
    * -mtime [+|-]TIME : 以数据修改时间（天）查找
    * -ctime [+|-]TIME : 以元数据修改时间（天）查找
    * -amin [+|-]TIME : 以访问时间（分钟）查找
    * -mmin [+|-]TIME : 以数据修改时间（分钟）查找
    * -cmin [+|-]TIME : 以元数据修改时间（分钟）查找
    * -newer FILE : 以FILE文件为条件，判断比它新的文件
    
6. 根据权限查找

    * -perm MODE : 精确权限查找
        find . -perm 644
    * -perm /MODE : 任何一类用户(u,g,o)中的任何一位(r,w,x)符合条件即满足，理解为或关系
    * -perm -MODE : 每一类用户(u,g,o)的权限中的每一位(r,w,x)同时符合条件即满足，理解为与关系

7. 组合条件测试

    * 与 : -a
    
    ```angular2html
    默认组合逻辑，可以加-a，也可以取消，例：find . -type f -a -user mariadb，两个条件同时满足
    ```
    
    * 或 : -o
    
    ```angular2html
    可以加-o参数，例：find . -type f -o -nouser ,两个条件只满足一个即可
    ```
    
    * 非 : -not 或者 !表示
    
    ```angular2html
    find . -not -type f,不是普通文件
    find . ! -type f ,同上
    ```

8. 处理动作

    * -print : 默认为打印，不需要添加，输出入屏幕
    * -ls : 以ls长文件的格式形式输出
    * -delete : 删除查找到的文件
    * -fls /PATH/TO/SOMEFILE :把查询到的文件以ls详细信息格式保存到SOMEFILE文件中
    * -ok COMMAND {} \; : 查找到的文件传递给COMMAND命令，提每步都给用户提示确认操作
    * -exec COMMAND {} \; : 查找到的文件传递给COMMAND命令，直接修改完成，不给用户确认
    >注意：find将查找到的文件路径一次性传递给后面的命令，但有很多的命令不能接受过长的参数，导致命令的执行失败，使用如下方式可避免此错误的发生：
    ```angular2html
       find /etc -type f | xargs -i COMMAND ： -i参数是由find的结果传给xargs命令后，由-i指定结果代替符
    ```
        
    
9. 其他选项

    * 指定查找目录层级
    
        -maxdepth NUM  指定最多搜索目录层级到NUM层
        -mindepth NUM  指定最少搜索目录层级到NUM层
    
    * 查找空文件
    
        find . -empty  查找当前目录下所有的空文件
        
    * 排除符号连接
    
        -follow
    
**find命令使用示例**

1. 找出/tmp目录下属主为非root,且文件名包含fstab字符串的文件

    find /tmp -not -user root -a -name *fstab*
    
2.  找出/etc目录下至少有一周没有访问过的文件

    find /etc -atime +7 -ls
    
3. 找出/etc目录下24小时内修改过的文件

    find /etc -mtime -1 -ls

4. 查找/etc目录下大于1M且类型为普通文件的所有文件

    find /etc/ -size +1M -type f -ls
    
5. 在logs目录下查找更改时间在5日以前的文件并删除他们

    find /logs -mtime +5 -exec rm {} \

6. 在当前目录中查找以.log结尾、更改时间在5日以上的文件，并删除，但在删除前给出提示

    find . -name "*.log" -mtime +5 -ok rm {} \
    
7. 先用find命令匹配所有文件名为”passwd*”的文件，然后执行grep看看这些文件中是否存在一个sam的字符串

    find / -name "passwd*" -exec grep "sam" {} \

8. 查找当前用户主目录下的所有文件

    find ~

9. 查找当前目录下644权限的文件

    find . -perm 644

10. 查找系统当中所有长度为0的普通文件，并列出它们的完整路径

    find / -type f -size 0 -exec ls -l {} \

11. 查找当前目录中所有属于root组的文件

    find . -group root -exec ls -l {} \

用xargs来配合find工作

> 在使用find命令的-exec选项处理匹配到的文件时，find命令将所有匹配到的文件一起传递给exec执行。但有些系统对传递给exec的命令长度是有限制的，
这样，在find命令运行几分钟之后，就会出现溢出错误。错误信息通常是”参数列太长”或”参数列溢出”。这就是xargs命令的用户所在，特别是与find命令一起使用
find命令把匹配到的文件传递给xargs命令，而xargs命令每次只获取一部分文件而不是全部，不像-exec选项那样。
这样它就可以先处理最先获取的那一部分文件，然后是下一批，并且如此继续下去。

12. 查找root下面的777权限的文件，如果找到，都改为644权限

    fine /root -perm 777 | xargs chmod 644

13. 查找比某个文件新或旧的文件

    find / -newer test.log
    
>在进行系统维护时，我们经常遇到一种情况，即发现磁盘在不停地发生写现象，这时候我们要定位这个正在写的文件，可以选择建立一个文件，比如test
然后用以下命令找出这个频繁写磁盘的文件(最好带上一些限制大小的参数来精确定位)

    ```angular2html
        touch new1
        touch new2
        find / -newer new1 -o -path "/proc" -prune
        找出比new1新的文件，由于/proc目录中的文件一直在变化，所以可以排除
    ```
    
    
14. 在当前目录下寻找pl后缀的文件，不在scripts下寻找

    find . -path './scripts' -prune -o -name '*.pl' -print


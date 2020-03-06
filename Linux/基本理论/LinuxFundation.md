### 进程和线程的区别

* 定义上的不同： **进程是操作系统资源分配的基本单位，线程是任务调度和执行的基本单位；**
* 内涵关系：          **操作系统中可以有多个进程在运行，一个进程中可以有多个线程在运行**
* 内存资源的分配： 系统为进程分配了内存资源，  进程内部的线程可以共享进程内部的资源，          **同时线程还有自己的栈和栈指针，程序计数器等寄存器。进程有自己独立的地址空间，而线程没有，线程必须依赖于进程而存在**  







##### netstat是一个监控tcp/ip信息的工具

* 查看某个端口被那个进程占用， 详细信息： 
* Netstate  -tunlp | grep XXX.  



##### 查看此时的某个进程瞬时所占用资源的信息

* ps aux | grep nginx 



##### 在一个文件中查找具体内容

* grep    -rn    "正则表达式"   filename







### AWK命令-分割文本

* 是一个强大的文本分析工具

* 将文本分段进行输出

  + 默认的分割符 ： 空格  and tab     ---->awk '{print $1,$4}' log.txt
  + 自定义文本分割符 ：以逗号为分割符 ------  awk -F, '{print $1}'   test.txt

* 进行相关变量的操作

  + 对文本分段的的变量的运算: 

    > awk -va=1 '{print $1,$1+a}' log.txt

* 对每一行的文本字段进行条件过滤后输出

  + 过滤第一列大于2并且第二列等于’Are’的行

    ```shell
    awk '$1>2 && $2=="Are" {print $1,$2,$3}' log.txt
    ```

    ![](https://img-blog.csdn.net/20180114142315510?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluOTcwNTA1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* 其他的具体的相关操作 [详细文章](https://blog.csdn.net/jin970505/article/details/79056457)
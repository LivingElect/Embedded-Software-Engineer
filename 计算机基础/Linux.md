# III. **Linux**
### 1. Inode节点
1) Linux操作系统引进了一个非常重要的概念inode，中文名为索引结点，引进索引接点是为了在物理内存上找到文件块，所以inode中包含文件的相关基本信息，比如文件位置、文件创建者、创建日期、文件大小等待，输入stat指令可以查看某个文件的inode信息；
2) 硬盘格式化的时候，操作系统自动将硬盘分成两个区域，一个是数据区，一个是inode区，存放inode所包含的信息，查看每个硬盘分区的inode总数和已经使用的数量，可以用df命令；
3) 在linux系统中，系统内部并不是采用文件名查找文件，而是使用inode编号来识别文件。查找文件分为三个过程：系统找到这个文件名对应的inode号码，通过inode号码获得inode信息，根据inode信息找到文件数据所在的block读取数据；
4) 除了文件名之外的所有文件信息，都存储在inode之中。
### 2. Linux软连接、硬链接，删除了软连接的源文件软连接可用？
1) 软链接可以看作是Windows中的快捷方式，可以让你快速链接到目标档案或目录。硬链接则透过文件系统的inode来产生新档名，而不是产生新档案。
2) 软链接（符号链接） ln -s   source  target 
硬链接 （实体链接）ln       source  target
3) 硬链接(hard link)：A是B的硬链接（A和B都是文件名），则A的目录项中的inode节点号与B的目录项中的inode节点号相同，即一个inode节点对应两个不同的文件名，两个文件名指向同一个文件，A和B对文件系统来说是完全平等的。如果删除了其中一个，对另外一个没有影响。每增加一个文件名，inode节点上的链接数增加一，每删除一个对应的文件名，inode节点上的链接数减一，直到为0，inode节点和对应的数据块被回收。注：文件和文件名是不同的东西，rm A删除的只是A这个文件名，而A对应的数据块（文件）只有在inode节点链接数减少为0的时候才会被系统回收。
4) 软链接(soft link)：A是B的软链接（A和B都是文件名），A的目录项中的inode节点号与B的目录项中的inode节点号不相同，A和B指向的是两个不同的inode，继而指向两块不同的数据块。但是A的数据块中存放的只是B的路径名（可以根据这个找到B的目录项）。A和B之间是“主从”关系，如果B被删除了，A仍然存在（因为两个是不同的文件），但指向的是一个无效的链接。
5) 硬链接
不能对目录创建硬链接；不能对不同的文件系统创建硬链接；不能对不存在的文件创建硬链接；
6) 软连接
可以对目录创建软连接；可以跨文件系统；可以对不存在的文件创建软连接；
7) 因为链接文件包含有原文件的路径信息，所以当原文件从一个目录下移到其他目录中，再访问链接文件，系统就找不到了，而硬链接就没有这个缺陷，你想怎么移就怎么移；还有它要系统分配额外的空间用于建立新的索引节点和保存原文件的路径。
### 3. Linux系统应用程序的内存空间是怎么分配的,用户空间多大，内核空间多大？
1) Linux内核将这4G字节的空间分为两部分。将最高的1G字节（从虚拟地址0xC0000000到0xFFFFFFFF），供内核使用，称为“内核空间”。而将较低的3G字节（从虚拟地址 0x00000000到0xBFFFFFFF），供各个进程使用，称为“用户空间“。因为每个进程可以通过系统调用进入内核，因此，Linux内核由系统 内的所有进程共享。于是，从具体进程的角度来看，每个进程可以拥有4G字节的虚拟空间。
### 4. Linux的共享内存如何实现
1) 管道只能在具有亲缘关系的进程间进行通信；通过文件共享，在处理效率上又差一些，而且访问文件描述符不如访问内存地址方便；
2) mmap内存共享映射，mmap本来是存储映射功能，它可以将一个文件映射到内存中，在程序里就可以直接使用内存地址对文件内容进行访问；Linux的mmap实现了一种可以在父子进程之间共享内存地址的方式；
3) XSI共享内存，XSI是X/Open组织对UNIX定义的一套接口标准（X/Open System Interface）。XSI共享内存在Linux底层的实现实际上跟mmap没有什么本质不同，只是在使用方法上有所区别。
4) POSIX共享内存，Linux提供的POSIX共享内存，实际上就是在/dev/shm下创建一个文件，并将其mmap之后映射其内存地址即可。
​

### 5. 文件处理grep,awk,sed这三个命令必知必会
1) grep
grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。常用来在结果中搜索特定的内容。
2) awk
awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件(或其他方式的输入流, 如重定向输入)逐行的读入（看作一个记录集）, 把每一行看作一条记录，以空格(或\t,或用户自己指定的分隔符)为默认分隔符将每行切片（类似字段），切开的部分再进行各种分析处理。
3) sed
sed更侧重对搜索文本的处理，如修改、删除、替换等等。sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。
### 6. 查询进程占用CPU的命令
1) top
top命令可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。
2) ps
ps命令就是最基本进程查看命令。使用该命令可以确定有哪些进程正在运行和运行的状态、进程是否结束、进程有没有僵尸、哪些进程占用了过多的资源等等.总之大部分信息都是可以通过执行该命令得到。ps是显示瞬间进程的状态，并不动态连续；如果想对进程进行实时监控应该用top命令。
### 7. 一个程序从开始运行到结束的完整过程
1) 预处理，主要处理源代码中的预处理指令，引入头文件，去除注释，处理所有的条件编译指令，宏替换，添加行号。经过预处理指令后生成一个.i文件；
2) 编译，编译过程所进行的是对预处理后的文件进行语法分析、词法分析、符号汇总，然后生成汇编代码。生成.s文件；
3) 汇编，将汇编文件转换成二进制文件，二进制文件就可以让机器来读取。生成.o文件；
4) 链接，由汇编程序生成的目标文件并不能立即就被执行，其中可能还有许多没有解决的问题。
### 8. 一般情况下在Linux/windows平台下栈空间的大小
windows是编译器决定栈的大小，记录在可执行文件中，默认是1M。linux是操作系统来决定的，在系统环境变量中设置， ulimit -s 字节数 命令查看修改，但是linux默认栈大小为10M;vs编译器设置：属性—>设置à链接à输出à栈分配à重新设置；
### 9. Linux重定向
**1 重定向符号**
>               输出重定向到一个文件或设备 覆盖原来的文件
>!              输出重定向到一个文件或设备 强制覆盖原来的文件
>>             输出重定向到一个文件或设备 追加原来的文件
<               输入重定向到一个程序
**2标准错误重定向符号**
2>             将一个标准错误输出重定向到一个文件或设备 覆盖原来的文件  b-shell
2>>           将一个标准错误输出重定向到一个文件或设备 追加到原来的文件
2>&1         将一个标准错误输出重定向到标准输出 注释:1 可能就是代表 标准输出
>&             将一个标准错误输出重定向到一个文件或设备 覆盖原来的文件  c-shell
|&              将一个标准错误 管道 输送 到另一个命令作为输入
**3命令重导向示例**
在bash 命令执行的过程中，主要有三种输出入的状况，分别是：
1. 标准输入；代码为 0 ；或称为 stdin ；使用的方式为 <
2. 标准输出：代码为 1 ；或称为 stdout；使用的方式为 1>
3. 错误输出：代码为 2 ；或称为 stderr；使用的方式为 2>
### 10. Linux常用命令
1) ls命令，不仅可以查看linux文件包含的文件，而且可以查看文件权限；
2) cd命令，切换当前目录到dirName
3) pwd命令，查看当前工作目录路径；
4) mkdir命令，创建文件夹
5) rm命令，删除一个目录中的一个或多个文件或目录
6) rmdir命令，从一个目录中删除一个或多个子目录项，
7) mv命令，移动文件或修改文件名
8) cp命令，将源文件复制至目标文件，或将多个源文件复制至目标目录
9) cat命令，显示文件内容；
10) touch命令，创建一个文件
11) vim命令，
12) which命令查看可执行文件的位置，whereis查看文件的位置，find实际搜寻硬盘查询文件名称；
13) chmod命令，用于改变linux系统文件或目录的访问权限，421，ewr
14) tar命令，用来压缩和解压文件。tar本身不具有压缩功能，只具有打包功能，有关压缩及解压是调用其它的功能来完成。
15) chown命令，将指定文件的拥有者改为指定的用户或组，用户可以是用户名或者用户ID;
16) ln命令；
17) grep命令，强大的文本搜索命令，grep全局正则表达式搜素；
18) ps命令，用来查看当前运行的进程状态，一次性查看，如果需要动态连续结果使用top;
19) top命令，显示当前系统正在执行的进程的相关信息，包括进程ID、内存占用率、CPU占用率等；
20) kill命令，发送指定的信号到相应进程。不指定型号将发送SIGTERM（15）终止指定进程。

[2.4 gdb 调试准备 | CppGuide社区](https://cppguide.cn/pages/323439/)
### 1. 被调试的程序需要带调试信息
```shell
gcc -g -o hello_server hello_server.c
```

### 2. 启动gdb调试的方法

```shell
# 直接调试
1. gdb filename
# 当程序启动了，但又需要调试时
2. gdb attach pid
# 当程序崩溃时，可利用core文件定位崩溃原因
4. gdb filename corename
```

---

[2.5 gdb常用命令详解——利用gdb调试redis | CppGuide社区](https://cppguide.cn/pages/68d9ed/)
### 1. gdb常用调试命令概览和说明


<table><thead><tr><th>命令名称</th><th>命令缩写</th><th>命令说明</th></tr></thead><tbody><tr><td>run</td><td>r</td><td>运行一个程序</td></tr><tr><td>continue</td><td>c</td><td>让暂停的程序继续运行</td></tr><tr><td>break</td><td>b</td><td>添加断点</td></tr><tr><td>tbreak</td><td>tb</td><td>添加临时断点</td></tr><tr><td>backtrace</td><td>bt</td><td>查看当前线程的调用堆栈</td></tr><tr><td>frame</td><td>f</td><td>切换到当前调用线程的指定堆栈</td></tr><tr><td>info</td><td>info</td><td>查看断点 / 线程等信息</td></tr><tr><td>enable</td><td>enable</td><td>启用某个断点</td></tr><tr><td>disable</td><td>disable</td><td>禁用某个断点</td></tr><tr><td>delete</td><td>del</td><td>删除断点</td></tr><tr><td>list</td><td>l</td><td>显示源码</td></tr><tr><td>print</td><td>p</td><td>打印或修改变量或寄存器值</td></tr><tr><td>ptype</td><td>ptype</td><td>查看变量类型</td></tr><tr><td>thread</td><td>thread</td><td>切换到指定线程</td></tr><tr><td>next</td><td>n</td><td>运行到下一行</td></tr><tr><td>step</td><td>s</td><td>如果有调用函数，进入调用的函数内部，相当于 step into</td></tr><tr><td>until</td><td>u</td><td>运行到指定行停下来</td></tr><tr><td>finish</td><td>fi</td><td>结束当前调用函数，到上一层函数调用处</td></tr><tr><td>return</td><td>return</td><td>结束当前调用函数并返回指定值，到上一层函数调用处</td></tr><tr><td>jump</td><td>j</td><td>将当前程序执行流跳转到指定行或地址</td></tr><tr><td>disassemble</td><td>dis</td><td>查看汇编代码</td></tr><tr><td>set args</td><td></td><td>设置程序启动命令行参数</td></tr><tr><td>show args</td><td></td><td>查看设置的命令行参数</td></tr><tr><td>watch</td><td>watch</td><td>监视某一个变量或内存地址的值是否发生变化</td></tr><tr><td>display</td><td>display</td><td>监视的变量或者内存地址，当程序中断后自动输出监控的变量或内存地址</td></tr><tr><td>dir</td><td>dir</td><td>重定向源码文件的位置</td></tr></tbody></table>

##### 1. run
```shell
(gdb) run

# ctrl + c 中断程序
```

##### 2. continue

```shell
(gdb) continue

# 遇到断点继续程序
```

##### 3. break

```shell
## 在函数名为functionname的入口处添加一个断点 
(gdb) break functionname                 
## 在当前文件行号为LineNo处添加一个断点
(gdb) break LineNo                            
## 在filename文件行号为LineNo处添加一个断点
(gdb) break filename:LineNo            

```

##### 4. tbreak 

```shell
(gdb) tbreak
#  添加临时断点
```

##### 5. backtrace & frame

```shell
(gdb) bt
# 到达断点处，查看当前所在线程的调用堆栈

(gdb) frame 堆栈编号（编号不用加#）

# 切换到其他堆栈处
```

##### 6. info break & disable & enable

```shell
(gdb) info b
# 查看所有断点情况

(gdb) disable 断点编号
# 禁用断点，不加编号禁用所有断点

(gdb) enable 断点编号
# 启用断点，同理
```

##### 7. list

```shell
(gdb) l
# **list**命令用于查看当前断点附近的代码
(gdb) l +
# 查看后十行
(gdb) l -
# 查看前十行
```

##### 8. print & ptype

```shell
# **print**命令可以在我们调试过程中方便地查看变量的值，也可以修改当前内存中的变量值

(gdb) p server.port=6400
$4 = 6400
(gdb) p server.port
$5 = 6400

(gdb) print /format variable
# 指定输出格式

o octal 八进制显示
x hex 十六进制显示
d decimal 十进制显示
u unsigned decimal 无符号十进制显示
t binary 二进制显示
f float 浮点值显示
a address 内存地址格式显示(与十六进制相似)
i instruction 指令格式显示
s string 字符串形式显示
z hex, zero padded on the left 十六进制左侧补0显示

(gdb) ptype 变量
# 输出变量类型
```

##### 9. info & thread

```shell
(gdb) info threads
# 查看所有线程情况，使用bt可查看当前调用堆栈

# **线程编号前面的星号**表示的是**gdb当前作用于哪个线程**

(gdb) thread 线程编号
# 切换线程
```

##### 10. next & step & until & finish & return & jump

```shell
(gdb) n
# 根据程序逻辑跳转到相应的位置，接下来可直接回车实现“单步步过”

(gdb) step
# 遇到函数调用，进入函数内部，“单步步入”

(gdb) return
# 退出步入的函数，即返回上一层

(gdb) finish
# 结束函数

(gdb) until 行号
# 让程序运行到指定行停下来

(gdb) jump <location>
```

##### 11. disassemble

```shell
(gdb) diassemble
# 输出当前函数的汇编指令
```

##### 12. set args & show args

```shell
(gdb) set args ../redis.conf 
(gdb) show args
Argument list to give program being debugged when it is started is "../redis.conf ".
(gdb) 

(gdb) set args "999 xx" "hu jj"
(gdb) show args
Argument list to give program being debugged when it is started is ""999 xx" "hu jj"".
(gdb) 
# 如果单个命令行参数之间含有空格，可以使用引号将参数包裹起来。

```

##### 13. watch

**watch**是一个强大的命令，它可以用来监视一个变量或者一段内存，当这个变量或者该内存处的值发送变化时，gdb就会中断下来。

```shell
int i;  
(gdb) watch i

char *p; 
(gdb) watch p 与 watch *p

char buf[128];
(gdb) watch buf

```

##### 14. dispaly

```shell
(gdb) display 变量名/内存地址/寄存器名

(gdb) delete display 
```

##### 15. dir

```shell
# 加一个源文件路径到当前路径的前面,指定多个路径，可以使用”:”
(gdb) dir SourcePath1:SourcePath2:SourcePath3

```

---

[2.6 使用gdb调试多线程程序 | CppGuide社区](https://cppguide.cn/pages/2ec5a2/)


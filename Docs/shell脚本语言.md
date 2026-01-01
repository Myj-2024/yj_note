# shell 脚本语言

## 1、shell 的概述

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/439cb8fff6f43452b5686673f5874164.jpeg#pic_center)
shell 是一种脚本语言
脚本：本质是一个文件，文件里面存放的是 特定格式的指令，系统可以使用脚本解析器 翻译或解析 指令 并执行（它不需要编译）
shell 既是应用程序 又是一种脚本语言（应用程序 解析 脚本语言）
shell 命令解析器：
系统提供 shell 命令解析器： sh ash bash
查看自己 linux 系统的默认解析：echo $SHELL
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/92516cdb7ef604bc5954fc8a64522c70.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c0a602acd0215cfbcb5ba0bf99745edf.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c626da74d42502f4cc7e937928c9160f.png)
shell 脚本是一种脚本语言，我们只需使用任意文本编辑器，按照语法编写相应程序，增加可执行权限，即可在安装 shell 命令解释器的环境下执行

## 2、脚本的调用形式

#### 打开终端时系统自动调用：/etc/profile 或 ~/.bashrc

/etc/profile
此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行，系统的公共环境变量在这里设置
开机自启动的程序，一般也在这里设置
~/.bashrc
用户自己的家目录中的.bashrc
登录时会自动调用，打开任意终端时也会自动调用
这个文件一般设置与个人用户有关的环境变量，如交叉[编译器](https://marketing.csdn.net/p/3127db09a98e0723b83b2914d9256174?pId=2782&utm_source=glcblog&spm=1001.2101.3001.7020)的路径等等
用户手动调用：用户实现的脚本
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/50b1e0f869a822043d9195bf1aeee4df.png)

## 3、shell 语法初识

### 3.1、定义以开头：#!/bin/bash

**#!用来声明脚本由什么 shell 解释，否则使用默认 shell**
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ea8396e8cc6cb0acaf259da1781ea987.png)

### 3.2、单个"#"号代表注释当前行

#### 第一步：编写脚本文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1791a8531784fe91d00613d74bf5c34c.png)

#### 第二步：加上可执行权限

**chmod +x xxxx.sh**
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/62e6826ba422317830e694d94edc9356.png)

#### 第三步：运行

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0d3fa2355a464637c32edc4fe1aff89f.png)

##### 三种执行方式 （./xxx.sh bash xxx.sh . xxx.sh）

三种执行方式的不同点（./xxx.sh bash xxx.sh . xxx.sh）

###### ./xxx.sh :先按照 文件中#!指定的解析器解析

如果#！指定指定的解析器不存在 才会使用系统默认的解析器

###### bash xxx.sh:指明先用 bash 解析器解析

如果 bash 不存在 才会使用默认解析器

###### . xxx.sh 直接使用默认解析器解析（不会执行第一行的#！指定的解析器）但是第一行还是要写的

三种执行情况：
打开终端就会有以后个解释器，我们称为当前解释器
我们指定解析器的时候（使用 ./xxx.sh 或 bash xxx.sh）时会创建一个子 shell 解析 脚本
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c8cbabcbefa31e11756ea2eb071da906.png)

#### 注意：windows 下 写脚本 在 linux 下执行 注意

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a4aa6c77983c52477b22c59ada3121f8.png)
执行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/09e1aefc72305608c30a79fbefe81227.png)
将 windows 文件 转换成 unix 文件
方法一：dos2unix 如果没有该插件 需要安装
sudo apt-get install dos2unix
dos2unix shell 脚本
转换成功就可以执行运行
方法二：
需要用 vi 打开脚本，在最后一行模式下执行
:set ff=unix
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/18d13d6dc00f490e2ea8061eac934a74.png)

## 4、变量

定义变量
变量名=变量值
如：num=10
引用变量
$变量名
unset ：清除变量值
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c117ff68be810e3b641df6fed0d1949c.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b243548d1d5a22c92bcc0c20cba4e8cc.png)
从键盘获取值 read

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bce20c913a88f6bcb083fa148870ad9c.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9107f1a575f5ca16d3b471318e304159.png)

### 案例：

在一行上显示和添加提示 需要加上-p
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0edf0b9903e0c52e9ad474584ef09a8c.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aa6ca06fc47f1a8794d1ac90321d3ba1.png)

### 案例：读取多个值

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/feb4272a6bb0d81d2d57d9e63978602c.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/dc922fcac1ba01467ac61eb12b3df544.png)

### 案例只读变量：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e18d674a9d5c6f99e6d9bf085e5a8518.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/998ce5ab05d0366bfb76148283acac31.png)

### 查看环境变量：env

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a67da76df237c2ca22cb77ac1bc68bc8.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/02593f5a09da06ed2e1d8e9d820734a8.png)

#### 导出环境变量 作用：（让其他 shell 脚本识别该变量，设为全局变量）

source 脚本文件
source 命令用法:
source FileName
作用:在当前 bash 环境下读取并执行 FileName 中的命令。
注:该命令通常用命令“.”来替代。
如:source .bash_rc 与 . .bash_rc 是等效的。
注意:source 命令与 shell scripts 的区别是，
source 在当前 bash 环境下执行命令，而 scripts 是启动一个子 shell 来执行命令。这样如果把设置环境变量(或 alias 等等)的命令写进 scripts 中，就只会影响子 shell,无法改变当前的 BASH,所以通过文件(命令列)设置环境变量时，要用 source 命令。

06_sh.sh

```c
#!/bin/bash
expor DATA=250
12
```

用 source 是文件生效
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/19227afd8dabf7bd5c3d4c0bc5aecede.png)
使用 env 可以查看到环境变量中已经有 DATA
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e45837be7b46b09c9ea331614a10ac9c.png)
可以在终端直接中读取：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/13aa1a3e172ee060134ec158f542c856.png)
在其他 sh 脚本读取：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a62611f238348d827de079eafad8a73a.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d00184cbdfdf365ccf4763d067fe51a8.png)

### 注意事项：

1、变量名只能包含英文字母下划线，不能以数字开头
1_num=10 错误
num_1=20 正确
2、等号两边不能直接接空格符，若变量中本身就包含了空格，则整个字符串都要用双引号、或单引号括起来
3、双引号 单引号的区别
双引号：可以解析变量的值
单引号：不能解析变量的值
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d9c588c26aac3ea537611fd2c35612db.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/47db4c245338b97d9ad14f35159bc822.png)
如果想在 PATH 变量中 追加一个路径写法如下：（重要！！！！）

```cpp
export PATH=$PATH:/需要添加的路径
1
```

## 5、预设变量

### shell 直接提供无需定义的变量

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/abb6e35d2077066a5bb56c68e71e6286.png)

### 案例：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2f5e474a840379656a12fc5021d41654.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d39e6ebd80b7c08c2544012913a2a181.png)

### 脚本标量的特殊用法

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/555677be158c84e8faf8b5d4ccd42ee3.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/317cb656da02ea6e0cbf00d69a4fb119.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c18c0639e5523227793cbf5c940422e1.png)
加-e 转义 才起换行作用
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e16fe6e0a7d6f0bf5f8713228ea65511.png)
()由子 shell 完成
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6ecaffc07591301479665446d5ffb818.png)
{}由当前的 shell 执行

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4204adf3f72d8e41e679f6f5bb235b78.png)

## 6、变量的扩展

### 6.1、判断变量是否存在

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d291e43bd9b15adfb4da937fd0125eec.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a6290732b6dcaf83cc24081f8b75a2a1.png)

### 6.2、字符串的操作

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ae0d349fe339b17d0d1f715745e075d6.png)

## 7、条件测试

test 命令：用于测试字符串、文件状态和数字
test 命令有两种格式:
test condition 或[ condition ]
使用方括号时，要注意在条件两边加上空格。

### 7.1、文件测试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/159376900403ea9a873c71841021a74e.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/13570893aa480148c097c9a199ca02bd.png)

### 7.2、字符串测试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/560f8917302b2d1aa78a2d6e01a64d4c.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8dd1efe7db24a445f90a9c0da1650311.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/28086c8b322757d2aaab56388f6933c5.png)

### 7.3、数值测试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/06b8af34c9f0ab30e29406a21efbe5e4.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6f22faf6da0d24bbe47bd1353be5c993.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/75aafea52d7b0aef3b4e4b2c90bede22.png)

### 7.4、符合语句测试

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0f1c32d616632add5614d6c169cc10a2.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/04a4459a7b091f49a272d8170c4f14cb.png)

## 8、控制语句

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e8046e3877db84d8b1fdb1b8b8c86842.png)

### 8.1、if 控制语句

```cpp
格式一：
if [条件1]; then
    执行第一段程序
else
    执行第二段程序
fi
格式二：
if [条件1]; then
    执行第一段程序
elif [条件2]；then
执行第二段程序
else
    执行第三段程序
fi
1234567891011121314
```

#### 案例：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/67684dc92b3df1bbe03269b379f1543f.png)

#### 案例：判断当前路径下有没有文件夹 有就进入创建文件 没有 就创建文件夹 再进入创建文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/caffead1d01abc8fa1e20c5944174b8c.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/58eb9e92cab26f67e20fa45d15648198.png)

#### 案例：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2bff88621b8e7132ffdb5b4781a0b767.png)
运行结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/205b1f453ae19978810b00afcfa9f710.png)

### 8.2、case

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/741995107703bf03c3aab67e240de67b.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3543f032c9e715e2d36b437fc6583067.png)

### 8.3、for 循环语句

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1785c580ca4591c103b1ae6d4a8532b2.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7671b502c4ad004f9020218a3ddd3993.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/58a3873f47beecdfd3f77759345085db.png)

#### 案例：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/89bcc09a4ab57393763bf74e46b55d03.png)

#### 案例：扫描当前文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0addfd80f503214d08348e3489604422.png)

### 8.4、while

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/50b732429b8206167c2006a2f25d1071.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/784a2cba01b267d09037fd45ec13ca4b.png)

### 8.5、until

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7c992ce671c02b4be01249d9305ce884.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e214573663522a553a5c4cccba7e2136.png)

### 8.6、break continue

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c5fc270903c416849134f569adca64eb.png)

## 9、[函数](https://marketing.csdn.net/p/3127db09a98e0723b83b2914d9256174?pId=2782&utm_source=glcblog&spm=1001.2101.3001.7020)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bdfe7f34eb6ecfdcfa36885485a4e29c.png)
所有函数在使用前必须定义，必须将函数放在脚本开始部分，直至 shell 解释器首次发现它时，才可以使用
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a21b7acfc7a696166215398a7daaf4b2.png)

#### 案例：求最值

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a46596d8a89929e925b91033b0bff489.png)

#### 案例：函数分文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1df93188ebb96dc0bf9f1a4ab9a63eac.png)
fun.sh
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3b6536ae393768432fd78f4155f97344.png)
24_sh.sh
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/a1294ba33cdc4b89043f10682c326a82.png)

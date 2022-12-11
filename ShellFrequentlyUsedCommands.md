## **echo -n 和echo -e 参数意义**

echo -n 不换行输出

```
$echo -n "123"
$echo "456"
```

最终输出 

```
123456 
```

而不是

```
123

456
```

## echo -e 处理特殊字符

若[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)中出现以下字符，则特别加以处理，而不会将它当成一般文字输出：
```
 \a 发出警告声；
 \b 删除前一个字符；
 \c 最后不加上换行符号；
 \f 换行但[光标](https://so.csdn.net/so/search?q=光标&spm=1001.2101.3001.7020)仍旧停留在原来的位置；
 \n 换行且光标移至行首；
 \r 光标移至行首，但不换行；
 \t 插入tab；
 \v 与\f相同；
 \ 插入\字符；
 \nnn 插入nnn（八进制）所代表的ASCII字符；
```



```
$echo -e "a\adddd" //输出同时会发出报警声音

adddd
```



```
$echo -e "a\ndddd" //自动换行

a

dddd
```





## Shell编程中Shift的用法

位置参数可以用`shift`命令**左移**。比如`shift 3`表示原来的`$4`现在变成`$1`，原来的`$5`现在变成`$2`等等，原来的`$1`、`$2`、`$3`丢弃，`$0`**不移动**。**不带参数的**`shift`**命令相当于**`shift 1`。

对于位置变量或命令行参数，其个数必须是确定的，或者当 Shell 程序不知道其个数时，可以把所有参数一起赋值给变量**$\***。若用户要求 Shell 在不知道位置变量个数的情况下，还能逐个的把参数一一处理，也就是在 $1 后为 $2,在 $2 后面为 $3 等。$1 的值在 shift 命令执行后就不可用了

### 测试 shift 命令(x_shift.sh)
```
 until [ $# -eq 0 ]
 do
   echo "第一个参数为: $1 参数个数为: $#"
   shift
 done
```

 执行以上程序x_shift.sh：`./x_shift.sh 1 2 3 4`
 结果显示如下：

```
 第一个参数为: 1 参数个数为: 4
 第一个参数为: 2 参数个数为: 3
 第一个参数为: 3 参数个数为: 2
 第一个参数为: 4 参数个数为: 1
```


   从上可知 shift 命令每执行一次，变量的个数($#)减一，而变量值提前一位，下面代码用 until 和 shift 命令计算所有命令行参数的和。

### shift 上档命令的应用(x_shift2.sh)
```
 if [ $# -eq 0 ]
 then
   echo "Usage:x_shift2.sh 参数"
   exit 1
 fi
 sum=0
 until [ $# -eq 0 ]
 do
   sum=`expr $sum + $1`
   shift
 done
 echo "sum is: $sum"
```


 执行上述程序:$x_shift2.sh 10 20 15, 其显示结果为：`45`

 ### Shift 命令一个重要用途

Bsh 定义了9个位置变量，从 $1 到 $9,这并不意味着用户在命令行只能使用9个参数，借助 shift 命令可以访问多于9个的参数。
Shift 命令一次移动参数的个数由其所带的参数指定。例如当 shell 程序处理完前九个命令行参数后，可以使用 shift 9 命令把 $10 移到 $1。



## shell中各种括号的作用()、(())、[]、[[]]、{} 

### 小括号，圆括号（）

#### 单小括号 ()

  ①命令组。括号中的命令将会新开一个子shell顺序执行，所以括号中的变量不能够被脚本余下的部分使用。括号中多个命令之间用分号隔开，最后一个命令可以没有分号，各命令和括号之间不必有空格。

  ②命令替换。等同于`cmd`，shell扫描一遍命令行，发现了$(cmd)结构，便将$(cmd)中的cmd执行一次，得到其标准输出，再将此输出放到原来命令。有些shell不支持，如tcsh。

  ③用于初始化数组。如：array=(a b c d)

 

#### 双小括号 (( ))

  ①整数扩展。这种扩展计算是整数型的计算，不支持浮点型。((exp))结构扩展并计算一个算术表达式的值，如果表达式的结果为0，那么返回的退出状态码为1，或者 是"假"，而一个非零值的表达式所返回的退出状态码将为0，或者是"true"。若是逻辑判断，表达式exp为真则为1,假则为0。

  ②只要括号中的运算符、表达式符合C语言运算规则，都可用在$((exp))中，甚至是三目运算符。作不同进位(如二进制、八进制、十六进制)运算时，输出结果全都自动转化成了十进制。如：echo $((16#5f)) 结果为95 (16进位转十进制)

  ③单纯用 (( )) 也可重定义变量值，比如 a=5; ((a++)) 可将 $a 重定义为6

  ④常用于算术运算比较，双括号中的变量可以不使用$符号前缀。括号内支持多个表达式用逗号分开。 只要括号中的表达式符合C语言运算规则,比如可以直接使用for((i=0;i<5;i++)), 如果不使用双括号, 则为for i in `seq 0 4`或者for i in {0..4}。再如可以直接使用if (($i<5)), 如果不使用双括号, 则为if [ $i -lt 5 ]。

### 中括号，方括号[]

#### 单中括号 []

  ①bash 的内部命令，[和test是等同的。如果我们不用绝对路径指明，通常我们用的都是bash自带的命令。if/test结构中的左中括号是调用test的命令标识，右中括号是关闭条件判断的。这个命令把它的参数作为比较表达式或者作为文件测试，并且根据比较的结果来返回一个退出状态码。if/test结构中并不是必须右中括号，但是新版的Bash中要求必须这样。

  ②Test和[]中可用的比较运算符只有=和!=，两者都是用于字符串比较的，不可用于整数比较，整数比较只能使用-eq，-gt这种形式。无论是字符串比较还是整数比较都不支持大于号小于号。如果实在想用，对于字符串比较可以使用转义形式，如果比较"ab"和"bc"：[ ab \< bc ]，结果为真，也就是返回状态为0。[ ]中的逻辑与和逻辑或使用-a 和-o 表示。且[]前后都有空格。

  ③字符范围。用作正则表达式的一部分，描述一个匹配的字符范围。作为test用途的中括号内不能使用正则。

  ④在一个array 结构的上下文中，中括号用来引用数组中每个元素的编号。

 

#### 双中括号[[ ]]

  ①[[是 bash 程序语言的关键字。并不是一个命令，[[ ]] 结构比[ ]结构更加通用。在[[和]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换。

  ②支持字符串的模式匹配，使用=~操作符时甚至支持shell的正则表达式。字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]，结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。

  ③使用[[ ... ]]条件判断结构，而不是[ ... ]，能够防止脚本中的许多逻辑错误。比如，&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错。比如可以直接使用if [[ $a != 1 && $a != 2 ]], 如果不适用双括号, 则为if [ $a -ne 1] && [ $a != 2 ]或者if [ $a -ne 1 -a $a != 2 ]。

  ④bash把双中括号中的表达式看作一个单独的元素，并返回一个退出状态码。
 例子：

1. **if ($i<5)**  

2. **if [ $i -lt 5 ]**  

3. **if [ $a -ne 1 -a $a != 2 ]**  

4. **if [ $a -ne 1] && [ $a != 2 ]**  

5. **if [[ $a != 1 && $a != 2 ]]**  

7. **for i in $(seq 0 4);do echo $i;done**  

8. **for i in `seq 0 4`;do echo $i;done**  

9. **for ((i=0;i<5;i++));do echo $i;done**  

10. **for i in {0..4};do echo $i;done**  

### 大括号、花括号 {}

#### 常规用法

①大括号拓展。(通配(globbing))将对大括号中的文件名做扩展。在大括号中，不允许有空白，除非这个空白被引用或转义。第一种：对大括号中的以逗号分割的文件列表进行拓展。如 touch {a,b}.txt 结果为a.txt b.txt。第二种：对大括号中以点点（..）分割的顺序文件列表起拓展作用，如：touch {a..d}.txt 结果为a.txt b.txt c.txt d.txt 

②代码块，又被称为内部组，这个结构事实上创建了一个匿名函数 。与小括号中的命令不同，大括号内的命令不会新开一个子shell运行，即脚本余下部分仍可使用括号内变量。括号内的命令间用分号隔开，最后一个也必须有分号。{}的第一个命令和左括号之间必须要有一个空格。{}也可以用于多行注释，作为函数包起来只是不调用即可。

  

#### 几种特殊的替换结构

```
${var:-string}
${var:+string}
${var:=string}
${var:?string}
```

①${var:-string}和${var:=string}:若变量var为空，则用在命令行中用string来替换${var:-string}，否则变量var不为空时，则用变量var的值来替换${var:-string}；对于${var:=string}的替换规则和${var:-string}是一样的，所不同之处是${var:=string}若var为空时，用string替换${var:=string}的同时，把string赋给变量var： ${var:=string}很常用的一种用法是，判断某个变量是否赋值，没有的话则给它赋上一个默认值。
 ② ${var:+string}的替换规则和上面的相反，即只有当var不是空的时候才替换成string，若var为空时则不替换或者说是替换成变量 var的值，即空值。(因为变量var此时为空，所以这两种说法是等价的) 
 ③${var:?string}替换规则为：若变量var不为空，则用变量var的值来替换${var:?string}；若变量var为空，则把string输出到标准错误中，并从脚本中退出。我们可利用此特性来检查是否设置了变量的值。

**补充扩展**：在上面这五种替换结构中string不一定是常值的，可用另外一个变量的值或是一种命令的输出。

#### 四种模式匹配替换结构

模式匹配记忆方法：
 \# 是去掉左边(在键盘上#在$之左边)
 % 是去掉右边(在键盘上%在$之右边)
 \#和%中的单一符号是最小匹配，两个相同符号是最大匹配。

${var%pattern},${var%%pattern},${var#pattern},${var##pattern}

   第一种模式：${variable%pattern}，这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最短的匹配模式
    第二种模式： ${variable%%pattern}，这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
    第三种模式：${variable#pattern} 这种模式时，shell在variable中查找，看它是否一给的模式pattern开始，如果是，就从命令行把variable中的内容去掉左边最短的匹配模式
    第四种模式： ${variable##pattern} 这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
    这四种模式中都不会改变variable的值，其中，只有在pattern中使用了*匹配符号时，%和%%，#和##才有区别。结构中的pattern支持通配符，*表示零个或多个任意字符，?表示仅与一个任意字符匹配，[...]表示匹配中括号里面的字符，[!...]表示不匹配中括号里面的字符。

\1. # var=testcase  

\2. # echo $var  

\3. testcase  

\4. # echo ${var%s*e}  

\5. testca  

\6. # echo $var  

\7. testcase  

\8. # echo ${var%%s*e}  

\9. te 

\10. # echo ${var#?e}  

\11. stcase 

\12. # echo ${var##?e}  

\13. stcase 

\14. # echo ${var##*e}  

\15.  

\16. # echo ${var##*s}  

\17. e  

\18. # echo ${var##test}  

\19. **case**  

 

#### 字符串提取和替换

${var:num},${var:num1:num2},${var/pattern/pattern},${var//pattern/pattern}

​    第一种模式：${var:num}，这种模式时，shell在var中提取第num个字符到末尾的所有字符。若num为正数，从左边0处开始；若num为负数，从右边开始提取字串，但必须使用在冒号后面加空格或一个数字或整个num加上括号，如${var: -2}、${var:1-3}或${var:(-2)}。     
​     第二种模式：${var:num1:num2}，num1是位置，num2是长度。表示从$var字符串的第$num1个位置开始提取长度为$num2的子串。不能为负数。
​    第三种模式：${var/pattern/pattern}表示将var字符串的第一个匹配的pattern替换为另一个pattern。。    
​    第四种模式：${var//pattern/pattern}表示将var字符串中的所有能匹配的pattern替换为另一个pattern。

\1. [root@centos ~]# var=/home/centos 

\2. [root@centos ~]# echo $var 

\3. /home/centos 

\4. [root@centos ~]# echo ${var:5} 

\5. /centos 

\6. [root@centos ~]# echo ${var: -6} 

\7. centos 

\8. [root@centos ~]# echo ${var:(-6)} 

\9. centos 

\10. [root@centos ~]# echo ${var:1:4} 

\11. home 

\12. [root@centos ~]# echo ${var/o/h} 

\13. /hhme/centos 

\14. [root@centos ~]# echo ${var//o/h} 

\15. /hhme/cenths 

 

### 符号$后的括号

（1）${a} 变量a的值, 在不引起歧义的情况下可以省略大括号。

（2）$(cmd) 命令替换，和`cmd`效果相同，结果为shell命令cmd的输，过某些Shell版本不支持$()形式的命令替换, 如tcsh。

（3）$((expression)) 和`exprexpression`效果相同, 计算数学表达式exp的数值, 其中exp只要符合C语言的运算规则即可, 甚至三目运算符和逻辑表达式都可以计算。

### 使用

1、多条命令执行

（1）单小括号，(cmd1;cmd2;cmd3) 新开一个子shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后可以没有分号。

（2）单大括号，{ cmd1;cmd2;cmd3;} 在当前shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后必须有分号, 第一条命令和左括号之间必须用空格隔开。
 对{}和()而言, 括号中的重定向符只影响该条命令， 而括号外的重定向符影响到括号中的所有命令。 

例如:

```
#!/bin/bash
str1="test1"
str2="Test1"
num1=33
num2=4
###################string complare#########
#1    [] use =
if [ "$str1" = "$str2" ]
        then
        echo "#1  ${str1} equals to ${str2}"
else
        echo "#1  ${str1} not e2 $str2"
fi
#2    [] use   !=
if [ "$str1" != "$str2" ]
        then
        echo "#2  ${str1} not equals to ${str2}"
else
        echo "#2  ${str1} eq2 $str2"
fi
#3    [] use  \<   (注意字符串之间不能有空格)
if [ "${str1}"\<"${str2}" ]
        then
        echo "#3  ${str1} less than  ${str2}"
else
        echo "#3  ${str1} eq2 or grater than $str2"
fi
#4 [[]] use <
if [[ "$str1" < "$str2" ]]
        then
        echo "#4  ${str1} less than ${str2}"
else
        echo "#4  ${str1} eq2 or grater than $str2"
fi
 
#################### number complare  ############################
#1    [] use -lt.-gt,-ge
if [ "$num1" -lt "${num2}"  ]
        then
        echo "${num1} less than ${num2}"
else
        echo "${num1} eq2 or grater than $num2"
fi
#2    [] use \<  is complare as string　　(错误的示范，不能在[]中使用转义比较数字，会当成字符串比较)
if [ "$num1" \< "${num2}"  ]
        then
        echo "${num1} less than ${num2}"
else
        echo "${num1} eq2 or grater than $num2"
fi
#3    (())   use  <　　
if (( "$num1" < "${num2}"  ))
        then
        echo "${num1} less than ${num2}"
else
        echo "${num1} eq2 or grater than $num2"
fi
 
#####################(()) use to  number +-*/ ###########################
echo "############# (()) use ro number +-*/%   ##############"
echo $(($num1 + $num2))
echo $(($num1 - $num2))
echo $(($num1 * $num2))
echo $(($num1 / $num2))
echo $(($num1 % $num2))
####################### ()   ###################
echo "############# \$() use like \`\`  ##################"
echo `which pwd`
echo $(which pwd)
#####################  \${} to get variables  #########################
echo "################## \${var} is like \$var   ################"
echo ${str1}
echo $str1
```



运行结果:

```
#1  test1 not e2 Test1
#2  test1 not equals to Test1
#3  test1 less than  Test1
#4  test1 less than Test1
33 eq2 or grater than 4
33 less than 4
33 eq2 or grater than 4
############# (()) use ro number +-*/%   ##############
37
29
132
8
1
############# $() use like ``  ##################
/usr/bin/pwd
/usr/bin/pwd
################## ${var} is like $var   ################
test1
test1
```

 

 ### 总结:

　　(1)$(cmd)与··(键盘上1左边的~)一样，都是命令替换，可以将执行结果提取出来

　　(2)[]使用的时候[ ]前后都必须有空格，且两个字符或数字之间的比较符左右也必须有空格。

　　(3)  []是test的另一种形式，[]中间只能使用= 和 != 比较字符串，如果使用< 、>需要进行转义\。

　　　　[]中间如果比较数字需要用 -lt 等符号，不能使用\<比较数字，会当成字符串处理。

　　(4)[[]]可用于处理逻辑命令，也可以用于处理字符串是否相等，且使用<、>不用转义符.

　　(5)(())可用于比较数字，且不用转义，而且也可以用于数字计算，比较的时候也是用普通的>,<。(())计算的时候运算符与数字之间不能有空格，例如: sum=$(($sum+4))

　　(6)字符串比较 用[],与普通的<,>,=,!=符号，如果使用<,>需要转义;或者使用[[]]比较字符串也是用普通符号不用转义

　　　　数字比较用[]的时候用-lt,-gt等符号，不能使用\<(因为会当成字符串处理);或者用(())比较数字用普通符号不用转义

　　**(7)****可以将$****理解为取变量的符号，$var** **或者 ${}** **，****在不影响语义的情况下可以省去{},****但是最好写上{}****。例如:test=XXX.$testWWWW.****这时候就必须加上{}****变为${test}WWWW**

 

 

 

 **更多的特殊符号参考:**[**http://www.jb51.net/article/69966.htm**](http://www.jb51.net/article/69966.htm)

 

## Shell里的 `` 、$( )、${ }、expr $((  ))

**所有****UNIX命令,要取结果或输出，都要用 $() 或 ` `**

| tt=**`** file test.sh **`**   echo $tt |
| -------------------------------------- |
| #sh test.sh   test.sh: ASCII text      |

 

| tar -zcvf lastmod.tar.gz **`find  . -mtime -1 -type f -print`** |
| ------------------------------------------------------------ |
| 将过去24小时（-mtime –2则表示过去48小时）内修改过的文件tar在一起 |


 **`` 和 $( ) 缺陷也相同，没回车换行，容易把多行合并**

| [mac@machome ~]$ vi test.sh   echo **`**ps -ef | grep syslog**`** |
| ------------------------------------------------------------ |
| [mac@machome ~]$ sh test.sh   root 1363 1 0 12:15 ? 00:00:00 syslogd -m 0 mac 15032 15030 0 18:13 pts/2  00:00:00 grep syslog |

 

| [macg@machome ~]$ vi test.sh      var=$(ls -l)   echo $var   |
| ------------------------------------------------------------ |
| [macg@machome ~]$ sh test.sh   total 44 -rw-rw-r-- 1 macg macg 126 Jun 8 23:40 1 -rw-rw-r-- 1 macg macg 0  Jun 8 17:53 100 -rw-rw-r-- 1 macg macg 16 Jun 9 19:00 111.txt -rw-rw-r-- 1  macg macg 15 Jun 9 19:00 22.txt -rw-rw-r-- 1 macg macg 15 Jun 9 19:00 33.txt  -rw-rw-r-- 1 macg macg 23 Jun 12 19:20 test.sh |


 **wc -l常与 $( ) 合用取其输出，计算行数，进而确定循环次数**

  count=$(more iftmp|wc -l)   while [ $i -le $count ]  



 **echo + $()****:** **echo内也可执行命令，并通过$()取该命令的输出**再显示
 常见使用：echo内，用$( )取awk和sed对某些命令修饰后的效果

| [macg@machome ~]$ vi test   #choose a iinterface to config   **/sbin/ifconfig -a \| awk '$2=="Link" {print($1,$3)}' >iftmp**   echo "choose one of interface to config"   i=1   **count=$(more iftmp\|wc -l)**   while [ $i -le $count ]   do   echo "$i)-------- **$(**sed -n "${i}p" iftmp**)**"            **用****sed** **修饰输出**   ii=$(($i+1))   done |
| ------------------------------------------------------------ |
| [macg@machome ~]$ sh test      choose one of interface to config   1)-------- eth0 encap:Ethernet   2)-------- lo encap:Local   3)-------- sit0 encap:IPv6-in-IPv4   give the interface a host name[such as:xxx-hme0]: |


 **${}** **和** **$()** **的区别：**

**${}** **避免在****echo中变量被连读**

| $ vi ttt.sh      GONE="**(ServerType\|BindAddress\|Port\|AddModule\|ClearModuleList\|**"   GONE="**${GONE}**AgentLog\|RefererLog\|RefererIgnore\|FancyIndexing\|"   echo $GONE |
| ------------------------------------------------------------ |
| $ sh ttt.sh   **(ServerType\|BindAddress\|Port\|AddModule\|ClearModuleList**\|AgentLog\|RefererLog\|RefererIgnore\|FancyIndexing |


 **${i}可以在任何场合替代普通变量$i**

| ttt=${REMOTEHOST}   echo ${ttt}              | ttt=$REMOTEHOST   echo $ttt                  |
| -------------------------------------------- | -------------------------------------------- |
| [macg@localhost ~]$ sh ttt.sh   192.168.1.11 | [macg@localhost ~]$ sh ttt.sh   192.168.1.11 |


 **expr数学表达式**
 **shell语言里没有运算公式**，必须借助expr语句实现：

| expr 变量 **‘**+-x  /**’** 变量 | expr $a **'**+**'** $b                    |
| ------------------------------- | ----------------------------------------- |
| expr 常数 **‘**+-x  /**’** 常数 | [mac@machome ~]$ expr 3 **'**+**'** 2   5 |

**运算符号都要加引号**,单引号双引号均可
 **输出是结果**


 **为什么if expr 3 '+' 2 得5，但if仍走then ? 非0应该走else啊**
 因为**expr表达式返回的是"返回值"，不是"结果"**

| [mac@machome ~]$ vi test.sh   expr 3 '+' 2   echo $?         |
| ------------------------------------------------------------ |
| [mac@machome ~]$ sh test.sh   **5**        **输出****5   0**        **返回****0**   **只要加法成功，返回值就是****0****，****0****为真** |


 **expr 表达式计算完后如何获得结果？用 $(expr** **…****) 或`expr** **…****`
** 其实就是取其输出，expr的输出就是结果

| var = expr $a '+' $b   echo "final is $var"                  | var=$(expr $a '+' $b)   echo "final is $var" |
| ------------------------------------------------------------ | -------------------------------------------- |
| test.sh: line 7: var: command not  found    加法完成后结果赋值出错   final is | final is 7                                   |


 **expr在if语句里，不能用 [ ]，因为这里把它当作command**

| [mac@machome ~]$ vi test.sh   if **[** expr 3 '+' 2 **]** ; then   echo no5   else   echo is5   fi |
| ------------------------------------------------------------ |
| [mac@machome ~]$ sh test.sh   test.sh: line 1: [: too many arguments   is5 |

改成

| [mac@machome ~]$ vi test.sh   **if expr 3 '+' 2** ;  then       **expr  3 '+' 2****相当于****command**   echo no5   else   echo is5   fi |
| ------------------------------------------------------------ |
| [mac@machome ~]$ sh test.sh   5   no5                        |

​    

不过如果要进行结果判定，加$(),就必须用[ ]了,**因为把它当作表达式了**

| [macg@machome ~]$ vi test.sh   if **[ $(expr 3 '+' 2) != 5 ]**;then     而且[和$中间必须有空格   echo no5   else   echo is5   fi |
| ------------------------------------------------------------ |
| [macg@machome ~]$ sh test.sh   is5                           |



 **另一种运算格式 i++** **如何实现？****------ i=$(($i+1)**)

  while [ **$i -le $count** ]   do   command   **i=$(($i+1))**   done  


 **(( ))** **与** **[ ]** **作用完全相同**

| echo input:   read i   **i=$(($i+1))**   echo $i | echo input:   read i   **i=$[$i+1**]   echo $i |
| ------------------------------------------------ | ---------------------------------------------- |
| [macg@localhost ~]$ sh ttt.sh   input:   6   7   | [macg@localhost ~]$ sh ttt.sh   input:   6   7 |


 **反面证明(( ))与[ ]完全相同--------if (( ))**

| if (( $# != 3 )); then    echo "usage: $0 host user passwd"   fi | if [ $# != 3 ]; then    echo "usage: $0 host user passwd"   fi |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [macg@localhost ~]$ sh ttt.sh 1 2   usage: ttt.sh host user passwd | [macg@localhost ~]$ sh ttt.sh 1 2   usage: ttt.sh host user passwd |

 

## Bash技巧：详解用$获取变量值是否要加双引号或者大括号

本篇文章介绍在 Linux bash shell 中，用 $ 获取变量值时，是否要加双引号、是否要加大括号。

### 用 $ 获取变量值是否要加双引号

在 bash shell 脚本中，用 $ 来获取变量值时，有一些不加双引号，例如 $arg。有一些会加双引号，例如 "$arg"。

下面具体说明这两种形式之间的区别，什么情况下要加双引号，什么情况可以不加双引号。

**在** **bash中，各个参数之间默认用空格隔开。**

**当参数值本身就带有空格时，如果不加双引号把参数值括起来，那么这个参数值可能会被扩展为多个参数值，而丢失原本的完整值。**

具体举例说明如下：

```
$ function test_args() { 
  echo \$\#: $#;
  echo first: $1; 
  echo second: $2; 
}

$ args="This is a Test"

$ test_args $args

$#: 4

first: This

second: is

$ test_args "$args"

$#: 1

first: This is a Test

second:
```

这里定义了一个 *test_args* 函数，打印传入的 $1、$2 参数值。
 所给的 *args* 变量指定的字符串含有空格。

可以看到，当执行 test_args $args 时，args 变量的值被空格隔开成四个参数。
 而执行 test_args "$args" 时，args 变量的值保持不变，被当成一个参数。
 使用双引号把字符串括起来，可以避免空格导致单词拆分。

**即，当我们需要保持变量本身值的完整，不想被空格扩展为多个参数，那么就需要用双引号括起来**。

在给脚本或函数传递参数时，我们可能不确定获取到的参数值是否带有空格。
为了避免带有空格导致不预期的单词拆分，造成参数个数发生变化，建议传参时每个参数都使用双引号括起来。

用 $ 获取变量值是否要加大括号

在 bash shell 脚本中，用 $ 来获取变量值时，有一些不加大括号，例如 $var。有一些会加大括号，例如 ${var}。
 下面具体说明这两种形式之间的区别，什么情况下要加大括号，什么情况可以不加大括号。

查看 man bash 里面对 ${parameter} 表达式的含义说明如下：

```
${parameter}
The value of parameter is substituted.
The braces are required when parameter is a positional parameter with more than one digit, or when parameter is followed by a character which is not to be interpreted as part of its name.
The parameter is a shell parameter or an array reference (Arrays).
```



即，大括号 {} 的作用是限定大括号里面的字符串是一个整体，不会跟相邻的字符组合成其他含义。

例如，有一个 var 变量值是 "Say"，现在想打印这个变量值，并跟着打印 "Hello" 字符串，也就是打印出来 "SayHello" 字符串。那么获取 var 变量值的语句和 "Hello" 字符串中间就不能有空格，否则 *echo* 命令会把这个空格一起打印出来。

但是写为 $varHello 达不到想要的效果。
具体举例如下：

```
var="Say"
echo $var Hello
Say Hello

echo $varHello

echo ${var}Hello
SayHello

$ echo "$var"Hello
SayHello
```



可以看到，$var Hello 这种写法打印出来的 "Say" 和 "Hello" 中间有空格，不是想要的结果。
 而 $varHello 打印为空，这其实是获取 varHello 变量的值，这个变量没有定义过，默认值是空。
 ${var}Hello 打印出了想要的结果，用 {} 把 var 括起来，明确指定要获取的变量名是 var，避免混淆。
 "$var"Hello **用双引号把 $var 括起来，也可以跟后面的 "Hello" 字符串区分开**。

即，当用 $ 获取变量值时，如果变量名后面跟着空白字符，隔开了其他内容，可以不用大括号来把变量名括起来。

**如果变量名后面直接跟着不属于变量名自身的其他字符，就需要用大括号把变量名括起来，以便明确该变量的名称**。

 

# getopt 命令

getopt 命令并不是 bash 的内建命令，它是由 util-linux 包提供的外部命令。相比较 bash 的内置命令，getopt 不只支持短参 -s，还支持 --longopt的 长参数，甚至支持 -longopt 的简化参数。getopt 可以用于 tcsh 等其它的 [sh](https://ywnz.com/linux/sh/)。

**常用参数**
 -a：使 getopt 长参数支持"-"符号打头，必须与-l同时使用
 -l：后面接 getop t支持长参数列表
 -n：program如果getopt处理参数返回错误，会指出是谁处理的这个错误，这个在调用多个脚本时，很有用
 -o：后面接短参数列表，这种用法与getopts类似
 -u：不给参数列表加引号，默认是加引号的（不使用-u选项），例如在加不引号的时候 

--longopt "select * from db1.table1" 

$2只会取到select ，而不是完整的SQL语句。

  可以同时处理短选项和长选项。

getopt 命令不是一个标准的 unix 命令，但它在大多数 Linux 的发行版中都自带了有，如果没有，也可以从 [getopt官网](http://software.frodo.looijaard.name/getopt/)上下载安装。

在 getopt 的较老版本中，存在一些 bug，不大好用，在后来的版本中解决了这些问题，我们称之为getopt 增强版。通过 -T 选项，我们可以检查当前的 getopt 是否为增强版，返回值为4，则表明是增强版的。

 ```
#getopt -T
#echo $?
4

#getopt -V
getopt (enhanced) 1.1.4
 ```

getopt 命令与 getopts 命令不同，它实际上是通过将参数规范化来帮助我们处理的。具体的用法，如下面的脚本：



```
#!/bin/bash

# echo $@

# 例如：ARGS=`getopt -o ab:c:: --long along,blong:,clong:: -n 'example.sh' -- "$@"`

# -o 或 --options 选项后面接可接受的短选项，如 ab:c::，表示可接受的短选项为-a -b -c，其中 -a 选项不接参数，-b 选项后必须接参数，-c 选项的参数为可选


# -l 或 --long 选项后面接可接受的长选项，用逗号分开，冒号的意义同短选项。

# 上述冒号的含义：不带冒号不接参数，一个冒号必须有参数，两个冒号参数可选

# -n 选项后接选项解析错误时提示的脚本名字

# 下面这段脚本 -a 与 --along 参数是相同的效果

ARGS=`getopt -o ab:c:: --long along,blong:,clong:: -n 'example.sh' -- "$@"`

if [ $? != 0 ]; then
  echo "Terminating..."
  exit 1
fi


# echo $ARGS
# 将规范化后的命令行参数分配至位置参数（$1,$2,...)

eval set -- "${ARGS}"


while true
do
  case "$1" in
​    -a|--along) 
​      echo "Option a";
​      shift
​      ;;
​    -b|--blong)
​      echo "Option b, argument $2";
​      shift 2
​      ;;
​    -c|--clong)
​      case "$2" in
​        "")
​          echo "Option c, no argument";
​          shift 2 
​          ;;
​        *)
​          echo "Option c, argument $2";
​          shift 2;
​          ;;
​      esac
​      ;;
​    --)
​      shift
​      break
​      ;;
​    *)
​      echo "Internal error!"
​      exit 1
​      ;;
  esac
done
 

# 处理剩余的参数
for arg in $@
do
  echo "processing $arg"
done
```

grep 'try{' lib/* -r  --color
grep 'if(' lib/* -r --color 
grep ',\w' lib/* -r --color 
grep '\w=\w' lib/* -r --color
grep ‘\W=\w' lib/* -r --color 
grep ‘\w=\W’ lib/* -r --color 
grep -E 'MyFetch\.(post|get)\($' lib/* -r --color
grep Avator lib/* -r grep '>\w' lib/* -r 
grep '\.sw \*'  mobile-*/lib/* -r --color
grep Future.delayed mobile-*/lib/* -r --color
grep '\w{'  mobile-*/lib/* -r —color
grep 'Icon($'  mobile-*/lib/* -r --color
grep 'Icon'  mobile-*/lib/* -r | grep 'size:' | grep -v '\w\+\.w)' | grep -v '\w\+\.w,' | grep Icon --color
grep 'MyIcon'  mobile-*/lib/* -r | grep 'size:' | grep -v '\w\+\.w)' | grep -v '\w\+\.w,' | grep MyIcon --color
grep 'MyIcon'  mobile-*/lib/* -r | grep 'size:' | grep -v '\.w)' | grep MyIcon --color   grep .ttf ./src_* -r --color
grep .ttf ./src_* -r --color
ls | grep -v dist | grep -v node_modules | xargs grep -r --color
grep ',\w' lib/* -r | grep -v substring | grep ',\w' --color
ls | grep -v dist | grep -v node_modules | xargs grep '{}' -r --color
alias vuegrep="ls | grep -v dist | grep -v node_modules | xargs grep -r --color"
alias grep="grep --color"

```
#!/bin/bash
# shell笔记

# +++++++++++++++++++++++++++++++++++++++++++
#
# 注意
＃
# 单引号不解析变量, 双引号都解析; 反引号会把其中的命令执行后返回结果
#　`cat users.txt` ≈ $(cat users.txt)
#　expr命令 用于表达式计算，e.g.: expr 100 + 200
# -------------------------------------------

# +++++++++++++++++++++++++++++++++++++++++++
#
# 接收用户的参数
#
# ./example.sh one two three four
# one two...分别对应 $1 $2
# $0 对应当前shell脚本程序的名称
#
# -------------------------------------------

# +++++++++++++++++++++++++++++++++++++++++++
#
# 接判断户的参数
#
# 测试语句格式, 两边均有空格 [ 表达式 ]
# 条件成立返回0 否则返回其他数值
#
# 条件测试语句分为4种：
#
# 1、文件测试语句：
# 
# 	-d 文件是否为目录
# 	-e 文件是否存在
# 	-f 是否为一般文件
# 	-r 是否有权读
# 	-w 是否有权写
# 	-x 是否有权执行
#
#	示例：	
#		[ -d /etc/ ]
#		// $?返回上一条命令执行的值
#		echo $?
#
# 2、逻辑测试语句 && 、||、!

# 3、整数值比较语句
#	仅是对数字的操作，由于冲突应使用规范的整数比较运算符进行操作
#
#	-eq
#	-ne	不等于
#	-gt
#	-lt
#	-le 小于等于
#	-ge 大于等于
#	
#	示例，查看剩余内存量第4列结果：
#		// awk 执行脚本... 这里即为取grep结果第4列的内容
#		FreeMem=`free -h | grep Mem: | awk '{print $4}'`
#		// Insufficient 不充足的
#		[ $FreeMem -lt 1024 ] && echo 'Insufficient Memory'
#
# 4、字符串比较语句 
# 	= 
#	!=
#	-z 字符串是否为空
# -------------------------------------------

# +++++++++++++++++++++++++++++++++++++++++++
# 流程控制语句
#	1、 if else
#		if 条件
#			then 语句
#		elif 条件
#			then 语句
#		else
#			语句
#		fi
#
# 	示例，简单的分数等级划分：
#		// read用来读取用户输入信息 接收到的值赋予后面指定变量
#		read -p 'Enter your score (0-100) : ' GRADE
#		if  $GRADE -ge 85 ] && [ $GRADE -le 100 ]
#			then 
#				echo "$GRADE is Excellent"
#		elif [ $GRADE -ge 70 ] && [ $GRADE -le 84 ]
#			then 
#				echo "$GRADE is Pass"
#		else
#			echo "$GRADE is Fail"
#		fi

# 2、 for循环
#		for 变量名 in 取值列表
#		do
#			语句
#		done

# 	示例，读取users.txt中信息并添加用户及密码：
#		// 添加几个用户名信息 一行一个...
#		vim users.txt
#		read -p 'Enter The Users Password : ' PASSWD
#		for UNAME IN `cat users.txt`
#		do
#			id $UNAME &> /dev/null
#			if [ $? -eq 0 ]
#				then
#					echo 'Already exists'
#			else
#				useradd $UNAME &> /de/null
#				echo $PASSWD | passwd --stdin $UNAME &> /dev/null
#				if [ $? -eq 0 ]
# 					then
#						echo "$UNAME , Create success"
#				else
#						echo "$UNAME , Create failed"
#				fi
#			fi
# 		done
#
# 3、while循环
#		while 条件
#		do
#			语句
#		done
#
# 	示例，猜测随机数：
#		PRICE=$(expr $RANDOM % 1000)
#		TIMES=0
#		echo '商品实际价格为0-999，猜猜看是多少？'
#		while true
#		do
#			read -p '请输入您猜测的价格数目：' INT
#			let TIMES++
#			if [ $INT -eq $PRICE ]
#				then
#					echo "恭喜您答对了，实际价格是 $PRICE"
#					echo "您总共猜测了 $TIMES 次"
#					exit 0
#			elif [ $INT -gt $PRICE ]
#				then
#					echo '太高了！'
#			else
#				echo '太低了'
#			fi
#		done
#		
# 4、switch
# 		case 变量 in
#		模式1)
#			语句
#		;;
#		模式2)
#			语句
#		;;
#		*)
#			语句
#		esac
#
#	示例，判断用户输入字符类型：
#		read -p '请输入一个字符，并按Enter确认：' KEY
#		case $KEY in
#		[a-z]|[A-Z])
#			echo '您输入的是字母'
#			;;
#		[0-9])
#			echo '您输入的是数子'
#			;;
#		*)
#			echo '输入的什么🐔'
#		esac
#
#
#
#

# -------------------------------------------
```
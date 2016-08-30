---
title: shell常用指令
categories:
  - null
date:
---

	grep -ioE 精确过滤匹配正则表达式
	grep ^aaa  匹配以aaa 开头的行
	grep [[:blank:]]aaa 匹配以aaa开头的单词行

	awk '{print $num}' 对每一条根据空格拆分成数组

	sort 排序  sort -rn 倒序 显示数量

	uniq -c  去重显示数量

	命令替换  ``  $(...)

	umask 077 删除除用户以外的其他人的访问权限
	temp=${TMPDIR-/tmp}/mytemp.$$  进程pid后缀 产生临时文件
	trap ' rm -f $temp' EXIT 完成时删除临时文件

	type command  显示命令的来源

	df  disk free 显示磁盘使用情况
	du  disk usage 磁盘用量 -sh  摘要


	分号可以分割命令 使多个命令可以在一行

	命令后面 加上& 可以将命令放到后台执行

	$? $@ $# $$ $0

	if..elif..else..fi

	-d dir -f file -e exist -z null -r read -w wirte -x executable

	case val in
		a)
		;;
		b)
		;;
		*)
		;;
	esac

	shitf 参数分别往前移动

	cat $file | while IFS=: read a b c d
	do
		.....
	done

	while IFS=: read a b c d
	do
		....
	done << $file


	双引号会解析变量 单引号会将整体当做字符串解析 倒引号会对所有命令尝试解析

	$# 返回动态参数个数
	$[@*] 返回动态参数列表

	2种方式执行shell  sh script.sh  ||  ./script [设定 shebang]


	每种颜色都有对应的颜色码。比如:重置=0,黑色=30,红色=31,绿色=32,黄色=33,蓝 色=34,洋红=35,青色=36,白色=37。
	要打印彩色文本,可输入如下命令:
    echo -e "\e[1;31m This is red text \e[0m"
	\e[1;31将颜色设为红色,\e[0m将颜色重新置回。只需要将31替换成想要的颜色码就可以了。 要设置彩色背景,经常使用的颜色码是:重置=0,黑色=40,红色=41,绿色=42,黄色=43,
	蓝色=44,洋红=45,青色=46,白色=47。 要打印彩色文本,可输入如下命令:
    echo -e "\e[1;42m Green Background \e[0m"


	mac declare -a var_arr='([0]="1" [1]="2" [2]="3" [3]="4")' 关联数组定义
	array=(1 2 3 4 5)
	array[0]=1
	array[1]=2  





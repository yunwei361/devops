# 各种类型的运算

### 自定义四则运算

#### 需求

开发一个计算脚步：

要求接受用户输入的数字和运算符，对用户输入信息（数字和运算符）进行判断，最终得出结果

#### 脚步实现

```sh
#!/bin/bash

print_useage(){
    printf "Please enter an integer!!!\n"
    exit 1
}

read -p "Please input your number: " firstnum

if [ -n "`echo ${firstnum} | sed 's/[0-9]//g'`" ]
    then
        print_useage
fi

read -p "Please input your operator: " operator

if [ "${operator}" != "+" ] && [ "${operator}" != "-" ] && [ "${operator}" != "*" ] && [ "${operator}" != "/" ]
    then
        echo "只允许输入+|-|*|/"
        exit 2
fi

read -p "Please input second number: " secondnum

if [ -n "`echo ${secondnum} | sed 's/[0-9]//g'`" ]
    then
        print_useage
fi

echo "${firstnum}${operator}${secondnum}结果是：$((${firstnum}${operator}${secondnum}))"
#echo "${firstnum}${operator}${secondnum}结果是：$((firstnum${operator}secondnum))" #也可以
```

#### 知识点解析

双小括号的数学运算



### 判断用户的输入是否是整数

#### 不完美方式

使用 expr 进行计算，只要输入是整数，expr 命令的返回值为0（唯一特殊情况：计算结果为0的时候返回值为1），然后对返回值进行判断即可确定用户输入是否为整数

此案例不考虑计算结果为0的特殊情况

```sh
#!/bin/bash

read -p "Input a integer: " num

expr $num + 9876543210123456789 &> /dev/null

if [ $? -eq 0 ]; then
  echo "This is a integer!"
else
  echo "Not a integer!"
fi
```

#### 相对完美方式（此方式需注意输入为空的情况，见脚本内说明）

test 命令使用比较操作符时需要 integer 类型做比较，如果是别的类型就会报错

```sh
#!/bin/bash

read -p "Input a integer: " num

# 这一步变量需要用双引号引起来，如果变量为空且没引起来，则 test 命令的返回值也为 0
test "$num" -eq "$num" 2> /dev/null

if [ $? -eq 0 ]; then
  echo "This is a integer!"
else
  echo "Not a integer!"
fi
```

#### 完美方式

```sh
#!/bin/bash

read -p "Input a integer: " num

if [[ $num =~ ^[0-9]+$ ]]; then
  echo "This is a integer!"
else
  echo "Not a integer!"
fi
```



### 整数计算器

```sh
#!/bin/bash

# 1.check args
if [ $# -ne 2 ]; then
  echo 'Usage: $0 num1 num2'
  echo 'Input 2 integers'
  exit 1
fi

num1=$1
num2=$2

# 2.check integers
if [ $num1 -eq $num1 2> /dev/null ] && [ $num2 -eq $num2 2> /dev/null ]; then
  :
else
  echo 'Usage: $0 num1 num2'
  echo 'Input 2 integers'
  exit 2
fi

let "result_plus = num1 + num2"
let "result_minus = num1 - num2"
let "result_multiply = num1 * num2"
let "result_divide = num1 / num2"
let "result_yu = num1 % num2"
let "result_mi = num1 ** num2"

echo "$num1 + $num2 = $result_plus"
echo "$num1 - $num2 = $result_minus"
echo "$num1 * $num2 = $result_multiply"
echo "$num1 / $num2 = $result_divide"
echo "$num1 % $num2 = $result_yu"
echo "$num1 ** $num2 = $result_mi"
```

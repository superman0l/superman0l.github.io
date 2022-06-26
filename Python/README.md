# 一些关于Python的东西
## 初识
`# coding=utf-8`
要有了这行才能用中文作字符串
`# 这个操作是单行注释`
```
'''
这样可以
注释多行
'''
"""
三个双引号也行
"""
```
## 基本语法
`print("Hello World")`
最基础的打印语句（自带换行）
`print("Hello");print("World")`
在同一行里写多个语句用分号隔开
`print("Hel"),;print("lo")`
在print语句后加逗号不换行
```
if True:
    print("It's")
    print("true")
elif True:
    print("else if")
else:
    print("false")
```
python不用大括号，用相同缩进实现控制代码块
```
a="这是一/
行字符串"
```
斜杠来多行写一行
```
sen1='sentence1'
sen2="sentence2"
sen3="""这个比较牛逼可以
包括多行的语句"""
```
单引号双引号都能当字符串
```
sts=row_input("这行字符可以作提示语提醒输入")
print(sts)
```
行输入并输出操作
## 变量
### 常规变量
```
a=1;b="abc";c=2.2
e=d=f=2;x,y,z=1,3.4,"Aimer"
```
python万物皆auto 恁牛啊
### string字符串
`sts="Misaka Mikoto";print(sts[0:6])`
截取从0到5的字符串，打印我老婆的姓
### list列表
`list=["string1",666,3.1415926];print(list)`
列表，啥都能塞`['string1',666,3.1415926]`这是打印结果
### tuple元组
`tuple=('steins;gate',1.0827656)`
不可更改的列表
### dictionary字典
```
dict={};dict['wife']='Kal'tsit';dict[1]='me'
dict2={'W':'限定','水陈':'批脸'}
```
类似map，但类型更灵活
`print dict.keys() # 输出所有key`
`print dict.values() # 输出所有value`
### 类型转换
`int(x);float(y)`类推
## 运算
### 数学运算
`a,b=2,3;print a**b`幂运算，可用于赋值**=
`a,b=9,-2;print a//b`整除向下取整整数，可用于赋值//=
### 呆逼逻辑运算
&&->and
||->or
!->not
### 还行的成员运算
`if (1 in list):`
`if (1 not in list):`
### 身份运算
`if a is b`
`if a is not b`
判断两个变量引用对象是否为同一个内存空间，感觉暂时用不上
## while语句
```
a=5
while a>=1:
    a-=1
    print a
else:
    print("now a = 0")
```
while-else结构
## for语句
```
rwby=['ruby','weiss','yang','blake']
for name in rwby:
    print(name)
else: print("There are 4 kawaii girls")

for id in range(4):
    print(rwby[i])
else: print("There are 4 kawaii girls")
```
以上两个for-else都是用for遍历，其中else语句当for正常循环结束之后执行
输出皆为
```
ruby
weiss
yang
blake
There are 4 kawaii girls
```
## pass语句
```
def function():
    pass
```
pass什么用都没有，只是一个占位空语句，因为函数如果为空会报错所以用pass占位

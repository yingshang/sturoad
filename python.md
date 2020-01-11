## print()的sep参数
```
name = "test"
age = 10
gender = "男"
print(name,age,gender,sep='--')
```
运行结果
```
test--10--男
```

## 格式化输出
```
name = "test"
age = 10
gender = "男"
print("%s++%s++%s" %(name,age,gender))
```
运行结果
```
test++10++男
```

## format使用
```
name = "test"
age = 10
gender = "男"
print("我叫{0}今年{1}岁是个{2}".format(name,age,gender))
print("我叫{2}今年{0}岁是个{1}".format(name,age,gender))
```
运行结果
```
我叫test今年10岁是个男
我叫男今年test岁是个10
```

## zip
zip() 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。
如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

```
a = [1,2,3,4,5]
b = ['a','b','c','d','e']
c = zip(a,b)
print(c)
print(type(c))
print(list(c))
```
运行结果
```
<zip object at 0x00000226F07124C8>
<class 'zip'>
[(1, 'a'), (2, 'b'), (3, 'c'), (4, 'd'), (5, 'e')]
```

## enumerate
```
seq = ['a','b','c','test']

print(type(enumerate(seq)))
print(enumerate(seq))
print(list(enumerate(seq)))

for index,value in enumerate(seq):
    print(index,value,sep='---')
```
运行结果
```
<class 'enumerate'>
<enumerate object at 0x000001708394E288>
[(0, 'a'), (1, 'b'), (2, 'c'), (3, 'test')]
0---a
1---b
2---c
3---test
```
## 装包，拆包
```
#装包
装包就是把未命名的参数放到元组中，把命名参数放到字典中

a = 1, 2
print(a)
(1, 2)

#拆包将一个结构中的数据拆分为多个单独变量中 *args **kwargs

def run1(*args):    # *args 相当于 a, b, c = args
    print(*args)    # 拆包 1， 2， 3
    print(args)     # 未拆包 （1， 2， 3）
    run2(*args)     # 将拆包数据传给run2
    run2(args)      # 将未拆包数据传给run2
 
 
def run2(*args):
    print(args)     # 打印包
    print(*args)    # 打印拆包后的数据
 
 
a, b, c = 1, 2, 3
run1(a, b, c)
 
1 2 3
(1, 2, 3)
(1, 2, 3)
1 2 3
((1, 2, 3),)
(1, 2, 3)
　　

```

## 闭包
请大家跟我理解一下，如果在一个函数的内部定义了另一个函数，外部的我们叫他外函数，内部的我们叫他内函数。
闭包： 
在一个外函数中定义了一个内函数，内函数里运用了外函数的临时变量，并且外函数的返回值是内函数的引用。这样就构成了一个闭包。
一般情况下，在我们认知当中，如果一个函数结束，函数的内部所有东西都会释放掉，还给内存，局部变量都会消失。但是闭包是一种特殊情况，如果外函数在结束的时候发现有自己的临时变量将来会在内部函数中用到，就把这个临时变量绑定给了内部函数，然后自己再结束。

```
#闭包函数的实例
# outer是外部函数 a和b都是外函数的临时变量
def outer( a ):
    b = 10
    # inner是内函数
    def inner():
        #在内函数中 用到了外函数的临时变量
        print(a+b)
    # 外函数的返回值是内函数的引用
    return inner
if __name__ == '__main__':
    # 在这里我们调用外函数传入参数5
    #此时外函数两个临时变量 a是5 b是10 ，并创建了内函数，然后把内函数的引用返回存给了demo
    # 外函数结束的时候发现内部函数将会用到自己的临时变量，这两个临时变量就不会释放，会绑定给这个内部函数
    demo = outer(5)
    # 我们调用内部函数，看一看内部函数是不是能使用外部函数的临时变量
    # demo存了外函数的返回值，也就是inner函数的引用，这里相当于执行inner函数
    demo() # 15
    demo2 = outer(7)
    demo2()#17
```

## 装饰器
**二层装饰器，也就是没有装饰器没有参数**

```
import time
def dec1(func):
    print("开始ing")
    time.sleep(3)

    def wrapper(*args,**kwargs):
        func(*args,**kwargs)
        print("到达装饰器里面")
    return wrapper()

@dec1
def f1():
    print("我是f1")

```
运行结果
```
开始ing
我是f1
到达装饰器里面
```
**三层装饰器，有参数的装饰器**
```
import time
def outer(a):
    def dec1(func):
        print("开始ing")
        time.sleep(3)

        def wrapper(*args,**kwargs):
            func(*args,**kwargs)
            print("到达装饰器里面",a)
        return wrapper()
    return dec1

@outer(10)
def f1():
    print("我是f1")

```
运行结果
```
开始ing
我是f1
到达装饰器里面 10
```

# 匿名函数
```

s = lambda a,b:a+b
print(s)
print(s(1,2))

#map+匿名函数
lists = [1,3,5,7,9]
result = map(lambda x:x*x,lists)
print(result)
print(list(result))

#if+匿名函数
func = lambda x:x if x==10 else x+1
print(func(10))
print(func(11))

func = lambda x:print("大于10") if x>10 else print("少于10")
print(func(11))
print(func(2))

#filter+匿名函数
result = list(filter(lambda x:x>5, lists))
print(result)


#reduce +匿名函数
from functools import reduce
# reduce 函数可以按照给定的方法把输入参数中上序列缩减为单个的值，具体的做法如下：
# 首先从序列中去除头两个元素并把它传递到那个二元函数中去，求出一个值，再把这个加到序列中循环求下一个值，直到最后一个值 。
result = reduce(lambda x,y:x*y, [1,2,3,4,5] )#((((1*2)*3)*4)*5
print(result)
```
运行结果
```
<function <lambda> at 0x00000285FDBC3E18>
3
<map object at 0x00000285FDF312B0>
[1, 9, 25, 49, 81]
10
12
大于10
None
少于10
None
[7, 9]
120

```
## 捕捉异常错误
```
try:
    open('cc')
except Exception as err:
    print(err)
```
运行结果
```
[Errno 2] No such file or directory: 'cc'
```

## 列表推导式

```
#[表达式 for i in lists ]
lists = ['tim','tom','cat','jacker']
result1 = [i for i in lists ]
print(result1)
result2 = [i+"test" for i in lists]
print(result2)

#[表达式 for i in lists if 判断]
lists2 = [1,3,5,7,9]
result3 = [x for x in lists2 if x>3]
print(result3)
result4 = [x*x for x in lists2 if x>3]
print(result4)
#if else
result5 = [x if x>5 else x+10 for x in lists2 ]
print(result5)
```
运行结果
```
['tim', 'tom', 'cat', 'jacker']
['timtest', 'tomtest', 'cattest', 'jackertest']
[5, 7, 9]
[25, 49, 81]
[11, 13, 15, 7, 9]
```

## 生成器
**小括号就是生成器**
```
g = (x*x for x in range(0,10))
print(type(g))
print(g)
print('-----------------------')
#通过调用__nexe__方法
print(g.__next__())
print(g.__next__())
print('-----------------------')
#调用next方法
print(next(g))
print(next(g))
print('-----------------------')
```
运行结果
```
<class 'generator'>
<generator object <genexpr> at 0x0000025418ED7F68>
-----------------------
0
1
-----------------------
4
9
```
yield是在函数里面，他就变成生成器

```

def func(n):
    while True:
        n = n+1
        yield n #return + 暂停

g = func(10)
print(g)
print(type(g))
print(next(g))
print(next(g))
print(next(g))
```
运行结果
```
<generator object func at 0x000001F0EDF57F68>
<class 'generator'>
11
12
13
```

## 类的call
Python中，如果在创建class的时候写了call（）方法， 那么该class实例化出实例后， 实例名()就是调用call（）方法。
```
class test():
    def __call__(self, *args, **kwargs):
        print("test")


t = test()
t()
```
运行结果
```
test
```


## classmethod
classmethod 修饰符对应的函数不需要实例化，不需要 self 参数，但第一个参数需要是表示自身类的 cls 参数，可以来调用类的属性，类的方法，实例化对象等。
**不需要实例化的意思是不创建对象，比如a = A()**
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class A(object):
    bar = 1
    def func1(self):  
        print ('foo') 
    @classmethod
    def func2(cls):
        print ('func2')
        print (cls.bar)
        cls().func1()   # 调用 foo 方法
 
A.func2()               # 不需要实例化
```
运行结果
```
func2
1
foo
```

## staticmethod
对比classmethod，staticmethod没有参数
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class C(object):
    @staticmethod
    def f():
        print('runoob');
 
C.f();          # 静态方法无需实例化
cobj = C()
cobj.f()        # 也可以实例化后调用
```
运行结果
```
runoob
runoob
```
## proprety
```
#普通的get和set私有化age，__的变量就是私有化
class Test():
    def __init__(self,name,age):
        self.name = name
        self.__age = age

    def getAge(self):
        return self.__age

    def setAge(self,age):
        self.__age = age


class Test1():
    def __init__(self,name,age):
        self.name = name
        self.__age = age

    @property
    def age(self):
        return self.__age

    #设置方法
    @age.setter
    def age(self,age):
        self.__age = age

t = Test("name",20)
print(t.getAge())
t.setAge(30)
print(t.getAge())

#简单来说，这装饰器的作用就是调用函数变成调用变量，从不能调用私有化变量，直接用一个变量代替
t1 = Test1("name",20)
print(t1.age)
t1.age = 30
print(t1.age)
```
运行结果
```
20
30
20
30
```
## 
```

```
运行结果
```

```


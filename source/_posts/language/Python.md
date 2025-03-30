---
title: Python
date: 2023-11-24
category:
  - Language 
tag:
  - python
sticky: false
---

# Python



## 基本语法

### Hello World
```python
#!/usr/bin/env python

# -*- encoding: utf-8 -*-

def print_hi(name: str, say: str = 'I am python'):
    hello: str = 'Hello'
    print(f'{hello}, {name}! \n{say}')


if __name__ == '__main__':
    print_hi('World')

    
[root@loacl ~]python3 run.py
Hello, World! 
I am python
```

### 变量
```python
age: int = 18  # 年龄是 int 类型
name: str = "John"  # 名字现在是 str 类型
print(name, age)

[root@loacl ~]python3 run.py
John 18
```

### 判断
```python
num: int = 200
if num > 0:
    print("num is greater than 0")
else:
    print("num is not greater than 0")
    
[root@loacl ~]python3 run.py
num is greater than 0
```

### 循环
```python
for item in range(6):
    if item == 3: break
    print(item)
else:
    print("Finally finished!")
```

### 算术
```python
result = 10 + 30 # => 40
result = 40 - 10 # => 30
result = 50 * 5  # => 250
result = 16 / 4  # => 4.0 (Float Division)
result = 16 // 4 # => 4 (Integer Division)
result = 25 % 2  # => 1
result = 5 ** 3  # => 125
```

### 文件读取
```python
with open("myfile.txt", "r", encoding='utf8') as file:
    for line in file:
        print(line)
```

### 函数
```python
def add(x, y):
    print("x is %s, y is %s" %(x, y))
    return x + y
add(5, 6)    # => 11

def swap(x, y):
    return y, x
x = 1
y = 2
x, y = swap(x, y)  # => x = 2, y = 1

def add(x, y=10):
    return x + y
add(5)      # => 15
add(5, 20)  # => 25

# => True
(lambda x: x > 2)(3)
# => 5
(lambda x, y: x ** 2 + y ** 2)(2, 1)
```
### 异常处理
```python
try:
    # 使用“raise”来引发错误
    raise IndexError("这是一个索引错误")
except IndexError as e:
    pass                 # pass只是一个空操作。 通常你会在这里做恢复。
except (TypeError, NameError):
    pass                 # 如果需要，可以一起处理多个异常。
else:                    # try/except 块的可选子句。 必须遵循除块之外的所有内容
    print("All good!")   # 仅当 try 中的代码未引发异常时运行
finally:                 # 在所有情况下执行
    print("我们可以在这里清理资源")
```


### 数据类型
|  类型   | 名称  |
|  ----  | ----  |
| str  | 文本／字符串（Text） |
| bool  | 布尔值／逻辑值（Boolean） |
| int, float, complex  | 数值（Numeric） |
| dict  | 映射／键值对（Mapping） |
| list, tuple, range  | 序列（Sequence） |
| set, frozenset  | 集合（Set） |
| bytes, bytearray,memoryview  | 二进制数据（Binary） |

#### 字符串
```python
hello = "Hello World"
hello = 'Hello World'
multi_string = """Multiline Strings
Lorem ipsum dolor sit amet,
consectetur adipiscing elit """

#切片
>>> msg = "Hello, World!"
>>> print(msg[2:5])
llo

#下标
>>> hello = "Hello, World"
>>> print(hello[1])  # 获取第二个字符
e
>>> print(hello[-1])  # 获取倒数第一个字符
d
>>> print(type(hello[-1]))  # 得到的还是字符串
<class 'str'>

#长度
>>> hello = "Hello, World!"
>>> print(len(hello))
13

#存在性判断
>>> s = 'spam'
>>> s in 'I saw spamalot!'
True
>>> s not in 'I saw The Holy Grail!'
True

#字符串拼接
>>> s = 'spam'
>>> t = 'egg'
>>> s + t  # 可以使用加号进行拼接
'spamegg'
>>> 'spam' 'egg'  # 两个字符串之间可以省略加号
'spamegg'

#格式化
name = "John"
print("Hello, %s!" % name)
name = "John"
age = 23
print("%s is %d years old." % (name, age))

format() 方法
txt1 = "My name is {fname}, I'm {age}".format(fname="John", age=36)
txt2 = "My name is {0}, I'm {1}".format("John", 36)
txt3 = "My name is {}, I'm {}".format("John", 36)
```

#### 列表
```python
#定义
list1 = ["apple", "banana", "cherry"]
list2 = [True, False, False]
list3 = [1, 5, 7, 9, 3]
list4 = list((1, 5, 7, 9, 3))

>>> li1 = []
>>> li1
[]
>>> li2 = [4, 5, 6]
>>> li2
[4, 5, 6]
>>> li3 = list((1, 2, 3))
>>> li3
[1, 2, 3]
>>> li4 = list(range(1, 11))
>>> li4
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

#添加
mylist = []
mylist.append(1)
mylist.append(2)
for item in mylist:
    print(item) # 打印输出 1,2
    
```
#### 元组
```
my_tuple = (1, 2, 3)
my_tuple = tuple((1, 2, 3))
#集合
set1 = {"a", "b", "c"}   
set2 = set(("a", "b", "c"))
```
####字典
```
>>> empty_dict = {}
>>> a = {"one": 1, "two": 2, "three": 3}
>>> a["one"]
1
>>> a.keys()
dict_keys(['one', 'two', 'three'])
>>> a.values()
dict_values([1, 2, 3])
>>> a.update({"four": 4})
>>> a.keys()
dict_keys(['one', 'two', 'three', 'four'])
>>> a['four']
4
```


### 类&继承
#### 定义
```python

class MyNewClass:
    pass
# 类的实例化
my = MyNewClass()
```
#### 构造函数
```
class Animal:
    def __init__(self, voice):
        self.voice = voice
 
cat = Animal('Meow')
print(cat.voice)    # => Meow
 
dog = Animal('Woof') 
print(dog.voice)    # => Woof
```

#### 方法
```
class Dog:
    # 类的方法
    def bark(self):
        print("Ham-Ham")
 
charlie = Dog()
charlie.bark()   # => "Ham-Ham"
```

#### 类变量
```
class MyClass:
    class_variable = "A class variable!"
# => 一个类变量！
print(MyClass.class_variable)
x = MyClass()
 
# => 一个类变量！
print(x.class_variable)
```

#### super函数
```
class ParentClass:
    def print_test(self):
        print("Parent Method")
 
class ChildClass(ParentClass):
    def print_test(self):
        print("Child Method")
        # 调用父级的 print_test()
        super().print_test() 
>>> child_instance = ChildClass()
>>> child_instance.print_test()
Child Method
Parent Method
```
#### repr方法
```
class Employee:
    def __init__(self, name):
        self.name = name
 
    def __repr__(self):
        return self.name
 
john = Employee('John')
print(john)  # => John
```
#### 多态
```
class ParentClass:
    def print_self(self):
        print('A')
 
class ChildClass(ParentClass):
    def print_self(self):
        print('B')
 
obj_A = ParentClass()
obj_B = ChildClass()
 
obj_A.print_self() # => A
obj_B.print_self() # => B
```

#### 重写
```
class ParentClass:
    def print_self(self):
        print("Parent")
 
class ChildClass(ParentClass):
    def print_self(self):
        print("Child")
 
child_instance = ChildClass()
child_instance.print_self() # => Child
```

#### 继承
```
class Animal: 
    def __init__(self, name, legs):
        self.name = name
        self.legs = legs
        
class Dog(Animal):
    def sound(self):
        print("Woof!")
 
Yoki = Dog("Yoki", 4)
print(Yoki.name) # => YOKI
print(Yoki.legs) # => 4
Yoki.sound()     # => Woof!
```
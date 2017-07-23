---
layout: post
title: "magic"
excerpt: "magic of python"
date: 2017-07-21 19:29
tag: [Python]
---

##  什么是魔法方法？

    　魔法方法就是可以给你的类增加魔力的特殊方法，如果你的对象实现（重载）了这些方法中的某一个，那么这个方法就会在特殊的情况下被 Python 所调用，你可以定义自己想要的行为，而这一切都是自动发生的。它们经常是两个下划线包围来命名的（比如 __init__，__lt__），Python的魔法方法是非常强大的，所以了解其使用方法也变得尤为重要！

__init__(self[, ...])，__new__(cls[, ...])，__del__(self)

　　__init__ 构造器，当一个实例被创建的时候初始化的方法。但是它并不是实例化调用的第一个方法，__new__才是实例化对象调用的第一个方法，它只取下 cls参数，并把其他参数传给 __init__。 __new__很少使用，但是也有它适合的场景，尤其是当类继承自一个像元组或者字符串这样不经常改变的类型的时候。

　　__new__ 使用时注意以下四点：

1. __new__ 是在一个对象实例化的时候所调用的第一个方法
2. 它的第一个参数是这个类，其他的参数是用来直接传递给 __init__ 方法
3. __new__ 决定是否要使用该 __init__ 方法，因为 __new__ 可以调用其他类的构造方法或者直接返回别的实例对象来作为本类的实例，如果 __new__ 没有返回实例对象，则 __init__ 不会被调用
4. __new__ 主要是用于继承一个不可变的类型比如一个 tuple 或者 string
5. __new__ return的是一个构建的实例
　　

　　__new__实现单例模式：

class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age
 
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls,'instance'):
            cls.instance = super(Person,cls).__new__(cls)
        return cls.instance
 
 
a = Person('p1',20)
b = Person('p2',21)
print(a == b)
单例作用：
第一、控制资源的使用，通过线程同步来控制资源的并发访问；
第二、控制实例产生的数量，达到节约资源的目的。
第三、作为通信媒介使用，也就是数据共享，它可以在不建立直接关联的条件下，让多个不相关的两个线程或者进程之间实现通信。
比如，数据库连接池的设计一般采用单例模式，数据库连接是一种数据库资源。
　　__del__ 析构器，当实例被销毁时调用

3、__call__(self[,args ...])，__getitem__(self,key)，__setitem__(self,key,value)

　　__call__ 允许一个类的实例像函数一样被调用

class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.instance = add
 
    def __call__(self,*args):
        return self.instance(*args)
 
def add(args):
    return args[0] + args[1]
 
a = Person('p1', 20)
print(a([1,2]))
#这里将打印 3
#可见当创建a这个对象之后，如果定义了__call__函数则对象是可以像函数一样调用的
　　__getitem__ 定义获取容器中指定元素的行为，相当于self[key]

class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self._registry = {
            'name': name,
            'age': age
        }
 
    def __call__(self, *args):
        return self.instance(*args)
 
    def __getitem__(self, key):
        if key not in self._registry.keys():
            raise Exception('Please registry the key:%s first !' % (key,))
        return self._registry[key]
 
a = Person('p1', 20)
print(a['name'],a['age'])
#这里打印的是 'p1' 20
#可见__getitem__使实例可以像字典一样访问
　　__setitem__ 设置容器中指定元素的行为，相当于self[key] = value 

4、__getattr__(self,name)，__getattribute__(self,name)，__setattr__(self,name,value)，__delattr__(self,name)，__get__(self,instance,owner)，__set__(self,instance,value)，__delete__(self,instance)

　　__getattr__ 定义当用户试图访问一个不存在属性的时候的行为

　　__setattr__ 定义当一个属性被设置的时候的行为

　　__getattribute__ 定义当一个属性被访问的时候的行为

　　

class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self._registry = {
            'name': name,
            'age': age
        }
 
    def __getattr__(self, item):
        print("don't have the attribute ",item)
        return False
 
    def __setattr__(self, key, value):
        self.__dict__[key] = value
 
    def __getattribute__(self, item):
        #注意此处不要用 self.__dict__[item]
        #因为self.__dict__依然会被__getattribute__拦截 这样就会陷入循环
        return object.__getattribute__(self,item)
 
a = Person('p1', 20)
print(a.hh)        #这里会打印 don't have the attribute hh 以及 False
a.hh = 'fdf'        #这里设置该属性值为'fdf'
print(a.hh)           #这里将打印出 fdf
　　__delattr__ 定义当一个属性被删除的时候的行为

　　__get__  定义当描述符的值被取得的时候的行为

　　__set__ 定义当描述符的值被设置的时候的行为

　　__delete__ 定义当描述符的值被删除的时候的行为

class Descriptor(object):
    def __init__(self):
        self.des = None
 
    def __get__(self, instance, owner):
        return instance.__dict__.get(self.des,None)
 
    def __set__(self, instance, value):
        instance.__dict__[self.des] = value
 
 
class Person(object):
    des = Descriptor() #这里的Descriptor就是一个描述符类
 
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self._registry = {
            'name': name,
            'age': age
        }
 
 
a = Person('p1', 20)
a.des = 10    #这里会调用Descriptor的__set__方法
print(a.des)   #这里会调用Descriptor的__get__方法

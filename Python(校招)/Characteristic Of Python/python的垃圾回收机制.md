## Python中的垃圾回收机制

​	现在的高级语言如java，c#等，都采用了垃圾收集机制，而不再是c，c++里用户自己管理维护内存的方式。自己管理内存极其自由，可以任意申请内存，但如同一把双刃剑，为大量内存泄露，悬空指针等bug埋下隐患。
对于一个字符串、列表、类甚至数值都是对象，且定位简单易用的语言，自然不会让用户去处理如何分配回收内存的问题。
​	python里也同java一样采用了垃圾收集机制，不过不一样的是:
**python采用的是引用计数机制为主，标记-清除和分代收集两种机制为辅的策略**

### 引用计数机制：

python里每一个东西都是对象，它们的核心就是一个结构体：

![1.PNG](https://i.loli.net/2019/08/15/Wj5H3ievkzsg2XL.png)

`PyObject`

```python
typedef struct_object {
 int ob_refcnt;
 struct_typeobject *ob_type;
} PyObject;
```

PyObject是每个对象必有的内容，其中`ob_refcnt`就是做为引用计数。当一个对象有新的引用时，它的`ob_refcnt`就会增加，当引用它的对象被删除，它的`ob_refcnt`就会减少，这个值就是来怕判断是否需要回收的，初始为1，一旦为零就会被回收

**优点**

1. 维护引用计数消耗资源
2. 循环引用

**缺点**

但是这种形式的回收可能会存在垃圾无法被回收的现象：

```python
list1 = []
list2 = []
list1.append(list2)
list2.append(list1)
```

这样，list1和list2相互引用，导致他们的引用计数为2，如果不存在对他们两个的引用，他们的值也会变成1，无法变成0，所以python不可能就这样让循环引用造成内存泄漏

### 具体见链接：

<https://www.jianshu.com/p/1e375fb40506>


# python 内置数据类型



## 内置数据类型种类

常见数据类型：

- 数字类型：int 、 float 、 complex
- 文本类型：str
- 序列类型：list 、 tuple 、 range
- 映射类型：dict

从 **非结构化** 数据到 **结构化** 数据  --  列表、元祖

从 **非结构化** 数据到 **半结构化** 数据  --  字典

![](.\images\python_003_001.png)



## 列表

列表的定义：

- 列表中可以存放一系列对象
- 存放的对象可以是 数字、字符串 也可以是 列表
- 列表用 **[]** 表示，每个对象称作列表的元素，元素之间用“,”分割
- 可以在定义之后进行修改，可以用索引访问

列表的创建：

- 使用一对方括号来表示空列表: `[]`
- 使用列表推导式: `[x for x in iterable]`
- 使用类型的构造器: `list()` 或 `list(iterable)`



#### 添加元素

- list.insert(索引， 元素) 在索引位置插入元素
- list.append(元素) 在列表结尾添加元素
- list.extend(可迭代对象*) 为列表扩展元素 

注意 append 和 extend 的区别

```python
# 添加元素
list_demo = ['a', 'b', 'c', 'd', 'e', ['f', 'g']]
print(type(list_demo))

list_demo.insert(0, 'first')
print(list_demo) # ['first', 'a', 'b', 'c', 'd', 'e', ['f', 'g']]

list_demo.insert(-1, 'last')
print(list_demo) # ['first', 'a', 'b', 'c', 'd', 'e', 'last', ['f', 'g']]

list_demo.append('last_one')
print(list_demo) # ['first', 'a', 'b', 'c', 'd', 'e', 'last', ['f', 'g'], 'last_one']

list_demo.extend('last_two')
print(list_demo)  # ['first', 'a', 'b', 'c', 'd', 'e', 'last', ['f', 'g'], 'last_one', 'l', 'a', 's', 't', '_', 't', 'w', 'o']

list_demo.append(['last_three', 'last_fore'])
print(list_demo) # ['first', 'a', 'b', 'c', 'd', 'e', 'last', ['f', 'g'], 'last_one', 'l', 'a', 's', 't', '_', 't', 'w', 'o', ['last_three', 'last_fore']]

list_demo.extend(['last_five', 'last_six'])
print(list_demo) # ['first', 'a', 'b', 'c', 'd', 'e', 'last', ['f', 'g'], 'last_one', 'l', 'a', 's', 't', '_', 't', 'w', 'o', ['last_three', 'last_fore'], 'last_five', 'last_six']
```



#### 修改元素

- list.remove(元素) 移除列表的元素
- list.reverse() 反转列表元素的顺序
- list.pop(索引) 移除索引对应的元素并返回改元素，不指定索引移除最后一个元素
- list.copy() 复制元素 - 是浅拷贝
- list.clear() 清空列表

```python
# 修改元素
list_demo2 = ['a', 'b', 'c', 'd', 'e', ['f', 'g']]

print(list_demo2.remove('a')) # None
print(list_demo2) # ['b', 'c', 'd', 'e', ['f', 'g']]

print(list_demo2.pop()) # ['f', 'g']
print(list_demo2) # ['b', 'c', 'd', 'e']

print(list_demo2.pop(0)) # b
print(list_demo2) # ['c', 'd', 'e']

print(list_demo2.reverse()) # None
print(list_demo2) # ['e', 'd', 'c']

# 注意 copy 是浅拷贝
list_demo3 = ['a', 'b', ['c', 'd']]
list_demo4 = list_demo3.copy()
print(list_demo4) # ['a', 'b', ['c', 'd']]
list_demo4[0] = 'first'
list_demo4[2][0] = 'two'
print(list_demo3) # ['a', 'b', ['two', 'd']]
print(list_demo4) # ['first', 'b', ['two', 'd']]

print(list_demo4.clear()) # None
print(list_demo4) # []
```



#### 计算列表长度

- len(list) 得到列表的长度
- len(list[0]) 得到列表中元素的长度
- list.count(元素) 得到元素出现的次数



#### 列表排序

- list.sort(key=None, reverse=False) 列表原地排序  - 列表自带方法
  - key - 指定一个函数，用于从每个元素中提取比较键
  - reverse - 排序顺序， False 升序， True 降序
- sorted(list) 列表排序后返回新的列表 - python 内置函数

```python
# 排序
list_demo5 = [1,2,3,4,5]
list_demo5.sort()
print(list_demo5) # [1, 2, 3, 4, 5]
list_demo5.sort(reverse=True)
print(list_demo5) # [5, 4, 3, 2, 1]

list_demo6 = ['bdca', 'dcba', 'abcd', 'cabd']
list_demo6.sort()
print(list_demo6) # ['abcd', 'bdca', 'cabd', 'dcba']
list_demo6.sort(key=lambda x: x[1])
print(list_demo6) # ['cabd', 'abcd', 'dcba', 'bdca']

# sorted() 返回新列表，不修改原列表
list_demo7 = [3, 1, 2, 5, 4]
list_demo8 = sorted(list_demo7)
print(list_demo7) # [3, 1, 2, 5, 4]
print(list_demo8) # [1, 2, 3, 4, 5]
```



## 元祖

元祖是不可变序列，创建之后不可修改。

与列表区别：

- 元祖创建之后不可修改，列表可修改。
- 元祖执行效率高，列表执行效率底。

创建元祖：

- 可以使用 圆括号 "()" 定义元祖
- 可以使用 tuple() 函数创建元祖
- 可以将 range()、列表、字符串 转换为元组

删除元祖：

- del



## 集合

[https://docs.python.org/zh-cn/3.14/library/stdtypes.html#set-types-set-frozenset](https://docs.python.org/zh-cn/3.14/library/stdtypes.html#set-types-set-frozenset)

集合类型包括 set 和 frozenset 两种对象；set 对象可变，frozenset 对象是不可变的。

创建集合：

- 使用花括号以逗号分割元素的方式： { ‘jack’, 'wilson' }
- 使用集合推导式：{c for c in 'abcdefg' if c not in 'abc' }
- 使用类型构造器：set()



## 字典

[https://docs.python.org/zh-cn/3.14/library/stdtypes.html#mapping-types-dict](https://docs.python.org/zh-cn/3.14/library/stdtypes.html#mapping-types-dict)

创建字典：

- 使用花括号"()"定义： {‘one’: 1, 'two': 2}
- 使用类型构造器：dict()
- 使用字典推导式： {x: x**2 for x in range(10)}

键不能重复，需要可哈希的类型，如列表、字典等，一般使用 字符串、元祖 做字典的键。

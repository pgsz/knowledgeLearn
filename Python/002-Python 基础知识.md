# 002-Python 基础知识



## 输入输出

**内置函数**：[https://docs.python.org/zh-cn/3.14/library/functions.html](https://docs.python.org/zh-cn/3.14/library/functions.html)

- **print()**  打印输出
- **input**  输入



## 临时存储数据

```python
# 跟 JavaScript 不一样，没有变量提升的说法
x = 123
print(x) # 123 

x = 456
print(x) # 456  

z = y = x
print(z) # 456
print(y) # 456

# 多个赋值
a,b = 1,2
print(a) # 1
print(b) # 2
```



## 字符串

#### 定义字符串可以使用：

1. **单引号 ' '**
2. **双引号 “ ”**
3. **三重引号 ''' ''' 或 ”“” “”“**  --  多行的时候可以使用



#### 字符串中使用变量

- 使用 f-strings ： **f"{ 变量名 }"**   （3.6 以上版本）

```python
x = 123
print(f"x的值是{x}")
```



#### 字符串基本操作

##### 一：成员运算

[https://docs.python.org/zh-cn/3.14/library/stdtypes.html#sequence-types-list-tuple-range](https://docs.python.org/zh-cn/3.14/library/stdtypes.html#sequence-types-list-tuple-range)

**x in s**
**x not in s**

```python
'x' in '123x456'  # True
print('x' in '123x456')  # True
'x' not in '123x456'  # False
```

##### 二：连接运算

**s + t**

**s * n**

##### 三：切片操作

**s[i]**  --  同 js 数组下标

**s[i:j]**  -- 获取字符串的 i（包含） 到 j（不包含）（类似： js 字符串的 substring 方法，注意：js 中 substr 是包含 j 的）； 

**s[i:j:k]**  --  获取字符串的 i（包含） 到 j（不包含）步长为 k 的字符串。

```python
# 这里面有很多用法
x = 'python'
x[0] # p
x[1:3] # yt
x[1:] # ython
x[::-1] # nohtyp
```

##### 四：常用方法

[https://docs.python.org/zh-cn/3.14/library/stdtypes.html#text-and-binary-sequence-type-methods-summary](https://docs.python.org/zh-cn/3.14/library/stdtypes.html#text-and-binary-sequence-type-methods-summary)

- **str.count(sub[,start[,end]])**  --  返回字符串 sub 在 [start, end] 范围内非重叠出现的次数
- **str.isalnum()**  --  如果字符串中的所有字符都是字母或数字，且至少有一个字符，那么返回 True，否则返回 False
- **str.isalpha()**  --  如果字符串中的所有字符都是字母，且至少有一个字符，那么返回 True，否则返回 False
- **str.join(*iterable*)**
- **str.split(sep=None, maxsplit=-1)**
- **str.startwith(prefix[, start[, end]])**
- 等等等等



## 数字类型

数字类型种类：**整数、浮点数和复数**

数字是由 数字字面值或内置函数与运算符的结果创建的

<img src=".\images\python_number_001.png"  />

- 不同类型之间可以转换，但是必须显示转换（js 可以隐示转换）
- 类型转换时需要考虑对象的内容，格式不正确会导致转换错误

```python
print(int(123.456))  # 123
print(float(123.456))  # 123.456
print(float(123))  # 123.0

print(type(123))  # <class 'int'>
print(type(123.456))  # <class 'float'>
print(type('123'))  # <class 'str'>
print(type(int('123')))  # <class 'int'>
print(type(int('123.456')))  # 报错：invalid literal for int() with base 10: '123.456'
```



## 字符串 与 数字 区分

**强类型**语言，数据类型不会因为运算等原因中途改变，必须**显示改变类型**。

**字符串用于处理文字，数字用于处理数学计算**

区分数据类型好处：

- 有代码提示
- 执行效率高
- 安全、出错率低

```python
100 + 200  # 300
'100' + '200'  # '100200'
100 + '200'  # 报错：TypeError: unsupported operand type(s) for +: 'int' and 'str'
456 > 1234  # False
'456' > '1234' # True  字符串比较不是比较数值大小，而是按字符逐个比较：逐个字符比较 Unicode 编码值
'2' > '10'  # True
len('456') > len('1234')  # False
```


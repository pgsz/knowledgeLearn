# Mock.js

## 文档

[Mock.js 文档](http://mockjs.com/)

[Mock.js gitHub](https://github.com/nuysoft/Mock)



## Vue 中使用

1：安装

```bash
npm install mockjs --save-dev
```

2：创建数据文件

```js
// mock/index.js
import Mock from 'mockjs';

// 模拟一个接口
Mock.mock('/api/data', 'get', {
  'list|1-10': [
    {
      'id|+1': 1,
      'name': '@cname',
      'age|18-60': 1
    }
  ]
});
```

3：根据环境加载数据

```js
// main.js
// 判断是否为开发环境
if (process.env.NODE_ENV === 'development') {
  // 引入 mock 数据
  require('../mock');
}
```



## 语法规范

语法规范包含两部分

1. 数据模板定义 DTD
2. 数据占位符定义 DPD



### 数据模板定义 DTD

**每个属性由 3 部分构成：属性名、生成规则、属性值**

```js
// 属性名  name
// 生成规则 rule
// 属性值  value
'name|rule': value
```

**注意**

- 属性名 和 生成规则 之间用 `|` 分割

- 生成规则 是可选的

- 生成规则 有 7 种格式

  1. `'name|min-max': value`
  2. `'name|count': value`
  3. `'name|min-max.dmin-dmax': value`
  4. `'name|min-max.dcount': value`
  5. `'name|count.dmin-dmax': value`
  6. `'name|count.dcount': value`
  7. `'name|+step': value`

- **生成规则 的含义需要依赖 属性值 才能确定**

- 属性值 可以含有 `@占位符`

- 属性值 还指定了最终值的 初始值和类型

  

**生成规则 属性值类型**

- 字符串 **String**
- 数字 **Number**
- 布尔型 **Boolean**
- 对象 **Object**
- 数组 **Array**
- 函数 **Function**
- 正则表达式 **RegEx**



### 数据占位符定义 DPD

占位符 在属性值中占据位置，不是最终的属性值；格式：

```
@占位符
@占位符(参数 [,参数])
```

**注意**

1. 用 `@` 来表示其后的字符串时 占位符
2. 占位符 引用的是 `Mock.Random` 中的方法
3. 通过 `Mock.Random.extend()` 来扩展自定义占位符
4. 占位符 可以引用 数据模板 中的属性
5. 占位符 会优先引用 数据模板中的属性
6. 占位符 支持 相对路径 和 绝对路径



| Type          | Method                                                       |
| ------------- | ------------------------------------------------------------ |
| Basic         | boolean, natural, integer, float, character, string, range, date, time, datetime, now |
| Image         | image, dataImage                                             |
| Color         | color                                                        |
| Text          | paragraph, sentence, word, title, cparagraph, csentence, cword, ctitle |
| Name          | first, last, name, cfirst, clast, cname                      |
| Web           | url, domain, email, ip, tld                                  |
| Address       | area, region                                                 |
| Helper        | capitalize, upper, lower, pick, shuffle                      |
| Miscellaneous | guid, id                                                     |



**Basic**

1. boolean(min?, max?, current?) -  随机的布尔值
2. natural(min?, max?) - 随机的自然数（大于等于0的整数）
3. integer(min?, max?) - 随机的整数（有正有负）
4. float(min?, max?, dmin?, dmax?) - 随机的浮点数（有小数点）
5. character(pool?) - 随机字符串 （pool: lower, upper, number, symbol）
6. string(pool?, minx?, max?) - 随机字符串
7. range(start?, stop?, step) - 整型数组（stop 不包含在返回值中）

**Date**

1. date(format?) - 随机的日期字符串
2. time(format?) - 随机的时间字符串
3. datetime(format?) - 随机的日期和时间字符串
4. now(unit?, format?) - 当前的日期和时间字符串

**Image**

1. image(size?, background?, foreground? format?, text?) - 随机的图片地址
2. dataImage(size?, text?) - 随机的 Base64 编码

**color**

1. color() - 随机颜色(#RRGGBB)
2. hex() - 随机颜色(#RRGGBB)
3. rgb() - 随机颜色(rgba(r,g,b))
4. rgba() - 随机颜色(rgba(r,g,b,a))
5. hsl() - 随机颜色(hsl(h,s,l))

**Text**

1. paragraph(min?, max?) - 随机生成一段文本
2. cparageaph(min?, max?) - 随机生成一段中文文本
3. sentence(min?, max?) - 随机生成一个句子
4. csentence(min?, max?) - 随机生成一个中文句子
5. word(min?, max?) - 随机生成一个单词
6. cword(pool?, min?, max?) - 随机生成一个汉字
7. title(min?, max?) - 随机生成一句标题
8. ctitle(min?, max?) - 随机生成一句中文标题

**Name**

1. first()
2. last()
3. name(middle?)
4. cfirst()
5. clast()
6. cname()

**Web**

1. url(protocol?, host?) - 随机生成一个 URL
2. protocol() - 随机生成一个 URL 协议
3. domain() - 随机生成一个域名
4. tld() - 随机生成一个顶级域名
5. email(domain?) - 随机生成一个邮件地址
6. ip() - 随机生成一个 IP 地址

**Address**

1. region() - 随机生成一个中国大区（华北）
2. province() - 随机生成一个中国省（直辖市、自治区、特别行政区）（湖南）
3. city(prefix?) - 随机生成一个中国市（布尔值，是否生成所属的省）（湖南省 长沙市）
4. conty(prefix?) - 随机生成一个中国县
5. zip() - 随机生成一个邮政编码（六位数）

**Helper**

1. capitalize(word) - 把字符串的第一个字母转为大写
2. upper(str) - 把字符串转换为大写
3. lower(str) - 把字符串转换为小写
4. pick(arr) - 从数组中随机选取一个元素并返回
5. shuffle(arr) - 打乱数组中元素顺序并返回

**Miscellaneous**

1. guid() - 随机生成一个 GUID
2. id() - 随机生成一个 18 位身份证
3. increment(step?) - 生成一个全局的自增整数
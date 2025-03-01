# Mockjs 源码

[源码 github](https://github.com/nuysoft/Mock)



## 目录结构

```
├── bin  ········································· 命令行工具脚本
|
|__ dist ········································· 打包压缩之后的 Mockjs 文件
│
├── src  ········································· 核心业务代码
│   │
│   ├── mock.js  ································· 入口文件
│   │
│   ├── handler.js  ······························ 处理数据模板
│   │
│   ├── parser.js   ······························ 解析数据模板（属性名）
│   │
│   ├── constant.js ·····························  常量集合
│   │
│   ├── util.js ·································· 工具集合
│   │
│   ├── random ··································· 工具类，生成各种随机数
│   │   ├── basic.js  ···························· basic
│   │   ├── text.js  ····························· text
│   │   ├── image.js  ····························· image
│   │   └── ......  ······························· 等等
│   │
│   ├── regexp ···································· 正则解析（属性值）
│   │
│   ├── schema ···································· toJSONSchema
│   │
│   ├── valid ····································· valid
│   │
│   └── xhr ········································ 自定义 XHR
│
|__ test ··········································· 单元测试
│
|__ package.json ··································· 等等
```



## mock.js  入口文件

入口文件：

定义 mock 的各类方法 ，如：mock、toJSONSchema、valid、setup 等

抛出 mock ，供外部使用

```js
/*
    * Mock.mock( template )
    * Mock.mock( function() )
    * Mock.mock( rurl, template )
    * Mock.mock( rurl, function(options) )
    * Mock.mock( rurl, rtype, template )
    * Mock.mock( rurl, rtype, function(options) )

    根据数据模板生成模拟数据。
*/
Mock.mock = function (rurl, rtype, template) {
    // Mock.mock(template)
    // 立即执行 - 返回结果
    if (arguments.length === 1) {
        return Handler.gen(rurl)
    }
    // Mock.mock(rurl, template)
    if (arguments.length === 2) {
        template = rtype
        rtype = undefined
    }
    // 拦截 XHR
    if (XHR) window.XMLHttpRequest = XHR
    Mock._mocked[rurl + (rtype || '')] = {
        rurl: rurl,
        rtype: rtype,
        template: template
    }
    // 有多个参数， 返回 Mock，后续调用时，返回结果
    return Mock
}	
```



## handler.js  处理数据模板

1. Handler.gen( template, name?, context? )   入口方法 
   1. 确定数据上下文
   2. 调用 Parser.parse(*name*) 解析 属性名 数据模板
   3. 根据 属性值 类型，调用 不同方法，进行解析操作
   4. 返回解析结果
2. 数据模板定义 DTD ：既根据不同 属性值 类型进行解析操作
   1.  Handler.array( options )   :  // 'name|1': []    // 'name|count': []    // 'name|min-max': []
   2. Handler.object( options )   :  // 'obj|min-max': {}    // 'obj': {}    注意：Mock.moc({}) 时初始化的时候 会进来一次
   3. Handler.number( options )  :  分为存在小数点和不存在小数点两种情形
   4. Handler.boolean( options )  ：  注意：其中对 数据占位符 DPD 的处理
   5. Handler.string( options )
   6.  Handler.function( options )
   7. Handler.regexp( options )
3. 处理数据占位符定义 DPD：处理占位符，根据 Random 方法处理对应关键字符
   1. Handler.placeholder( placeholder, context, templateContext, options )
   2. 根据正则解析 属性值，获取 @ 之后的字符
   3. 根据 / 拆解字符，解决 相对路径和绝对路径
   4. 解析占位符参数；如："float('60, 100, 3, 5')" => ['60', '100', '3', '5']
   5. 根据解析结果进行操作：
      1. 占位符优先引用数据模板中的属性
      2. 解析相对或绝对路径
      3. 判断是否是 Random 中的关键字，否就原路返回
      4. 是 则进入 Random 方法返回随机结果
4. 处理路径（相对和绝对）
   1. Handler.getValueByKeyPath( key, options )



## parser.js 解析数据模板（属性名）

通过正则解析和数据判断之后，将其返回

```js
Parser.parse( name )

{
    parameters: [ name, inc, range, decimal ],
    rnage: [ min , max ],

    min: min,
    max: max,
    count : count,

    decimal: decimal,
    dmin: dmin,
    dmax: dmax,
    dcount: dcount
}
```

1. 根据正则解析 **name**

   ```js
   /**
   	* name = 'number|1-10.1-2'
   	* parameters = ['number|1-10.1-2', 'number', undefined, '1-10', '1-2', index: 0, input: 'number|1-10.1-2', groups: undefined]
   */
   ```

2. 根据 **parameters** 解析其中的各类数据

3. 抛出 供 **Handler.gen** 使用



## Constant.js 常量集合

```js
	## 属性名、占位符 解析的正则
	/*
        RE_KEY
            'name|min-max': value
            'name|count': value
            'name|min-max.dmin-dmax': value
            'name|min-max.dcount': value
            'name|count.dmin-dmax': value
            'name|count.dcount': value
            'name|+step': value

            1 name, 2 step, 3 range [ min, max ], 4 drange [ dmin, dmax ]

        RE_PLACEHOLDER
            placeholder(*)
    */

	/**
     * 'number|1-10.1-2'
     * ['number|1-10.1-2', 'number', undefined, '1-10', '1-2', index: 0, input: 'number|1-10.1-2', groups: undefined]
     * 注意： 匹配字符： 没有 xxx| 时会失败，返回 null； 即 xxx、|xxx 都会失败
     * 1: (.+) 匹配任意字符（至少一个），并捕获第一个分组
     *    () 捕获符号
     *    . 元字符，匹配除换行符以外的任意单个字符
     *    + 量词，表示前面的元素可以出现一次或多次
     *    即匹配 name
     * 2：\| 匹配一个竖线符号 (|)
     * 3：?: 非捕获组
     * 4: \+(\d+) 匹配一个加号后跟一个或多个数字，并捕获数字部分
     *    即匹配 step   +123 => 匹配，捕获 123
     * 5：| 分支选择 或运算符
     * 6：([\+\-]?\d+-?[\+\-]?\d*)?
     *    [\+\-]? 可选的加号或减号字符
     *    \d+ 匹配一个或多个数字
     *    -? 可选的连字符 -
     *    即匹配 rang  min - max 并进行捕获
     * 7：(?:\.(\d+-?\d*))?)
     *    \. 匹配一个小数点
     *    (\d+-?\d*) 匹配一个或多个数字，可能包含 - 连字符， 并将其捕获到分组中
     *    即匹配 drange  dmin - dmax 并进行捕获 
     */
    RE_KEY: /(.+)\|(?:\+(\d+)|([\+\-]?\d+-?[\+\-]?\d*)?(?:\.(\d+-?\d*))?)/,
    RE_RANGE: /([\+\-]?\d+)-?([\+\-]?\d+)?/,
    /**
     * '@float(60, 100, 3, 5)'
     * ['@float(60, 100, 3, 5)', 'float', '60, 100, 3, 5', index: 0, input: '@float(60, 100, 3, 5)', groups: undefined]
     * 1: \\*@ 
     *    第一个 \ 是转义符
     *    匹配零个或多个 \
     *    匹配 @
     * 2：([^@#%&()\?\s]+)
     *    匹配除 @#%&()? 和 空白字符串之外的任何字符串，并进行捕获
     *    ^ 取反   
     *    \s 匹配任意一个空白字符： 空格、 制表符(\t)、换行符(\t)、换行符(\n)、回车符(\r)、垂直制表符(\v)、换页符(\f)
     *    \S 是 \s 的反向匹配，匹配一个或多个非空白字符
     * 3：(?:\((.*?)\))?
     *    匹配可能存在的用括号包裹的内容，并将括号内的内容捕获到一个捕获组中
    */
    RE_PLACEHOLDER: /\\*@([^@#%&()\?\s]+)(?:\((.*?)\))?/g

    /**
     * 一：正则贪婪与非贪婪区别：
     * 贪婪模式：
     *  会尽可能多的匹配字符，使用量词： *、 + 、{n, m}
     *  a.*b 在 aabab 中会匹配整个字符串
     *  适合匹配大块内容
     * 非贪婪模式
     *  会尽可能少的匹配字符，量词后面加 ? ： *? 、 +? 、 ?? 、 {n, m}?
     *  a.*b 在 aabab 中会匹配 aab 和 ab 两个结果
     *  适合精确匹配最小单元
     * 
     * 二：捕获与非捕获
     * 捕获组： () 对指定模式进行匹配，并将匹配内容保存
     * 非捕获组： (?:) 不会保存匹配到的内容
     * 
     * 三：量词区别
     * * 表示前面元素可以出现零次或多次
     * ? 
     *  普通量词：前面元素可以出现零次或一次，该元素是可选的
     *  跟在其他量词后面，将贪婪匹配变成非贪婪匹配;
     * + 表示前面元素可以出现一次或多次，即该元素至少要出现一次； /a+/
     * {n} 表示前面的元素正好出现 n 次；/a{3}/：正好匹配三个 a
     * {n,} 表示前面元素至少出现 n 次； /a{3,}/：至少匹配 三个或更多 a
     * {n,m} 表示前面的元素出现的次数在 n 到 m 间（包含 n 和 m ）
     * 
     * 四：^ 作用：
     * 1： 在正则表达式开头表示字符串起始位置:  /^hellow/ 匹配以 hello 开头
     * 2： 在方括号 [] 内开头位置表示否定字符串，注意：在 [] 内部且是第一个字符串才表示取反；
     *     该字符类会匹配 除了 方括号内指定字符串之外的任意单个字符串
     *    /[^0-9]/: 匹配字符串中除了数字以外的字符串  '12345': false(只有数字)  '123xy': true  'xynm': true
     * 3： 在多行模式下匹配行起始位置； /^hello/m   'say hello\nhello word' 匹配第二行开头的 'hello'
     * 
     * 五：正则 match 与 exec 的区别
     * 1：所属对象： string.match(reg)   reg.exec(string)
     * 2：返回值： 
     *    match：正则表达式没有 g 标志，返回数组，包含匹配结果和捕获组信息
     *           有 g 标志，返回数组，包含所有匹配结果，不包含捕获组信息
     *    exec： 无论是否有 g 标志，都返回数组，包含匹配结果和捕获组信息
     * 3：全局匹配：
     *    str = "123-abc 456-def"   regex = /(\d+)-(\w+)/g
     *    match：一次性返回所有结果，性能相对较高
     *          ['123-abc', '456-def']
     *    exec：每次调用返回一个结果，性能相对稍低
     *          regex.exec(str): ['123-abc', '123', 'abc']
     *          regex.exec(str): ['456-def', '456', 'def']
     * 
     * 六：\s 与 \S 的区别
     * \s 匹配任意一个空白字符： 空格、 制表符(\t)、换行符(\t)、换行符(\n)、回车符(\r)、垂直制表符(\v)、换页符(\f)
     *  \S 是 \s 的反向匹配，匹配一个或多个非空白字符
     * 
     * 七： \w 与 \W 的区别
     * \w 匹配单词字符，包含：任何字母(a-z,A-Z)、数字(0-9)或下划线(_)  等价于 [a-zA-Z0-9_]
     * \W 是 \w 的反向匹配，匹配非单词字符；包含：标点符号（！、@、#等）、空格、特殊字符（-、+、*等）
    */
```



## util.js

工具集合，定义一些方法供内部使用；同时也抛出绑定在 Mock.Util 上

如：extend、type、isObjectOrArray 等



## xhr 

模拟原生的 XMLHttpRequest 对象，主要目的是拦截和模拟 Ajax 请求。

1. 对自定义事件创建方式的兼容性处理，以确保在不同浏览器环境下都能正常创建自定义事件

   ```js
   /* <button id="myButton">触发自定义事件</button>
   <script>
       // 创建自定义事件，事件类型为 'mySpecialEvent'
       const myEvent = new Event('mySpecialEvent');
   
       // 监听自定义事件
       document.addEventListener('mySpecialEvent', function () {
           console.log('自定义事件 mySpecialEvent 已触发');
       });
   
       // 获取按钮元素
       const button = document.getElementById('myButton');
   
       // 点击按钮时触发自定义事件
       button.addEventListener('click', function () {
         document.dispatchEvent(myEvent);
       });
   </script> */
   try {
       /**
        * 现代浏览器支持的创建自定义事件
        * Event 和 CustomEvent 区别：
        * Event 不支持传递自定义数据， CustomEvent 更好 支持
        * Event 兼容性相对于 CustomEvent 更好
       */
       new window.Event('custom')
   } catch (exception) {
       window.Event = function (type, bubbles, cancelable, detail) {
           // tag: 自定义事件： CustomEvent 是必须，不能随意更改，是 浏览器 API 规定的字符串
           var event = document.createEvent('CustomEvent') // MUST be 'CustomEvent'
           // 初始化自定义事件
           // type：事件名称； bubbles：是否冒泡 默认 false
           // cancelable 事件是否可以被取消 默认 false   detail：传递自定义数据
           event.initCustomEvent(type, bubbles, cancelable, detail)
           return event
       }
   }
   ```

2. 定义 XHR 的状态、事件和属性

   ```js
   // 定义 XHR 状态
   var XHR_STATES = {
       // XMLHttpRequest 对象已经创建，但 open() 方法还未被调用，请求尚未初始化。
       UNSENT: 0,
       // open() 方法已经被调用，请求已经初始化，但 send() 方法还未被调用，请求还未发送。
       OPENED: 1,
       // send() 方法已经被调用，并且已经接收到了服务器的响应头。
       HEADERS_RECEIVED: 2,
       // 正在接收服务器的响应数据，此时 responseText 属性可能已经包含了部分数据。
       LOADING: 3,
       // 请求已经完成，响应数据已经全部接收完毕。此时可以根据 status 属性判断请求是否成功。
       DONE: 4
   }
   // 定义XHR事件
   /**
    * 1：readystatechange : 跟踪请求的不同状态变，分为 0、1、2、3、4
    * 2：loadstart ：当调用 XMLHttpRequest 对象的 send() 方法后，请求开始建立连接并发送数据，此时会触发 loadstart 事件
    * 3：progress ：在 loadstart 之后，可能会多次触发，直到请求完成。
    *              在数据传输过程中，只要有新的数据块到达，就会触发 progress 事件。这个事件通常用于显示进度条等场景，让用户了解数据传输的进度。
    * 4：abort ：在 progress 过程中，如果调用 XMLHttpRequest 对象的 abort() 方法中断请求，会触发该事件。
    *            当用户主动取消请求，或者代码中调用 abort() 方法时，请求会被中断，此时触发 abort 事件。
    * 5：error ： 在请求过程中出现错误（如无法连接到服务器、DNS 解析失败等）时，导致请求无法正常进行时触发。
    * 6：load ：请求正常完成，服务器返回了响应数据，此时触发 load 事件。
    * 7：timeout ：请求超过 timeout 属性设置的时间仍未完成时，会触发 timeout 事件（异步请求时才有效果）
    *              即 open(method, url, async(默认是 true))
    * 8：loadend ： 无论请求是成功完成（load）、被取消（abort）、发生错误（error）还是超时（timeout），最后都会触发 loadend 事件。
    *               表示请求的整个生命周期结束，可用于进行一些清理工作。
    *  顺序： loadstart -> progress -> (abort | error | timeout | load) -> loadend
    *        progress  会多次触发
   */
   var XHR_EVENTS = 'readystatechange loadstart progress abort error load timeout loadend'.split(' ')
   // 请求属性  withCredentials： 是否携带跨域凭证
   var XHR_REQUEST_PROPERTIES = 'timeout withCredentials'.split(' ')
   // 响应属性
   var XHR_RESPONSE_PROPERTIES = 'readyState responseURL status statusText responseType response responseText responseXML'.split(' ')
   ```

3. 定义 MockXMLHttpRequest 和 setup 方法

4. 初始化 Request 相关的属性和方法

   1. open()  初始化请求；拦截和处理 HTTP 请求
      1. 查找与请求参数匹配的数据模板
      2. 未找到匹配的数据模板，则采用原生 XHR 发送请求
         1. 创建原生 XHR 对象
         2. 初始化事件，监听原生 XHR 对象的事件；将原生 XHR 的响应属性 同步到 MockXMLHttpRequest ，并让 MockXMLHttpRequest 触发相应事件
         3. 调用 原生 xhr.open() 方法
         4. 将 MockXMLHttpRequest 的请求属性同步到 NativeXMLHttpRequest 中
      3. 找到了匹配的数据模板，开始拦截 XHR 请求
   2. send()  发送数据
      1. 拦截 XHR 请求；dispatchEvent 各类事件和状态
   3. abort()  取消 XHR



## random

工具类，用于生成各种随机数据

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



## 整体流程

Mock.mock  =>  Handler.gen => Parser.parse( name ) => Constant.js  => DTD => DPD => DATA
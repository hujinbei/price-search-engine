![00cd73960d018f7c20bb54f364f33cb2.png](en-resource://database/418:2)
## 1. 基础知识
#### （1）. [JSON](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499490767fe5a0e31e17e44b69dcd1196f7ec6fc6000)
 JSON是JavaScript Object Notation的缩写，是一种交换格式。
 数据类型：
 * number：和JavaScript的number一样
 * boolean:JavaScript的true和false
 * string:JavaScript的string
 * null:JavaScript的null
 * array:JavaScript的array表示方式——[]
 * object:JavaScript的{...}表示方式
    
#### （2）. [xPath](https://zhuanlan.zhihu.com/p/29436838)
*  xPath是XML Path Language,是一门XML路径语言，用于在XMl/HTML文档中查找信息的语言，做爬虫时用于抽取相应信息。
* **xPath常用规则：**
    * nodename 选取此节点的所有子节点
    * / 从当前节点选取子节点
    * // 从当前节点选取子孙节点
    * . 选取当前节点
    * .. 选取当前节点的父节点
    * @ 选取属性
    例如：//title[@lang='eng']  表示选择所有名称为title，同时属性lang的值为eng的节点

* LXML库中的etree模块可对HTML文本进行修正（补全标签等），tostring()方法可输出修正后的代码（bytes类型），decode()方法可转成str类型。

例如：**text()直接读取文本文件解析，获取节点内部文本**
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParse())
result = etree.tostring(html)
print(result.decode('utf-8'))

```
其中test.html的内容为
```
<div>
    <ul>
        <li class="item-0">< a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
     </ul>
 </div>
```
* **选取所有节点**
result = html.xpath('//\*')
    其中 \* 代表匹配所有节点
    
    * **获取 li 节点**
result = html.xpath('//li')
print(result[0])    
    ```取出其中一个对象用括号加索引```   
选取所有li节点用 // 加上节点名称，提取结果的每一个元素都是Element对象

* **子节点**
    * 通过添加 /a 即选择了所有li节点的所有a子节点或 //li 选中所有li节点，二者结合在一起获取所有li节点中的所有直接a子节点

```
result = html.xpath('//li/a')
```

* **子孙节点**

    * 获取ul节点下的所有子孙a节点

    ```
    result = html.xpath('//ul//a')
    ```
 
 * **父节点**
    * 选中href是link4.html的a节点，然后获取其父节点和class属性

```   result=html.xpath('//a[@href="http://ask.hellobi.com/link4.html"]/../@class')
```

或使用parent::获取父节点
```
result=html.xpath('//a[@href="http://ask.hellobi.com/link4.html"]/parent::* /@class')
```

* **属性获取**
    * 使用@符号获取节点属性
    获取li节点下所有a节点的href属性,以列表形式返回
   ```
    from lxml import etree
    html=etree.parse('./test.html',etree.HTMLParser())
    result = html.xpath('//li/a/@href')
    print(result)
   ```
* **属性多值匹配**
   通过contains()函数，第一个参数传入属性名称，第二个参数传入属性值
 ```
 result=html.xpath('//li[contains(@class,"li")]/a/text()'）
 ```
    
* **多属性匹配**
    使用and运算符连接多个属性
 ``` 
result=html.xpath('//li[contains(@class,"li") and @name="item"]/a/text()')
```
* **运算符描述实例返回值：**
   * or 或 A=9.80 or A=9.70 二者满足其一返回true，都不满足返回false
   * and 与 A>9.00 and A<9.90 满足条件返回true，不满足返回false
   * mod 计算除法余数
   * \ 计算两个节点集 //book \ //cd 返回所有拥有 book和cd元素的节点集
   * \+ 加法， \- 减法，\* 乘法，div 除法  
   
* **使用中括号传入索引获取特定次序的节点**
    ```
   result = html.xpath('//li[1]/a/text()')
   //选取第一个li节点
   result = html.xpath('//li[last()]/a/text()')
   //选取最后一个li节点
   result=html.xpath(//li[position()>3]/a/text()')
   //选取大于3的li节点
   ```
* **节点轴选择**
    * XPath Axes： 获取子元素、兄弟元素、父元素、祖先元素等
```
result = html.xpath('//li[1]/ancestor::* ')
//调用ancestor轴获取所有祖先节点，其后跟两个冒号，添加节点选择器,\*选取所有节点，div选取div节点;

result = html.xpath(//li[1]/attribute::* ')
//调用attribute轴获取所有属性值;

result=html.xpath('//li[1]/chid::a[href="http://ask.hellobi.com/link1.html"]')
//调用child轴获取所有直接子节点，选取href属性为link1.html的a子节点

result=html.xpath('//i[1]/descendant::span')
//调用descendant轴获取所有子孙节点中span节点

resul = html.xpath('//li[1]/following::* [2]')
//调用following轴获取节点之后所有节点，只获取a节点

result = html.xpath('//li[1]/following-sibling::* ')
//调用following-sibling轴，获取节点之后的所有同级节点
```

#### （3）. Requests库
* **安装requests库**
```
pip intall requests 
easy_install requests
```
* 在pycham中运行后出现"No module named 'requests'" 错误提示，没有requests模块
    * 点击File-settings-Project：untitled-Project Interpreter
    ![cb7021c6df80d4954f28e613cb8180c4.png](en-resource://database/412:1)
    * 选择正确的python安装地址(根据个人的安装地址)，设置成功后即可正确运行
 ![e80af7f6db62e24683ce4a507be1eccb.png](en-resource://database/414:1)
 
 * 输入代码
 ```python
 import requests
 r=requests.get('http://cuiqingcai.com')
 print(type(r))
 print(r.encoding)
 print(r.cookies)
 ```
 
运行结果如下：
 ![71f52be1ab182c5b81f0939d1a4290d0.png](en-resource://database/416:1)
 
* requests库提供http所有基本方式：
```python
r=requests.post("http://httpbin.org/post")
r=requests.put("http://httpbin.org/put"）
r=requests.delete("http://httpbin.org/delete")
r=requests.head("http://httpbin.org/get")
r=requests.options("http://httpbin.org/get")
```
* **基本GET请求**
```
r=requests.get("http://httpbin.org/get")
```
params添加参数
```python
import requests
payload={"key1":"value1","key2","value2"}
r=requests.get("http://httpbin.org/get",params=payload)
print(r.url)
```
运行结果如下：
![00cd73960d018f7c20bb54f364f33cb2.png](en-resource://database/418:2)

* **POST请求**
    * 添加data参数，服务器返回数据
```python
import requests
payload={'key1':'value1','key2':'value2'}
r=requests.post("http://httpbin.org//post",data=payload)
print(r.text)
```
运行结果：
```
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "key1": "value1", 
    "key2": "value2"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Connection": "close", 
    "Content-Length": "23", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.21.0"
  }, 
  "json": null, 
  "origin": "36.152.26.203", 
  "url": "http://httpbin.org/post"
}

```
* 将表单形式数据更改为JSON格式数据，利用json.dumps()将表单数据序列化
```python
import json
import requests
url='http://httpbin.org/post'
payload={'some':'data'}
r=requests.post(url,data=json.dumps(payload))
print(r.text) 
```
运行结果：
```
{
  "args": {}, 
  "data": "{\"some\": \"data\"}", 
  "files": {}, 
  "form": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Connection": "close", 
    "Content-Length": "16", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.21.0"
  }, 
  "json": {
    "some": "data"
  }, 
  "origin": "42.245.203.133", 
  "url": "http://httpbin.org/post"
}
```
*  **cookie**
    * 利用cookies变量向服务器发送cookies学信息
    
```python
import requests
url='http://httpbin.org/cookies'
cookies=dict(cookies_are='worikng'
r=requests.get(url,cookies=cookies)
print(r.text)
```
运行结果：
```
{
  "cookies": {
    "cookies_are": "working"
  }
}
```
* **超时配置**
    * timeout变量配置最大请求时间，timeout只对连接过程有效，对响应体的下载无效
```
requests.get('http://github.com',timeout0.001)
```


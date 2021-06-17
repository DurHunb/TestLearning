# Selenium简介&原理

Selenium 是一个 Web 应用的自动化框架。

通过它，我们可以写出自动化程序，像人一样在浏览器里操作web界面。 

- 比如点击界面按钮，在文本框中输入文字 等操作。

- 比如获取12306票务信息，招聘网站职位信息，财经网站股票价格信息 等等，然后用程序进行分析处理。

---

我们写的自动化程序 需要使用 **客户端库**。

我们程序的自动化请求都是通过这个库里面的编程接口发送给浏览器。

比如，我们要模拟用户点击界面按钮， 自动化程序里面就应该 调用客户端库相应的函数， 就会发送 **点击元素** 的请求给 下方的 **浏览器驱动**。 然后，浏览器驱动再转发这个请求给浏览器。

这个自动化程序发送给浏览器驱动的请求 是HTTP请求。

---

**浏览器驱动** 也是一个独立的程序，是由浏览器厂商提供的， 不同的浏览器需要不同的浏览器驱动。 比如 Chrome浏览器和 火狐浏览器有 各自不同的驱动程序。

浏览器驱动接收到我们的自动化程序发送的界面操作请求后，会转发请求给浏览器， 让浏览器去执行对应的自动化操作。

浏览器执行完操作后，会将自动化的**结果**返回给浏览器驱动， 浏览器驱动再通过HTTP响应的消息返回给我们的自动化程序的客户端库。

自动化程序的客户端库 接收到响应后，将结果转化为 `数据对象` 返回给 我们的代码。

我们的程序就可以知道这次自动化操作的结果如何了。

---

selenium 自动化流程如下：

1. 自动化程序调用Selenium 客户端库函数（比如点击按钮元素）

2. 客户端库会发送Selenium 命令 给浏览器的驱动程序

3. 浏览器驱动程序接收到命令后 ,驱动浏览器去执行命令

4. 浏览器执行命令

5. 浏览器驱动程序获取命令执行的结果，返回给我们自动化程序

6. 自动化程序对返回结果进行处理



# Selenium安装&示例

**安装客户端库** 	`pip install selenium`

**安装浏览器驱动**

​	不同的浏览器 需要选择不同的浏览器驱动。注意浏览器驱动 必须要和浏览器版本匹配。

​	目前主流的浏览器中， Chrome 浏览器对Selenium自动化的支持更加成熟一些。

​	[Chrome 浏览器驱动下载地址](https://chromedriver.storage.googleapis.com/index.html)

---


### 示例：打开百度网址

```python
from selenium import webdriver

# 创建 WebDriver 对象，指明使用chrome浏览器驱动
wd = webdriver.Chrome(r'D:\ggplugin\chromedriver.exe')

# 调用WebDriver 对象的get方法 可以让浏览器打开指定网址
wd.get('https://www.baidu.com')
```

执行完自动化代码，如果想关闭浏览器窗口可以调用WebDriver对象的 quit 方法，`wd.quit()`

---



### 将浏览器驱动目录加入环境变量Path

前面，代码创建 WebDriver对象时，需要指定浏览器驱动路径，比如

```python
wd = webdriver.Chrome(r'D:\ggplugin\chromedriver.exe')
```

如果，我们把浏览器驱动 `所在目录` 加入环境变量 Path， 再写代码时，就可以无需指定浏览器驱动路径了，

像下面这样

```py
wd = webdriver.Chrome()
```

因为，Selenium会自动在环境变量 Path 指定的那些目录里查找名为chromedriver.exe 的文件。

> 一定要注意的是， 加入环境变量 Path 的路径，
>
> 不是浏览器驱动全路径，比如 `d:\webdrivers\chromedriver.exe`
>
> 而是 浏览器驱动所在目录，比如 `d:\webdrivers`

​	

# Web自动化重点——找到元素

## 前置知识

### 1、WebDriver对象&WebElement对象

```python
from selenium import webdriver

# 创建 WebDriver 对象，指明使用chrome浏览器驱动
wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

# 调用WebDriver 对象的get方法 可以让浏览器打开指定网址
wd.get('https://www.baidu.com')

# 根据id选择元素，返回的就是该元素对应的WebElement对象
element = wd.find_element_by_id('kw')

# 通过该 WebElement对象，就可以对页面元素进行操作了
# 比如输入字符串到 这个 输入框里
element.send_keys('白月黑羽\n')
```

其中

```py
wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')
```

driver赋值的是 WebDriver 类型的对象，我们可以通过这个对象来操控浏览器。

使用了 WebDriver 对象 的方法 find_element_by_id，

```python
wd.find_element_by_id('kw')
```

这行代码运行是，就会发起一个请求通过 浏览器驱动 转发给浏览器，告诉它，需要选择一个id为 kw 的元素。

浏览器，找到id为kw的元素后，将结果通过 浏览器驱动 返回给 自动化程序， 所以 find_element_by_id 方法会 返回一个 **WebElement 类型的对象**。

这个WebElement 对象可以看成是对应 页面元素 的遥控器。

我们通过这个WebElement对象，就可以 `操控` 对应的界面元素。

比如 ：

调用这个对象的 `send_keys` 方法就可以在对应的元素中 输入字符串，

调用这个对象的 `click` 方法就可以 **点击** 该元素。



### 2、find_element 和 find_elements 的区别

使用 `find_elements` 选择的是符合条件的 `所有` 元素， 如果没有符合条件的元素， `返回空列表`

使用 `find_element` 选择的是符合条件的 `第一个` 元素， 如果没有符合条件的元素， `抛出 NoSuchElementException 异常`



---

### 3. 通过WebElement对象选择元素



不仅 WebDriver对象有 选择元素 的方法， WebElement对象 也有选择元素的方法。



WebDriver 对象 选择元素的范围是 **整个 web页面**， 而

WebElement 对象 选择元素的范围是 **该元素的内部**。

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

element = wd.find_element_by_id('container')

# 限制 选择元素的范围是 id 为 container 元素的内部。
spans = element.find_elements_by_tag_name('span')
for span in spans:
    print(span.text)
```

输出结果就只有

```python
内层11
内层12
内层21
```



---

### 4.  等待界面元素出现



在我们进行网页操作的时候， 有的元素内容不是可以立即出现的， 可能会等待一段时间。

比如 百度搜索一个词语， 我们点击搜索后， 浏览器需要把这个搜索请求发送给百度服务器， 百度服务器进行处理后，把搜索结果返回给我们。

所以，从点击搜索到得到结果，需要一定的时间，

只是通常百度服务器的处理比较快，我们感觉好像是立即出现了搜索结果。

百度搜索的每个结果 对应的界面元素 其ID 分别是数字 1， 2 ，3， 4 。。。

如下

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_38163256-b8ca567a-3523-11e8-9ec7-ce4a667c76ad.png)

那么我们可以试试用如下代码 来将 第一个搜索结果里面的文本内容 打印出来

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('https://www.baidu.com')

element = wd.find_element_by_id('kw')

element.send_keys('白月黑羽\n')

# id 为 1 的元素 就是第一个搜索结果
element = wd.find_element_by_id('1')

# 打印出 第一个搜索结果的文本字符串
print (element.text)
```

运行后会发现有如下异常抛出

```python
selenium.common.exceptions.NoSuchElementException: Message: no such element: Unable to locate element: {"method":"id","selector":"1"}
```

`NoSuchElementException` 的意思就是在当前的网页上 找不到该元素， 就是找不到 id 为 1 的元素。



这是因为代码执行的速度比 百度服务器响应的速度 快。

百度还没有来得及 返回搜索结果，我们就执行了如下代码

```python
element = wd.find_element_by_id('1')
```

在那短暂的瞬间， 网页上是没有用 id为1的元素的 （因为还没有搜索结果呢）。自然就会报告错误 id为1 的元素不存在了。

解决方法：

- 点击搜索后， 用sleep 来 等待几秒钟， 等百度服务器返回结果后，再去选择 id 为1 的元素， 就像下面这样

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('https://www.baidu.com')

element = wd.find_element_by_id('kw')

element.send_keys('黑羽魔巫宗\n')

# 等待 2 秒
from time import sleep
sleep(2)

# 2 秒 过后，再去搜索
element = wd.find_element_by_id('1')

# 打印出 第一个搜索结果的文本字符串
print (element.text)
```

但是这样的方法 有个很大的问题，就是：设置等待多长时间合适呢？



> Selenium提供了一个更合理的解决方案：
>
> 当发现元素没有找到的时候， 并不立即返回 找不到元素的错误。
>
> 而是周期性（每隔半秒钟）重新寻找该元素，直到该元素找到，
>
> 或者超出指定最大等待时长，这时才 抛出异常（如果是 `find_elements` 之类的方法， 则是返回空列表）。
>
> Selenium 的 Webdriver 对象 有个方法叫 `implicitly_wait`
>
> 该方法接受一个参数， 用来指定`最大等待时长`。



如果我们 加入如下代码

```python
wd.implicitly_wait(10)
```

那么后续所有的 `find_element` 或者 `find_elements` 之类的方法调用 都会采用上面的策略：

如果找不到元素， 每隔 ==半秒钟==再去界面上查看一次， 直到找到该元素， 或者 过了10秒 最大时长。

这样，我们的百度搜索的例子的最终代码如下

```python
from selenium import webdriver

wd = webdriver.Chrome()

# 设置最大等待时长为 10秒
wd.implicitly_wait(10)

wd.get('https://www.baidu.com')

element = wd.find_element_by_id('kw')

element.send_keys('黑羽魔巫宗\n')

element = wd.find_element_by_id('1')

print (element.text)
```

那么是不是有了implicitwait， 可以彻底不用sleep了呢？

不是的，有的时候等待元素出现，仍然需要sleep。







## 一、BY方式定位



使用selenium的By方式定位，首先得导入该模块

```python
from selenium.webdriver.common.by import By
```



**id属性定位**

```python
driver.find_element(By.ID,"id")
```

**name属性定位**

```python
driver.find_element(By.NAME,"name")
```

**classname属性定位**

```python
driver.find_element(By.CLASS_NAME,"claname")
```

**a标签文本属性定位**

只能使用精准的匹配（a标签的全部文本内容）

```python
driver.find_element(By.LINK_TEXT,"text")
```

**a标签部分文本属性定位**

可以使用精准或模糊匹配，如果使用模糊匹配最好能使用可以唯一关键字；

```python
driver.find_element(By.PARTIAL_LINK_TEXT,"partailtext")
```

**标签名定位**

```python 
driver.find_elemnt(By.TAG_NAME,"input")
```

**xpath路径定位**

```python
driver.find_element(By.XPATH,"//div[@name='name']")
```

**css选择器定位**

```python
driver.find_element(By.CSS_SELECTOR,"#id")	
```









## 二、`CSS`表达式



通过 `CSS` Selector 选择单个元素的方法是

```python
find_element_by_css_selector(`CSS` Selector参数)
```

选择所有元素的方法是

```python
find_elements_by_css_selector(`CSS` Selector参数)
```





###  1、根据 tag名、id、class 选择元素





根据 tag名 选择元素的 `CSS` Selector 语法非常简单，直接写上tag名即可，

比如 要选择 所有的tag名为div的元素，就可以是这样

```python
elements = wd.find_element(By.CSS_SELECTOR,'div')
```



------



根据id属性 选择元素的语法是在id号前面加上一个井号： `#id值`

比如 有下面这样的元素：

```html
<input  type="text" id='searchtext' />
```

就可以使用 `#searchtext` 来选择它。

比如，我们想在 `id 为 searchtext` 的输入框中输入文本 `你好` ，完整的Python代码如下

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

element = wd.find_element(By.CSS_SELECTOR,"#searchtext")	
element.send_keys('你好')
```



------



根据`class`属性 选择元素的语法是在 class 值 前面加上一个点： `.class值`

比如 要选择**所有** class 属性值为 animal的元素

```python
elements = wd.find_elements_by_css_selector('.animal')
```

因为是选择 `所有符合条件的` ，所以用 `find_elements` 而不是 `find_element`







### 2、根据属性选择




id、class 都是web元素的 ```属性``` ，因为它们是很常用的属性，所以`CSS`选择器专门提供了根据 id、class 选择的语法。

同样，可以用`CSS`选择器选择其他属性，比如下面根据`href`选择

```html
<a href="http://www.miitbeian.gov.cn">苏ICP备88885574号</a>
```



<font color='red'>`CSS`选择器支持通过任何属性来选择元素</font>，语法是用一个方括号 `[]` 。

> 比如要选择上面的a元素，就可以使用 `[href="http://www.miitbeian.gov.cn"]` 。
>
> 这个表达式的意思是，选择 属性`href`值为 `http://www.miitbeian.gov.cn` 的元素。



完整代码如下

```python
from selenium import webdriver

wd = webdriver.Chrome(r'e:\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

# 根据属性选择元素
element = wd.find_element_by_css_selector('[href="http://www.miitbeian.gov.cn"]')

# 打印出元素对应的html
print(element.get_attribute('outerHTML'))
```



- 前面可以加上标签名的限制
  - 比如 `div[class='SKnet']` 表示 选择所有 标签名为div，且class属性值为`SKnet`的元素。
  - 属性值用单引号，双引号都可以。



- 可以不指定属性值，
  - 比如 `[href]` ， 表示选择 所有 具有 属性名 为`href `的元素，不管它们的值是什么。



- 如果一个元素具有多个属性

```html
<div class="misc" ctype="gun">沙漠之鹰</div>
```

`CSS` 选择器 可以指定 选择的元素要 同时具有多个属性的限制， `div[class=misc][ctype=gun]`



#### 选择 某个字符串在 元素属性值 的开头、中间、结尾

1. `CSS` 可以选择 属性值 包含 某个字符串 的元素

   比如， 要选择a节点，里面的href属性包含了 miitbeian 字符串，就可以这样写

   ```python
   a[href*="miitbeian"]
   ```



2. `CSS` 可以选择 属性值 以某个字符串 `开头` 的元素

   比如， 要选择a节点，里面的href属性以 http 开头 ，就可以这样写

   ```python
   a[href^="http"]
   ```



3. `CSS` 可以选择 属性值 以某个字符串 `结尾` 的元素

   比如， 要选择a节点，里面的href属性以 gov.cn 结尾 ，就可以这样写

   ```python
   a[href$="gov.cn"]
   ```







### 3、选择语法联合使用



[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=15)



`CSS` selector的另一个强大之处在于： 选择语法 可以 `联合使用`

[请点击打开这个网址](http://cdn1.python3.vip/files/selenium/sample1.html)

比如， 我们要选择 网页 html 中的元素 `<span class='copyright'>版权</span>`

```html
<div id='bottom'>
    <div class='footer1'>
        <span class='copyright'>版权</span>
        <span class='date'>发布日期：2018-03-03</span>
    </div>
    <div class='footer2'>
        <span>备案号
            <a href="http://www.miitbeian.gov.cn">苏ICP备88885574号</a>
        </span>
    </div>        
</div>         
```

`CSS` selector 表达式 可以这样写：

```
div.footer1 > span.copyright
```

选择 一个class 属性值为 copyright 的 span 节点， 并且要求其 必须是 class 属性值为 footer1 的 div节点 的子节点



也可以更简单：

```
.footer1 > .copyright
```

选择 一个class 属性值为copyright 的节点（不限类型）， 并且要求其 必须是 class 属性值为 footer1 的节点的 子节点



当然 这样也是可以的：

```
.footer1  .copyright
```

因为子元素同时也是后代元素



### 4、组选择

如果我们要 同时选择所有class 为 plant `和` class 为 animal 的元素。怎么办？

这种情况，`CSS`选择器可以 使用 `逗号` ，称之为 组选择 ，像这样

```css
.plant , .animal
```



例1：同时选择所有tag名为div的元素 `和` id为BYHY的元素，可以像这样写

```html
div,#BYHY
```

对应的selenium代码如下

```python
elements = wd.find_elements_by_css_selector('div,#BYHY')
for element in elements:
    print(element.text)
```



例2：选择所有 id 为 t1 里面的 `span`和 `p 元素`

我们是不是应该这样写呢？

```py
#t1 > span,p
```

不行，这样写的意思是 选择所有 `id 为 t1 里面的 span` 和 `所有的 p 元素`

只能这样写

```py
#t1 > span , #t1 > p
```



### 5、按次序选择子节点



对应的html如下，关键信息如下

```html
    <body>  
       <div id='t1'>
           <h3> 唐诗 </h3>
           <span>李白</span>
           <p>静夜思</p>
           <span>杜甫</span>
           <p>春夜喜雨</p>              
       </div>      
        
       <div id='t2'>
           <h3> 宋词 </h3>
           <span>苏轼</span>
           <p>赤壁怀古</p>
           <p>明月几时有</p>
           <p>江城子·乙卯正月二十日夜记梦</p>
           <p>蝶恋花·春景</p>
           <span>辛弃疾</span>
           <p>京口北固亭怀古</p>
           <p>青玉案·元夕</p>
           <p>西江月·夜行黄沙道中</p>
       </div>             

    </body>
```

#### 父元素的第n个子节点

我们可以指定选择的元素 `是父元素的第几个子节点`，使用 `nth-child`。

比如，要选择唐诗和宋词的第一个作者，即选择的是 第2个子元素，并且是span类型。

```css
span:nth-child(2)`
```



如果不加节点类型限制，直接这样写

 ```css
 :nth-child(2)
 ```

就是选择所有位置为第2个的所有元素，不管是什么类型



#### 父元素的倒数第n个子节点

也可以反过来， 选择的是父元素的 倒数第几个子节点 ，使用 `nth-last-child`

比如，选择每个子节点倒数第1个子元素，并且是p元素

```css
p:nth-last-child(1)
/* 
结果：
<p>春夜喜雨</p>
<p>西江月·夜行黄沙道中</p>
*/
```



#### 父元素的第几个某类型的子节点

使用 `nth-of-type`，指定选择的元素 是父元素的第几个 `某类型的` 子节点，

比如，我们要选择 唐诗 和宋词 的第一个 作者，即选择的是 第2个子元素，并且是span类型

```css
span:nth-child(2)
/* 
结果：
<span>李白</span>
<span>苏轼</span>
*/
```



还可以这样思考，选择的是 `第1个span类型` 的子元素，所以也可以这样写

 ```css
 span:nth-of-type(1)
 /* 
 结果：
 <span>李白</span>
 <span>苏轼</span>
 */
 ```



#### 父元素的倒数第几个某类型的子节点

当然也可以反过来， 选择父元素的 `倒数第几个某类型` 的子节点

使用 `nth-last-of-type`

```css
p:nth-last-of-type(2)

/* 结果：<p>静夜思</p>  */
```



#### 奇数节点和偶数节点

如果要选择的是父元素的 `偶数节点`，使用 `nth-child(even)`

```css
p:nth-child(even)
```

如果要选择的是父元素的 `奇数节点`，使用 `nth-child(odd)`

```css
p:nth-child(odd)
```



如果要选择的是父元素的 `某类型偶数节点`，使用 `nth-of-type(even)`

如果要选择的是父元素的 `某类型奇数节点`，使用 `nth-of-type(odd)`

### 4、兄弟节点选择



#### 相邻兄弟节点选择

上面的例子里面，我们要选择 唐诗 和宋词 的第一个 作者

还有一种思考方法，就是选择 h3 `后面紧跟着的兄弟节点` span。

这就是一种 相邻兄弟 关系，可以这样写

```css
h3 + span
```

表示元素 紧跟关系的 是 `加号`



#### 后续所有兄弟节点选择

如果要选择是 选择 h3 `后面所有的兄弟节点` span，可以这样写 

```css
h3 ~ span
```



更多`CSS`选择器的介绍，可以参考[CSS 选择器参考手册](http://www.w3school.com.cn/cssref/css_selectors.asp)







## 三、Xpath选择器

### 1、Xpath语法简介



XPath (XML Path Language) 是由国际标准化组织W3C指定的，用来在 XML 和 HTML 文档中选择节点的语言。

目前主流浏览器 (chrome、firefox，edge，safari) 都支持XPath语法，xpath有 1 和 2 两个版本，目前浏览器支持的是 xpath 1的语法。



按F12打开调试窗口，点击 Elements标签。

要验证 Xpath 语法是否能成功选择元素，也可以像 验证 `CSS` 语法那样，按组合键 Ctrl + F ，就会出现 搜索框。



xpath 语法中，整个HTML文档根节点用’/‘表示，如果我们想选择的是根节点下面的html节点，则可以在搜索框输入。

```
/html
```



如果输入下面的表达式

```
/html/body/div
```

这个表达式表示选择html下面的body下面的div元素。

注意 `/` 有点像 `CSS`中的 `>` , 表示直接子节点关系。



#### 绝对路径选择

从根节点开始的，到某个节点，每层都依次写下来，每层之间用 `/` 分隔的表达式，就是某元素的 `绝对路径`

上面的xpath表达式 `/html/body/div` ，就是一个绝对路径的xpath表达式， 等价于 `CSS`表达式 `html>body>div`



自动化程序要使用Xpath来选择web元素，应该调用 WebDriver对象的方法 `find_element_by_xpath` 或者 `find_elements_by_xpath`，像这样：

```python
elements = driver.find_elements_by_xpath("/html/body/div")
```



#### 相对路径选择

有的时候，我们需要选择网页中某个元素， `不管它在什么位置` 。

比如，选择示例页面的所有标签名为 `div` 的元素，如果使用`CSS`表达式，直接写一个 `div` 就行了。

那xpath怎么实现同样的功能呢？ xpath需要前面加 `//` , 表示从当前节点往下寻找所有的后代元素,不管它在什么位置。

所以xpath表达式，应该这样写： `//div`



`//`符号也可以继续加在后面,比如，要选择 所有的 div 元素里面的 所有的 p 元素 ，不管div 在什么位置，也不管p元素在div下面的什么位置，则可以这样写 `//div//p`

对应的自动化程序如下

```python
elements = driver.find_elements_by_xpath("//div//p")
```



如果使用`CSS`选择器，对应代码如下

```python
elements = driver.find_elements_by_css_selector("div p")
```



如果，要选择 所有的 div 元素里面的 直接子节点 p ， xpath，就应该这样写了 `//div/p`

如果使用`CSS`选择器，则为 `div > p`



#### 通配符

如果要选择所有div节点的所有直接子节点，可以使用表达式 `//div/*`

```
*` 是一个通配符，对应任意节点名的元素，等价于`CSS`选择器 `div > *
```

代码如下：

```python
elements = driver.find_elements_by_xpath("//div/*")
for element in elements:
    print(element.get_attribute('outerHTML'))
```



### 2、根据属性选择



[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=28)

Xpath 可以根据属性来选择元素。

根据属性来选择元素 是通过 这种格式来的 `[@属性名='属性值']`

注意：

- 属性名注意前面有个@
- 属性值一定要用引号， 可以是单引号，也可以是双引号

#### 根据id属性选择

选择 id 为 west 的元素，可以这样 `//*[@id='west']`

#### 根据class属性选择

选择所有 select 元素中 class为 single_choice 的元素，可以这样 `//select[@class='single_choice']`

如果一个元素class 有多个，比如

```html
<p id="beijing" class='capital huge-city'>
    北京    
</p>
```

如果要选 它， 对应的 xpath 就应该是 `//p[@class="capital huge-city"]`

不能只写一个属性，像这样 `//p[@class="capital"]` 则不行

#### 根据其他属性

同样的道理，我们也可以利用其它的属性选择

比如选择 具有multiple属性的所有页面元素 ，可以这样 `//*[@multiple]`

#### 属性值包含字符串

要选择 style属性值 包含 color 字符串的 页面元素 ，可以这样 `//*[contains(@style,'color')]`

要选择 style属性值 以 color 字符串 `开头` 的 页面元素 ，可以这样 `//*[starts-with(@style,'color')]`

要选择 style属性值 以 某个 字符串 结尾 的 页面元素 ，大家可以推测是 `//*[ends-with(@style,'color')]`， 但是，很遗憾，这是xpath 2.0 的语法 ，目前浏览器都不支持



### 3、按次序选择

xpath类似`CSS`表达式，也可以根据次序选择元素。

 语法比`CSS`更简洁，直接在方括号中使用数字表示次序

#### 某类型 第几个 子元素

比如要选择 p类型第2个的子元素，就是

```py
//p[2]
```

注意，选择的是 `p类型第2个的子元素` ， 不是 `第2个子元素，并且是p类型` 。

==注意体会区别==

再比如，要选取父元素为div 中的 p类型 第2个 子元素

```py
//div/p[2]
```

#### 第几个子元素

也可以选择第2个子元素，不管是什么类型，采用通配符

比如 选择父元素为div的第2个子元素，不管是什么类型

```py
//div/*[2]
```

#### 某类型 倒数第几个 子元素

当然也可以选取倒数第几个子元素

比如：

- 选取p类型倒数第1个子元素

```py
//p[last()]
```

- 选取p类型倒数第2个子元素

```python
//p[last()-1]
```

- 选择父元素为div中p类型倒数第3个子元素

```python
//div/p[last()-2]
```

#### 范围选择

xpath还可以选择子元素的次序范围。

比如，

- 选取option类型第1到2个子元素

```python
//option[position()<=2]
```

或者

```python
//option[position()<3]
```

- 选择class属性为multi_choice的前3个子元素

```python
//*[@class='multi_choice']/*[position()<=3]
```

- 选择class属性为multi_choice的后3个子元素

```python
//*[@class='multi_choice']/*[position()>=last()-2]
```

为什么不是 `last()-3` 呢？ 因为

`last()` 本身代表最后一个元素

`last()-1` 本身代表倒数第2个元素

`last()-2` 本身代表倒数第3个元素



### 4、组选择

`CSS`有组选择，可以同时使用多个表达式，多个表达式选择的结果都是要选择的元素

`CSS` 组选择，表达式之间用 **逗号** 隔开

xpath也有组选择， 是用 **竖线** 隔开多个表达式

比如，要选所有的option元素 和所有的 h4 元素，可以使用

```python
//option | //h4
```

等同于`CSS`选择器

```python
option , h4
```



再比如，要选所有的 class 为 single_choice 和 class 为 multi_choice 的元素，可以使用

```python
//*[@class='single_choice'] | //*[@class='multi_choice']
```

等同于`CSS`选择器

```python
.single_choice , .multi_choice
```



### 5、父节点选择

xpath可以选择父节点， 这是`CSS`做不到的。

某个元素的父节点用 `/..` 表示

比如，要选择 id 为 china 的节点的父节点，可以这样写 `//*[@id='china']/..` 。

当某个元素没有特征可以直接选择，但是它有子节点有特征， 就可以采用这种方法，先选择子节点，再指定父节点。

还可以继续找上层父节点，比如 `//*[@id='china']/../../..`



### 6、兄弟节点选择

前面学过 `CSS`选择器，要选择某个节点的后续兄弟节点，用 **波浪线**

xpath也可以选择 后续 兄弟节点，用这样的语法 `following-sibling::`

比如，要选择 class 为 single_choice 的元素的所有后续兄弟节点 `//*[@class='single_choice']/following-sibling::*`

等同于`CSS`选择器 `.single_choice ~ *`

如果，要选择后续节点中的div节点， 就应该这样写 `//*[@class='single_choice']/following-sibling::div`



xpath还可以选择 `前面的` 兄弟节点，用这样的语法 `preceding-sibling::`

比如，要选择 class 为 single_choice 的元素的所有前面的兄弟节点 `//*[@class='single_choice']/preceding-sibling::*`

而`CSS`选择器目前还没有方法选择前面的 兄弟节点



要了解更多Xpath选择语法，可以[打开Xpath选择器参考手册](http://www.w3school.com.cn/xpath/index.asp)



---

### selenium 使用Xpath注意点



我们来看一个例子

我们的代码：

- 先选择示例网页中，id是china的元素
- 然后通过这个元素的WebElement对象，使用find_elements_by_xpath，选择里面的p元素，

```python
# 先寻找id是china的元素
china = wd.find_element_by_id('china')

# 再选择该元素内部的p元素
elements = china.find_elements_by_xpath('//p')

# 打印结果
for element in elements:
    print('----------------')
    print(element.get_attribute('outerHTML'))
```

运行发现，打印的 不仅仅是 china内部的p元素， 而是所有的p元素。

要在某个元素内部使用xpath选择元素， 需要 `在xpath表达式最前面加个点` 。

像这样

```python
elements = china.find_elements_by_xpath('.//p')
```





## 三、frame切换/窗口切换

### 1、切换到frame



[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=19)

[请大家点击这里，打开这个链接](http://cdn1.python3.vip/files/selenium/sample2.html)

如果我们要 选择 下图方框中 所有的 蔬菜，使用`CSS`选择，怎么写表达式？

当然，要先查看到它们的html元素特征

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_44899250-d4cde200-ad33-11e8-9abf-e1f24be6fbe3.png)

大家可能会照旧写出如下代码：

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')

# 根据 class name 选择元素，返回的是 一个列表
elements = wd.find_elements_by_class_name('plant')

for element in elements:
    print(element.text)
```

运行一下，你就会发现，运行结果打印内容为空白，说明没有选择到 class 属性值为 plant 的元素。

为什么呢？

因为仔细看，你可以发现， 这些元素是在一个叫 iframe的 元素中的。

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_44899479-76edca00-ad34-11e8-9a56-cb4be10fceb5.png)

这个 iframe 元素非常的特殊， 在html语法中，frame 元素 或者iframe元素的内部 会包含一个 **被嵌入的** 另一份html文档。

在我们使用selenium打开一个网页是， 我们的操作范围 缺省是当前的 html ， 并不包含被嵌入的html文档里面的内容。

如果我们要 操作 被嵌入的 html 文档 中的元素， 就必须 `切换操作范围` 到 被嵌入的文档中。

怎么切换呢？

使用 WebDriver 对象的 switch_to 属性，像这样

```python
wd.switch_to.frame(frame_reference)
```

其中， frame_reference 可以是 frame 元素的属性 name 或者 ID 。

- iframe元素的id ‘frame1’ 

```python
wd.switch_to.frame('frame1')
```

- iframe元素的name属性值 ‘innerFrame’。

```python
wd.switch_to.frame('innerFrame')
```



- 也可以填写frame 所对应的 WebElement 对象。

我们可以根据frame的元素位置或者属性特性，使用find系列的方法，选择到该元素，得到对应的WebElement对象，比如

```python
wd.switch_to.frame(wd.find_element_by_tag_name("iframe"))
```

然后，就可以进行后续操作frame里面的元素了。

上面的例子的正确代码如下

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')


# 先根据name属性值 'innerFrame'，切换到iframe中
wd.switch_to.frame('innerFrame')

# 根据 class name 选择元素，返回的是 一个列表
elements = wd.find_elements_by_class_name('plant')

for element in elements:
    print(element.text)
```

------



如果我们已经切换到某个iframe里面进行操作了，那么后续选择和操作界面元素 就都是在这个frame里面进行的。

切换回原来的主html：

```python
wd.switch_to.default_content()
```

例如，在上面 代码 操作完 frame里面的元素后， 需要 点击 主html 里面的按钮，就可以这样写

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')

# 先根据name属性值 'innerFrame'，切换到iframe中
wd.switch_to.frame('innerFrame')

# 根据 class name 选择元素，返回的是 一个列表
elements = wd.find_elements_by_class_name('plant')

for element in elements:
    print(element.text)

# 切换回 最外部的 HTML 中
wd.switch_to.default_content()

# 然后再 选择操作 外部的 HTML 中 的元素
wd.find_element_by_id('outerbutton').click()

wd.quit()
```



### 2、切换到新的窗口



在网页上操作的时候，我们经常遇到，点击一个链接 或者 按钮，就会打开一个 `新窗口` 。

[请大家点击这里，打开这个链接](http://cdn1.python3.vip/files/selenium/sample3.html)

在打开的网页中，点击 链接 “访问bing网站” ， 就会弹出一个新窗口，访问bing网址。

如果我们用Selenium写自动化程序 **在新窗口里面 打开一个新网址**， 并且去自动化操作新窗口里面的元素，会有什么问题呢？

问题就在于，即使新窗口打开了， 这时候，我们的 WebDriver对象对应的 还是老窗口，自动化操作也还是在老窗口进行，

我们可以运行如下代码验证一下

```python
from selenium import webdriver

wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')
wd.implicitly_wait(10)

wd.get('http://cdn1.python3.vip/files/selenium/sample3.html')

# 点击打开新窗口的链接
link = wd.find_element_by_tag_name("a")
link.click()

# wd.title属性是当前窗口的标题栏 文本
print(wd.title)
```

运行完程序后，最后一行 打印当前窗口的标题栏 文本， 输出内容是

```py
白月黑羽测试网页3
```

说明， 我们的 WebDriver对象指向的还是老窗口，否则的话，运行结果就应该新窗口的标题栏 “微软Bing搜索”



------

如果我们要到新的窗口里面操作，该怎么做呢？

可以使用Webdriver对象的switch_to属性的 window方法，如下所示：

```py
wd.switch_to.window(handle)
```

其中，参数handle需要传入什么呢？

WebDriver对象有window_handles 属性，这是一个列表对象， 里面包括了当前浏览器里面**所有的窗口句柄**。

所谓句柄，大家可以想象成对应网页窗口的一个ID，

那么我们就可以通过 类似下面的代码，

```python
for handle in wd.window_handles:
    # 先切换到该窗口
    wd.switch_to.window(handle)
    # 得到该窗口的标题栏字符串，判断是不是我们要操作的那个窗口
    if 'Bing' in wd.title:
        # 如果是，那么这时候WebDriver对象就是对应的该该窗口，正好，跳出循环，
        break
```

上面代码的用意就是：

我们依次获取 wd.window_handles 里面的所有 句柄 对象， 并且调用 wd.switch_to.window(handle) 方法，切入到每个窗口，

然后检查里面该窗口对象的属性（可以是标题栏，地址栏），判断是不是我们要操作的那个窗口，如果是，就跳出循环。

------

同样的，如果我们在新窗口 操作结束后， 还要回到原来的窗口，该怎么办？

我们可以仍然使用上面的方法，依次切入窗口，然后根据 标题栏 之类的属性值判断。

还有更省事的方法。

因为我们一开始就在 原来的窗口里面，我们知道 进入新窗口操作完后，还要回来，可以事先 保存该老窗口的 句柄，使用如下方法

```python
# mainWindow变量保存当前窗口的句柄
mainWindow = wd.current_window_handle
```

切换到新窗口操作完后，就可以直接像下面这样，将driver对应的对象返回到原来的窗口

```python
#通过前面保存的老窗口的句柄，自己切换到老窗口
wd.switch_to.window(mainWindow)
```



## 四、选择框

[请点击打开这个网址](http://cdn1.python3.vip/files/selenium/test2.html)

并且按F12，观察HTML的内容

常见的选择框包括： radio框、checkbox框、select框



### 1、radio框



[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=21)

radio框选择选项，直接用WebElement的click方法，模拟用户点击就可以了。

比如, 我们要在下面的html中：

- 先打印当前选中的老师名字
- 再选择 小雷老师

```html
<div id="s_radio">
  <input type="radio" name="teacher" value="小江老师">小江老师<br>
  <input type="radio" name="teacher" value="小雷老师">小雷老师<br>
  <input type="radio" name="teacher" value="小凯老师" checked="checked">小凯老师
</div>
```

对应的代码如下

```python
# 获取当前选中的元素
element = wd.find_element_by_css_selector(
  '#s_radio input[checked=checked]')
print('当前选中的是: ' + element.get_attribute('value'))

# 点选 小雷老师
wd.find_element_by_css_selector(
  '#s_radio input[value="小雷老师"]').click()
```



### 2、checkbox框



[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=22)

对checkbox进行选择，也是直接用 WebElement 的 click 方法，模拟用户点击选择。

需要注意的是，要选中checkbox的一个选项，必须 `先获取当前该复选框的状态` ，如果该选项已经勾选了，就不能再点击。否则反而会取消选择。

比如, 我们要在下面的html中：选中 小雷老师

```html
<div id="s_checkbox">
  <input type="checkbox" name="teacher" value="小江老师">小江老师<br>
  <input type="checkbox" name="teacher" value="小雷老师">小雷老师<br>
  <input type="checkbox" name="teacher" value="小凯老师" checked="checked">小凯老师
</div>
```

我们的思路可以是这样：

- 先把 已经选中的选项全部点击一下，确保都是未选状态
- 再点击 小雷老师

示例代码

```python
# 先把 已经选中的选项全部点击一下
elements = wd.find_elements_by_css_selector(
  '#s_checkbox input[checked="checked"]')

for element in elements:
    element.click()

# 再点击 小雷老师
wd.find_element_by_css_selector(
  "#s_checkbox input[value='小雷老师']").click()
```



### 3、select框

[点击这里，边看视频讲解，边学习以下内容](https://www.bilibili.com/video/av64421994/?p=23)

radio框及checkbox框都是input元素，只是里面的type不同而已。

select框 则是一个新的select标签，大家可以对照浏览器网页内容查看一下

对于Select 选择框， Selenium 专门提供了一个 `Select类` 进行操作。

`Select类` 提供了如下的方法

- select_by_value

  根据选项的 `value属性值` ，选择元素。

  比如，下面的HTML，

```html
<option value="foo">Bar</option>
```

​		就可以根据 foo 这个值选择该选项，

```py
s.select_by_value('foo')
```

- select_by_index

  根据选项的 `次序` （从0开始），选择元素

- select_by_visible_text

  根据选项的 `可见文本` ，选择元素。

  比如，下面的HTML，

```html
<option value="foo">Bar</option>
```

​		就可以根据 Bar 这个内容，选择该选项

```python
s.select_by_visible_text('Bar')
```

- deselect_by_value

  根据选项的value属性值， `去除` 选中元素

- deselect_by_index

  根据选项的次序，`去除` 选中元素

- deselect_by_visible_text

  根据选项的可见文本，`去除` 选中元素

- deselect_all

  `去除` 选中所有元素



#### Select单选框

对于 select单选框，操作比较简单：

不管原来选的是什么，直接用Select方法选择即可。

例如，选择示例里面的小雷老师，示例代码如下

```python
# 导入Select类
from selenium.webdriver.support.ui import Select

# 创建Select对象
select = Select(wd.find_element_by_id("ss_single"))

# 通过 Select 对象选中小雷老师
select.select_by_visible_text("小雷老师")
```



#### Select多选框

对于select多选框，要选中某几个选项，要注意去掉原来已经选中的选项。

例如，我们选择示例多选框中的 小雷老师 和 小凯老师

可以用select类 的deselect_all方法，清除所有 已经选中 的选项。

然后再通过 select_by_visible_text方法 选择 小雷老师 和 小凯老师。

示例代码如下：

```python
# 导入Select类
from selenium.webdriver.support.ui import Select

# 创建Select对象
select = Select(wd.find_element_by_id("ss_multi"))

# 清除所有 已经选中 的选项
select.deselect_all()

# 选择小雷老师 和 小凯老师
select.select_by_visible_text("小雷老师")
select.select_by_visible_text("小凯老师")
```



# 操控元素的基本方法

选择到元素之后，我们的代码会返回元素对应的 WebElement对象，通过这个对象，我们就可以 `操控` 元素了。

操控元素通常包括

- 点击元素
- 在元素中输入字符串，通常是对输入框这样的元素
- 获取元素包含的信息，比如文本内容，元素的属性



## 一、点击元素

`点击元素` 非常简单，就是调用元素WebElement对象的 click方法。

> element.click()

这里我们要补充讲解一点。

当我们调用 WebElement 对象的 click 方法去点击 元素的时候， 浏览器接收到自动化命令，点击的是该元素的 `中心点` 位置 。

例如，对于下面的这样一个元素

![out](http://cdn1.python3.vip/imgs/gh/36257654_64305834-39fd8880-cfc3-11e9-9299-d1ec45405e1a.png)

我们要点击 添加客户 这个按钮，既可以点击 右边对应的 绿色框子总的button 元素 ，也可以点击红色框子中的span元素 。

因为这两个元素的中心点都是 button 内部，都是有效点击区域



## 二、在输入框输入内容

`输入字符串` 也非常简单，就是调用元素WebElement对象的send_keys方法。前面我们也已经学过。



如果我们要 把输入框中已经有的内容清除掉，可以使用WebElement对象的clear方法

请大家[点击打开这个网址](http://cdn1.python3.vip/files/selenium/test3.html)，并且按F12，观察HTML的内容

我们要写一个自动化程序：要求在输入框中填入姓名：白月黑羽。

而且要做到输入框中已经有的提示字符，需要先 `清除掉`

代码应该如下

```python
element = wd.find_element_by_id("input1")

element.clear() # 清除输入框已有的字符串
element.send_keys('白月黑羽') # 输入新字符串
```



## 三、获取元素信息

### 1、获取元素的文本内容

我们已经知道，通过WebElement对象的 `text` 属性，可以获取元素 `展示在界面上的` 文本内容。

比如

```python
element = wd.find_element_by_id('animal')
print(element.text)
```

通过WebElement对象的 `text` 属性，可以获取元素 `展示在界面上的` 文本内容。

但是有时候，元素的文本内容没有展示在界面上，或者没有完全展示在界面上。 

这时，用WebElement对象的text属性，获取文本内容，就会有问题。

出现这种情况，可以尝试使用 `element.get_attribute('innerText')` ，或者 `element.get_attribute('textContent')`



###  2、获取元素属性

通过WebElement对象的 `get_attribute` 方法来获取元素的属性值，

比如要获取元素属性class的值，就可以使用 `element.get_attribute('class')`

如下：

```python
element = wd.find_element_by_id('input_name')
print(element.get_attribute('class'))
```

执行完自动化代码，如果想关闭浏览器窗口可以调用WebDriver对象的 quit 方法，像这样 wd.quit()



### 3、获取整个元素对应的HTML

要获取整个元素对应的HTML文本内容，可以使用 `element.get_attribute('outerHTML')`

如果，只是想获取某个元素 `内部` 的HTML文本内容，可以使用 `element.get_attribute('innerHTML')`



### 4、获取输入框里面的文字

对于input输入框的元素，要获取里面的输入文本，用text属性是不行的，这时可以使用 `element.get_attribute('value')`

比如

```python
element = wd.find_element_by_id("input1")
print(element.get_attribute('value')) # 获取输入框中的文本
```



## 四、更多动作——`ActionChains` 类

### 1、介绍

比如：比如 鼠标右键点击、双击、移动鼠标到某个元素、鼠标拖拽等。

这些操作，可以通过 Selenium 提供的 `ActionChains` 类来实现。

ActionChains 类 里面提供了 一些特殊的动作的模拟，我们可以通过 ActionChains 类的代码查看到，如下所示

![image](http://cdn1.python3.vip/imgs/gh/36257654_62782521-ffe8b600-baec-11e9-9ca8-fde0af030da5.png)



### 2、鼠标移动到元素上

百度首页的右上角，有个 **更多产品** 选项，如下图所示

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_44762007-09953a00-ab78-11e8-979e-da7933a78b54.png)

如果我们把鼠标放在上边，就会弹出 下面的 糯米、音乐、图片 等图标。

使用 ActionChains 来 模拟鼠标移动 操作的代码如下：

```python
from selenium import webdriver

driver = webdriver.Chrome(r'f:\chromedriver.exe')
driver.implicitly_wait(5)

driver.get('https://www.baidu.com/')

from selenium.webdriver.common.action_chains import ActionChains

ac = ActionChains(driver)

# 鼠标移动到 元素上
ac.move_to_element(
    driver.find_element_by_css_selector('[name="tj_briicon"]')
).perform()
```





# EX-1 CSS Selector 选择子元素和后代元素



HTML中， 元素 内部可以 **包含其他元素**， 比如 下面的 HTML片段

```html
<div id='container'>
    
    <div id='layer1'>
        <div id='inner11'>
            <span>内层11</span>
        </div>
        <div id='inner12'>
            <span>内层12</span>
        </div>
    </div>

    <div id='layer2'>
        <div id='inner21'>
            <span>内层21</span>
        </div>
    </div>
    
</div>
```

下面的一段话有些绕口， 请 细心 阅读：

> id 为 `container` 的div元素 包含了 id 为 `layer1` 和 `layer2` 的两个div元素。
>
> 这种包含是直接包含， 中间没有其他的层次的元素了。 所以 id 为 `layer1` 和 `layer2` 的两个div元素 是 id 为 `container` 的div元素 的 **直接子元素**
>
> 而 id 为 `layer1` 的div元素 又包含了 id 为 `inner11` 和 `inner12` 的两个div元素。 中间没有其他层次的元素，所以这种包含关系也是 **直接子元素** 关系
>
> id 为 `layer2` 的div元素 又包含了 id 为 `inner21` 这个div元素。 这种包含关系也是 **直接子元素** 关系
>
> 
>
> 而对于 id 为 `container` 的div元素来说， id 为 `inner11` 、`inner12` 、`inner22` 的元素 和 两个 `span类型的元素` 都不是 它的直接子元素， 因为中间隔了 几层。
>
> 虽然不是直接子元素， 但是 它们还是在 `container` 的内部， 可以称之为它 的 **后代元素**
>
> 后代元素也包括了直接子元素， 比如 id 为 `layer1` 和 `layer2` 的两个div元素 也可以说 是 id 为 `container` 的div元素 的 **直接子元素，同时也是后代子元素**



1. 如果 `元素2` 是 `元素1` 的 直接子元素， `CSS` Selector 选择子元素的语法是这样的

	> 元素1 > 元素2

	中间用一个大于号 （我们可以理解为箭头号）

	注意，最终选择的元素是 **元素2**， 并且要求这个 **元素2** 是 **元素1** 的直接子元素

	

	也支持更多层级的选择， 比如

	> 元素1 > 元素2 > 元素3 > 元素4

	就是选择 `元素1` 里面的子元素 `元素2` 里面的子元素 `元素3` 里面的子元素 `元素4` ， 最终选择的元素是 **元素4**

	

2. 如果 `元素2` 是 `元素1` 的 后代元素， `CSS` Selector 选择后代元素的语法是这样的

	> 元素1   元素2

	中间是一个或者多个空格隔开

	最终选择的元素是 **元素2** ， 并且要求这个 **元素2** 是 **元素1** 的后代元素。


   也支持更多层级的选择， 比如

   > 元素1   元素2   元素3  元素4

   最终选择的元素是 **元素4**


```python
element = wd.find_element_by_css_selector('#bottom > .footer2  a')
```













# EX-2 实战技巧

## 1、 直接执行javascript

我们可以直接让浏览器运行一段javascript代码，并且得到返回值，如下

```python
# 直接执行 javascript，里面可以直接用return返回我们需要的数据
nextPageButtonDisabled = driver.execute_script(
    '''
    ele = document.querySelector('.soupager > button:last-of-type');
    return ele.getAttribute('disabled')
    ''')

# 返回的数据转化为Python中的数据对象进行后续处理
if nextPageButtonDisabled == 'disabled': # 是最后一页
    return True
else: # 不是最后一页
    return False
```

## 2、冻结界面

有些网站上面的元素， 我们鼠标放在上面，会动态弹出一些内容。

比如，百度首页的右上角，有个 **更多产品** 选项，如下图所示

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_44762007-09953a00-ab78-11e8-979e-da7933a78b54.png)

如果我们把鼠标放在上边，就会弹出 下面的 糯米、音乐、图片 等图标。

如果我们要用 selenium 自动化 点击 糯米图标，就需要 F12 查看这个元素的特征。

但是 当我们的鼠标 从 糯米图标 移开， 这个 栏目就整个消失了， 就没法 查看 其对应的 HTML。

怎么办？

可以如下图所示：

![白月黑羽Python3教程](http://cdn1.python3.vip/imgs/gh/36257654_44762324-3e55c100-ab79-11e8-89af-4a744d775c45.png)



在 开发者工具栏 console 里面执行如下js代码

```js
setTimeout(function(){debugger}, 5000)
```



这句代码表示在 5000毫秒后，执行 debugger 命令

执行该命令会 浏览器会进入debug状态。 debug状态有个特性， 界面被冻住， 不管我们怎么点击界面都不会触发事件。



所以，我们可以在输入上面代码并回车 执行后， 立即 鼠标放在界面 右上角 更多产品处。

这时候，就会弹出 下面的 糯米、音乐、图片 等图标。

然后，我们仔细等待 5秒 到了以后， 界面就会因为执行了 debugger 命令而被冻住。

然后，我们就可以点击 开发者工具栏的 查看箭头， 再去 点击 糯米图标 ，查看其属性了。



## 3、处理的弹出对话框

有的时候，我们经常会在操作界面的时候，出现一些弹出的对话框。

[请点击打开这个网址](http://cdn1.python3.vip/files/selenium/test4.html)

分别点击界面的3个按钮，你可以发现：

弹出的对话框有三种类型，分别是 Alert（警告信息）、confirm（确认信息）和prompt（提示输入）

### （1）Alert

Alert 弹出框，目的就是显示通知信息，只需用户看完信息后，点击 OK（确定） 就可以了。

那么，自动化的时候，代码怎么模拟用户点击 OK 按钮呢？

selenium提供如下方法进行操作

```python
driver.switch_to.alert.accept()
```

注意：如果我们不去点击它，页面的其它元素是不能操作的。 {: .notice–info}

如果程序要获取弹出对话框中的信息内容， 可以通过 如下代码

```py
driver.switch_to.alert.text
```



示例代码如下

```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.implicitly_wait(5)
driver.get('http://cdn1.python3.vip/files/selenium/test4.html')


# --- alert ---
driver.find_element_by_id('b1').click()

# 打印 弹出框 提示信息
print(driver.switch_to.alert.text) 

# 点击 OK 按钮
driver.switch_to.alert.accept()
```

### （2）Confirm

Confirm弹出框，主要是让用户确认是否要进行某个操作。

比如：当管理员在网站上选择删除某个账号时，就可能会弹出 Confirm弹出框， 要求确认是否确定要删除。

Confirm弹出框 有两个选择供用户选择，分别是 OK 和 Cancel， 分别代表 确定 和 取消 操作。

那么，自动化的时候，代码怎么模拟用户点击 OK 或者 Cancel 按钮呢？

selenium提供如下方法进行操作

如果我们想点击 OK 按钮， 还是用刚才的 accept方法，如下

```py
driver.switch_to.alert.accept()
```

如果我们想点击 Cancel 按钮， 可以用 dismiss方法，如下

```py
driver.switch_to.alert.dismiss()
```



示例代码如下

```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.implicitly_wait(5)
driver.get('http://cdn1.python3.vip/files/selenium/test4.html')

# --- confirm ---
driver.find_element_by_id('b2').click()

# 打印 弹出框 提示信息
print(driver.switch_to.alert.text)

# 点击 OK 按钮 
driver.switch_to.alert.accept()

driver.find_element_by_id('b2').click()

# 点击 取消 按钮
driver.switch_to.alert.dismiss()
```

### （3）Prompt

出现 Prompt 弹出框 是需要用户输入一些信息，提交上去。

比如：当管理员在网站上选择给某个账号延期时，就可能会弹出 Prompt 弹出框， 要求输入延期多长时间。

可以调用如下方法

```py
driver.switch_to.alert.send_keys()
```



示例代码如下

```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.implicitly_wait(5)
driver.get('http://cdn1.python3.vip/files/selenium/test4.html')


# --- prompt ---
driver.find_element_by_id('b3').click()

# 获取 alert 对象
alert = driver.switch_to.alert

# 打印 弹出框 提示信息
print(alert.text)

# 输入信息，并且点击 OK 按钮 提交
alert.send_keys('web自动化 - selenium')
alert.accept()

# 点击 Cancel 按钮 取消
driver.find_element_by_id('b3').click()
alert = driver.switch_to.alert
alert.dismiss()
```

**注意** ： 有些弹窗并非浏览器的alert 窗口，而是**html元素**，这种对话框，只需要通过之前介绍的`选择器`选中并进行相应的操作就可以了。 {: .notice–info}



## 4、其他技巧

下面是一些其他的 Selenium 自动化技巧

### （1）窗口大小操作

有时间我们需要获取窗口的属性和相应的信息，并对窗口进行控制

- 获取窗口大小

```py
driver.get_window_size()
```

- 改变窗口大小

```py
driver.set_window_size(x, y)
```

### （2）获取当前窗口标题

浏览网页的时候，我们的窗口标题是不断变化的，可以使用WebDriver的title属性来获取当前窗口的标题栏字符串。

```python
driver.title
```

### （3）获取当前窗口URL地址

```py
driver.current_url
```

例如，访问网易，并获取当前窗口的标题和URL

```python
from selenium import  webdriver

driver = webdriver.Chrome()
driver.implicitly_wait(5)

# 打开网站
driver.get('https://www.163.com')

# 获取网站标题栏文本
print(driver.title) 

# 获取网站地址栏文本
print(driver.current_url) 
```

### （4）截屏

有的时候，我们需要把浏览器屏幕内容保存为图片文件。

比如，做自动化测试时，一个测试用例检查点发现错误，我们可以截屏为文件，以便测试结束时进行人工核查。

可以使用 WebDriver 的 get_screenshot_as_file方法来截屏并保存为图片。

```python
from selenium import  webdriver

driver = webdriver.Chrome()
driver.implicitly_wait(5)

# 打开网站
driver.get('https://www.baidu.com/')

# 截屏保存为图片文件
driver.get_screenshot_as_file('1.png')
```

### （5）手机模式

我们可以通过 `desired_capabilities` 参数，指定以手机模式打开chrome浏览器

参考代码，如下

```python
from selenium import webdriver

mobile_emulation = { "deviceName": "Nexus 5" }

chrome_options = webdriver.ChromeOptions()

chrome_options.add_experimental_option("mobileEmulation", mobile_emulation)

driver = webdriver.Chrome( desired_capabilities = chrome_options.to_capabilities())

driver.get('http://www.baidu.com')

input()
driver.quit()
```

### （6）上传文件

有时候，网站操作需要上传文件。

比如，著名的在线图片压缩网站： https://tinypng.com/

通常，网站页面上传文件的功能，是通过 `type` 属性 为 `file` 的 HTML `input` 元素实现的。

如下所示：

```html
<input type="file" multiple="multiple">
```

使用selenium自动化上传文件，我们只需要定位到该input元素，然后通过 send_keys 方法传入要上传的文件路径即可。

如下所示：

```python
# 先定位到上传文件的 input 元素
ele = wd.find_element_by_css_selector('input[type=file]')

# 再调用 WebElement 对象的 send_keys 方法
ele.send_keys(r'h:\g02.png')
```



如果需要上传多个文件，可以多次调用send_keys，如下

```py
ele = wd.find_element_by_css_selector('input[type=file]')
ele.send_keys(r'h:\g01.png')
ele.send_keys(r'h:\g02.png')
```





## 自动化Edge浏览器



自动化基于Chromium内核的 微软最新Edge浏览器，首先需要查看Edge的版本。

点击菜单 `帮助和反馈` > `关于Microsoft Edge` ，在弹出界面中，查看到版本，比如

```py
版本 79.0.309.71 (官方内部版本) (64 位)
```

然后 [打开Edge浏览器驱动下载网页](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/#downloads) ，并选择下载对应版本的驱动。

在自动化代码中，指定使用Edge Webdriver类，并且指定 Edge 驱动路径，如下所示

```python
from selenium import webdriver

driver = webdriver.Edge(r'd:\tools\webdrivers\msedgedriver.exe')

driver.get('http://www.51job.com')
```



## 自动化Electron程序

Electron程序都是基于基于Chromium技术开发的，所以基本也可以用Chromedriver驱动自动化。

要自动化，首先需要得到内置 Chromium的版本号。

向开发人员查询打开 Dev Tools 窗口的快捷键（通常是ctrl + Shift + I），打开Dev Tools 窗口后， 在 Console tab中输入 如下语句，查看版本

```js
> navigator.appVersion.match(/.*Chrome\/([0-9\.]+)/)[1]
  "79.0.3945.130"
```

然后去 [chromedriver下载网址](https://chromedriver.storage.googleapis.com/index.html) ，下载对应版本的驱动。

在自动化程序中需要指定打开的可执行程序为Electron程序，而不是 Chrome浏览器。

如下所示

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

ops = Options()

# 指定Electron程序路径
ops.binary_location = r"C:\electronAPP.exe"

driver = webdriver.Chrome(r"e:\chromedriver.exe",
                            options = ops)
```


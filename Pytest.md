# 一、了解测试框架

 * 组织协调自动化用例（各种层级的套件机制）
 * 可以根据特征挑选用例执行（名称、标签过滤）
 * 控制用例执行前和执行后动作（初始化和清除）
 * 输出测试报表的能力**



# 二、了解Pytest

 * 公认的Python领域的最强的自动化框架
 * Python的第三方单元测试框架，比自带的Unittest更简洁高效，支持315种插件，并且兼容unittest
 * Unittest迁移到Pytest不需要重写代码
 * 纯Python编写用例



# 三、Pytest编写规范

 * 测试方法必须以``test_`开头	

 * 断言必须用assert

 * 测试类必须以Test开头，并且不能有==**init**==方法

 * .py文件必须以test_开头（否则无法自动检测到）

   > 假设有一个名为测试套件的包，内有test_002.py 和 测试_003.py
   >
   > 在终端输入&emsp;pytest 测试套件 -s&emsp; 
   >
   > 则只会执行test_002.py的用例

   



# 四、Pytest测试基础

##  **4.1&emsp;测试方式**

* 函数方式定义测试用例

	```python
	def test_case001():
	    print('这是case001')
	```

* 类的方式定义测试用例

	```python
	class TestLogin():
	    #用例1
	    def test_case1(self):
	        print('执行类方法测试用例*****1')
	    #用例2
	    def test_case2(self):
	        print('执行类方法测试用例*****2')
	```

## **4.2 &ensp;执行用例   **

### 4.2.1 方式一：命令行

##### 指定模块

​		`pytest suite.py -s`

#####  指定目录

​		 `pytest case1 -s`

##### 指定多个目录

​		`pytest case1 case2\登录 `

##### 指定模块内的函数或类

  - 指定一个类 

     `pytest cases\登录\test_1.py::Test_Login::test_C001001`

  - 指定类里面的方法 

    `pytest cases\登录\test_1.py::Test_Login::`

 ##### 根据名字 

  `pytest -k C001001 -s cases`

  - 注意，-k	后面的名字。cases是目录名

    1. 可以是测试函数、类、模块、目录的名字；

    2. 大小写敏感 ；

    3. 不一定要完整，只要能部分匹配上就行

       - 原函数为`def test_C001001(self):`

         ​	即，001001也可

    4. 可以用not表示选择名字中不包含

       `pytest -k "not C001001" -s cases`

    5. 可以用and表示选择名字同时包含多个关键字

       `pytest -k "C and 001" -s cases`

    6. 可以用or表示选择名字包含指定关键字之一
    
       `pytest -k "C or 001" -s cases`

##### 根据标签

1. 可以这样给某个方法加上标签 webtest

  ```python
  import pytest
  
  class Test_IncorrectCode:
      @pytest.mark.webtest
      def test_C001021(self):
          print('\n C001021')
          assert 1==1
  ```

  然后，可以这样运行指定标签的用例 ，*-m意为mark*

  	`pytest cases -m webtest -s`



2. 或者给整个类加上标签

```python
import pytest

@pytest.mark.webtest  
class Test_IncorrectCode:
    def test_C001021(self):
        print('\n C001021')
        assert 1==1
```



3.  当然标签也支持中文

```python
import pytest

@pytest.mark.网页测试  
class Test_IncorrectCode:
    def test_C001021(self):
        print('\n C001021')
        assert 1==1
```



4. 可以同时添加多个标签

```python
@pytest.mark.网页测试
@pytest.mark.登陆测试
class Test_IncorrectCode:
    def test_C001022(self):
        print('\n C001021')
        assert 1==1
```



5. 可以定义一个全局变量pytestmark为 *整个模块文件* 设定标签

```python
import pytest

pytestmark =pytest.mark.网页测试
```

```python
import pytest

#定义多个标签
pytestmark =[pytest.mark.网页测试,pytest.mark.登陆测试]
```





> 为什么加-s，
> &emsp; 1. 输出用例里print的内容
> &emsp; 2. 执行该.py文件下所有的用例
>
> > ​	如果我们希望得到更详细的执行信息，包括每个测试类、测试函数的名字，可以加上参数-v，这个参数可以和-s合并成-sv
> >
> > ​     即  `pytest -sv`



### 4.2.2 方式二：代码

```python
if __name__=='__main__' :
    #需要导入pytest包
	pytest.main([ 'suite.py' , '-s' ])
```



## **4.3 &ensp;断言   **

```python
def test_case001():
    print('This is case_001')
    assert 2020==2019
```





# **五、 Pytest初始化与清除   **

​	将用例中的**共同操作**抽出，设计为setup和teardown函数

1. 每个用例在执行前，都会执行setup函数

2. 每个用例在执行后，都会执行teardown函数
3. 初始化级别参照下面的**层级关系**

**&emsp;层级关系——高级别会优先进行**

* 方法级别 —— setup_method / teardown_method

	- 每个用例执行都会调用一遍

		```python
		def setup_method():
		    print('执行用例初始化操作')
			print('打开浏览器')
		def teardown_method():
		    print('执行用例初始化操作')
			print('打开浏览器')
		def test_case1():
		    print('执行类方法测试用例*****1')
		```

		

* 类级别—— setup_class / teardown_class

	- 定义在类里

		```python
		class TestLogin():
		    def setup_class(self):
		        print('执行用例初始化操作')
		        print('打开浏览器')
		    def teardown_class(self):
		        print('执行用例初始化操作')
		        print('打开浏览器')
		    def test_case1(self):
		        print('执行类方法测试用例*****1')
		```

		

* 模块级别——setup_module / teardown_moudle

	- 一个.py文件只会执行一次初始化与清除

		```python
		def setup_module():
		    print('执行用例初始化操作')
			print('打开浏览器')
		def teardown_module():
		    print('执行用例初始化操作')
			print('打开浏览器')
		```


- 目录级别(不推荐)

  - 目录级别的初始化清除，就是针对整个目录执行的初始化、清除。

  - 我们需要在初始化的目录下面创建一个 名为conftest.py 的文件，里面内容如下所示

    ```python
    import pytest
    
    @pytest.fixture(scope='package',autouse='True')
    def st_emptyEnv():
        print(f'\n### 初始化-目录甲')
        yield
        print(f'\n### 清除-目录甲')
    ```

    - 目录级别存在一个==重要的bug：==

      清除操作不一定会在该目录最后一个测试用例执行完后调用，有可能延迟调用。





# 六、简单例子

```python
from selenium import webdriver
import time


class Test_IncorrectLogin:

    def test_UI_001(self):
        print('\n case UI_001')
        #浏览器是Chrome
        driver=webdriver.Chrome()

        #全局等待时间——必有
        driver.implicitly_wait(10)

        #获取网址
        driver.get('http://127.0.0.1/mgr/sign.html')

        #输入账号密码
        driver.find_element_by_id('username').send_keys('')
        driver.find_element_by_id('password').send_keys('88888888')

        #模拟鼠标的点击
        driver.find_element_by_css_selector("button[type='submit']").click()

        #网页反应时间，等待2s
        time.sleep(2)

        #获取浏览器原生提示
        alertText=driver.switch_to.alert.text
        print(alertText)

        assert alertText=='请输入用户名'
```



# 七、数据驱动

**定义：**

​		如果有一批用例，具有==相同的测试步骤==，只是==测试参数数据不同==。把测试数据从用例代码里分离开，以后增加新的测试用例，只需要修改数据。

*但这样，由于都在一个函数里，在测试报告上，测试结果和测试用例很难对应上*

==不建议使用==

```python
class Test_IncorrectLogin:
    @pytest.mark.parametrize('username,password,expectedalert')[
        (None,'8888888','请输入用户名'),
        ('byby',None,'请输入密码'),
        ('byh','88888888','登陆失败：用户名或密码错误'),
        ('byby','88888877','登陆失败：用户名或密码错误'),
    ]
    
    def test_UI_001_004(self,username,password,expectedalert):
       	#LoginAndCheck 是login.py的函数，为封装结果
    	#WPS的PyPrac里的cases文件夹里有备份
    	alertText = LoginAndCheck(username,password)
        assert alertText==expectedalert
```



这样，就不用定义四个测试用例方法了，而且测试数据可以集中存放



# **EX-1、 allure测试报告   **

- 测试环境准备

> 1、下载allure-commandline-xxx.zip
>
> 2、解压allure-commandline-xxx.zip到一个文件目录中
>
> 3、将`allure/bin`路径添加至环境变量path中
>
> 4、`pip install allure-pytest`
>
> - Pytest的allure插件用于生成报告缓存文件
>
> 5、`allure --version`&emsp; 在CMD中验证

- Pytest 生成 allure 报告
	- 代码生成不了的话，可以用命令行生成

```python
if __name__=='__main__':
    #生成报告缓存文件，第三个参数是指定缓存文件的路径,在当前目录下生成tmp文件夹.etc
    pytest.main(
       'test_001.py','-s','--alluredir=tmp/allure_result'
    )
    #打开报告
    os.system('allure serve tmp/allure_result')
```





 
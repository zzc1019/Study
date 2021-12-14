# UnitTest测试框架

优点：

便于整理测试用例

断言

生成测试报告

## Unittest基础知识

### 简单使用

```python
import unittest  #标准库，不需要额外安装
class TestMyCase(unittest.TestCase):  #继承父类
  def setUp(self):  #重写父类方法  测试用例执行前
    print("----->set up")
  def tearDown(self):   #重写父类方法  测试用例执行后
    print("----->tear down")
  def test_1(self):   # 必须以test开头才能执行
    print("do test1")
    a=1
    b=2
    self.assertEqual(a,b)  #断言判断是否相等
  def test_2(self):
    print("do test2")
    x='p'
    s='python'
    self.assertIn(x,s)  #判断是否在字符串里  和in用法一样
  def test_3(self):
    print("do test3")
    a=[1,2,3,4]
    b=[i for i in range(1,5)]
    self.assertListEqual(a,b)  #判断两个列表是否相等
    
   def test_4(self):
    print("assertTrue")
    a=True
    self.assertTrue(a)
    
    def test_assertRaises(self):
      self.assertRaises(ZeroDivisionError,self.mydiv,3,0)  #抛出异常
if __name__=='__main__':
  unittest.main()
```

可以直接导出测试报告

### 优化login页面

```python
class LoginTestCase(unittest.TestCase):
  def do_input(self,user,password,randcode):
    if user:
    	self.driver.find_element_by_id('id1').send_keys(user)
    if password:  
    	self.driver.find_element_by_id('id2').send_keys(password)
    if randcode:
   	 	self.driver.find_element_by_id('id3').send_keys(randcode)
    self.driver.find_element_by_id('id4').click()
  def setUp(self):
    self.driver=webdriver.Chrome()
    self.driver.get()
    self.driver.implicitly_wait(10)
    
  def tearDown(self):  
    self.driver.close()
  def test_1_normal(self):
    #模拟登陆
    user,password,randcode = "admin","123456","1234"
    #定位并输入
		self.do_input(user=user,password=password,randcode=randcode)
    time.sleep(1)
    page_title=self.driver.title
    self.assertEqual(title,"xxxx")
if __name__=='__main__':
  unittest.main()  
```

## Unittest 进阶知识

- TestCase 测试用例
- TestFixture 测试工具/夹具
- TestSuite 测试套件
- TestRunner 执行器
- TestReport 测试报告

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606183513626.png" alt="image-20210606183513626" style="zoom:50%;" />

### 类方法

```python
#在所有的TestCase中先执行SetUp和TearDown而不是每一个都执行
class TestMyCase(unittest.TestCase):
 	@classmethod
	def setUpClass(cls):
    print("------setupclass-------")
    #读取相关配置文件
    cls.ipaddr="192.168.1.1"   #类属性
  @classmethod
  def tearDownClass(cls):
    print("-------tearDownClass-----")
  def test_1(self,TestMyCase.ipaddr)  
```

### 方法关联

```python
每个方法的self都不一样
def test_1(self):
  TestMyCase.name="aaa"
 def test_2(self):
  print(TestMyCase.name)
```

### 方法跳过

解释器实现

```python
# 解释器处理 最先执行在所有方法之前

@unittest.skip("不想执行的原因")
def test_2(self):
  print("do test2")
  
@unittest.skipIf(2>1,"原因")  
def test_3(self):
  print("do test3")
  
@unittest.skipUnless(1>2,"原因")  #和if相反 很少用
def test_4(self):
	print("do test4")
```

用上一个test的条件控制下一个

```python
#类中定义一个result
result={"m5":False}
def test_2(self):
  self.result['m5']=True
def test_5(self):
  if not self.result['m5']:
    return
  print("do test5")
```

## 数据驱动

**DDT** ：Data Drive Test

- 将数据与代码分离

- 不写重复逻辑

```python
import unittest
from ddt import ddt

@ddt
class TestMyCase(unittest.TestCase):
  #单一参数
  @data("18810001000","13520002000")
  def test_1(self,phone):
    print("测试手机号注册流程：",phone)
    
  #多个参数  
  @data(["admin","123456"],["test","0000"])  
  @unpack
  def test_1(self,user,pwd):
    print("测试手机号注册流程：",user,pwd)
  
if __name__=='__main__':
  unittest.main()      
```

### 从txt读取

```python
def read_phone():
	li=[]
	with open("phone.txt","r","utf-8") as f:
	  for line in f.readlines():
 		 	li.append(line.strip("\n"))
	return li;    

class TestMyCase(unittest.TestCase):
  
	@data(*read_phone())
  def test_1(self,phone):
  print("测试手机号注册流程：",phone)
```

```python
def read_userdata():
  li=[]
	with open("userdata.txt","r","utf-8") as f:
	  for line in f.readlines():
 		 	li.append(line.strip("\n").split(","))
	return li; 

class TestMyCase(unittest.TestCase):
  
	@data(*read_userdata())
  @unpack
  def test_1(self,username,pwd):
  print("测试手机号注册流程：",username,pwd)
```

### json文件

```json
[
	["admin",
 "123456"
  ],[
    "test",
    "00000"
 ]
]
```

```python
import json
 
    def read_jsondata(jsonfile):
        obj = json.load(open(jsonfile, "r"))
        if type(obj) == type("string"):
            return eval(obj)
        else:
            return obj
```

```json
[
  {
    "username":"abc",
    "pwd":"12345"
  }
]
```

key和形参一样可以直接进去

```json
[{"user_id": "9354801", "session_max": "11", "period": "1"},{"user_id": "9354801", "session_max": "11", "period": "2"}]
```

```python
import unittest
from ddt import ddt, data, unpack
import json

def read_userata():
    return json.load(open("../data/1.json", "r"))
@ddt
class TestMyCase(unittest.TestCase):
    # 单一参数
    # @data("18810001000", "13520002000")
    # def test_2(self, phone):
    #     print("测试手机号注册流程：", phone)
    #
    # 多个参数
    # @data({"user_id": "9354801", "session_max": "11", "period": "1"})
    # @unpack
    # def test_1(self, user_id, session_max,period):
    #     print("测试手机号注册流程：", user_id, session_max,period)

    # @data({"user_id": "9354801", "session_max": "11", "period": "1"},{"user_id": "9354801", "session_max": "11", "period": "1"})
    # @unpack
    # def test_1(self, **value):
    #     print("value：", value)


    @data(*read_userata())
    @unpack
    def test_1(self, **value):
        print("value：", value)


if __name__ == '__main__':
    unittest.main()
    #print("userdata",read_userata())
```



### yaml文件

1. 安装`PyYaml`库

2. 找到yaml文件

   ```yaml
   - 13711111111
   - 15822222222
   - 18833333333
   ```

3. ```python
   from ddt import file_data
   @ddt
   class TestMyCase(unittest.TestCase):
     
     @file_data("phone.yaml")
     def test_1(self,phone):
     	print("测试手机号注册流程：",phone)
     
   # 不需要额外的读取函数  
   ```

   

多参数

```yaml
- 
  username: admin
  pwd: 123456
- 
  username: test
  pwd: 0000  
```

```python

from ddt import file_data
@ddt
class TestMyCase(unittest.TestCase):
  
  @file_data("userdata.yaml")
	def test_1(self,username,pwd):
  	print("测试手机号注册流程：",username,pwd)

```

```python
from ddt import file_data
@ddt
class TestMyCase(unittest.TestCase):
  
  @file_data("userdata.yaml")
	def test_1(self,**userdata): #传入字典
  	print(userdata)
```



## 套件



```python
class TestMyCase1(uinttest.TestCase):
  def test_1(self):
    print("do1 test1")
  def test_2(self):
    print("do1 test2")
  def test_3(self):
    print("do1 test3") 
  def test_4(self):
    print("do1 test4") 
```

```python
class TestMyCase2(uinttest.TestCase):
  def test_1(self):
    print("do2 test1")
  def test_2(self):
    print("do2 test2")
  def test_3(self):
    print("do2 test3") 
  def test_4(self):
    print("do2 test4") 
```

```python
# 套件和执行器必须在独立的文件
import unittest
from MyTestCase1 import TestMyCase1
from MyTestCase2 import TestMyCase2
# 实例化套件
suite = unittest.TestSuite()
# 第一种添加测试套件的方法
suite.addTest(TestMyCase1("test_1"))
suite.addTest(TestMyCase1("test_2"))
suite.addTest(TestMyCase2("test_3"))


# 第二种添加方法
cases = [
  TestMyCase2("test_3"),
  TestMyCase1("test_4"),
  TestMyCase2("test_1")
]
suite.addTests(cases)


# 第三种 使用装载器
suite.addTests(unittest.TestLoader().loadTestsFromTestCase(TestMyCase1))
suite.addTests(unittest.TestLoader().loadTestsFromName("MyTestCase1.TestMyCase1"))


# 第四种 载入多个模块
module_path = "./" # 当前目录
discover = unittest.defaultTestLoader.discover(start_dir=module_path,pattern="MyTestCase*.py")
runner=unittest.TextTestRunner()
runner.run(discover)
# 实例化执行器
runner=unittest.TextTestRunner()
runner.run(suite)
```

## 生成测试报告

HTMLTestRunner 不是属于unittest

```python
import os
report_dir = "./reports/"
report_file = report_dir+"html_report.html"
# 判断目录是否存在
if not os.path.exists(report_dir):
  os.mkdir(report_dir)
  
with open(report-file,"wb") as rf:  
  module_path = "./" # 当前目录
	discover = unittest.defaultTestLoader.discover(start_dir=module_path,pattern="MyTestCase*.py")
  
  runner= HTMLTestRunner(title="测试标题"+description="描述"，stream=rf)
  runner.run(discover)
```


# Pytest 框架

## 一、需要安装的package

```txt
pytest
pytest-html 生成测试报告
pytest-xdist 测试用例分布式执行
pytest-ordering 改变测试用例执行顺序
pytest-rerunfailures 重新跑失败
allure-pytest 美观报告
```

## 二、使用

### 命名规则

1. Module名称必须命名为test_ 开头或以 _test结尾
2. 测试类必须以Test开头，并且不能有init方法
3. 测试方法必须以test开头

### 运行方式

1. 主函数模式

   - 运行所有`pytest.main()` 不只是一个module，能运行所有module

   - 参数 `pytest.main([参数])`

     ```python
     -s 输出调试信息，包括print打印信息
     -v 详细信息
     pytest.main(['-vs','mododulename.py']) 运行指定模块
     -n 多线程
     pytest.main(['-vs','mododulename.py',['-n=2']])
     --reruns 失败用例重新跑
     pytest.main(['-vs','mododulename.py',['-reruns=2']]) 失败用例重新跑两次
     -x 只要有一个出错就停止
     -x --maxfails==2 出现两个失败就停止
     -k 根据测试用例字符串指定测试用例 (根据方法/函数名)
     pytest -vs ./module.py -k "hello"
     ```

   ![image-20210707130153544](/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210707130153544.png)

   - 通过指定nodeid运行 `pytest.main(['-vs','dir/modulename.py::函数名'])`

2. 命令行模式

   - 运行所有 `pytest`
   - 指定模块`pytest -vs modulename.py`
   - 指定目录 `pytest -vs /dirname`
   - 指定nodeid `pytest -vs dir/modulename.py::函数名`
   - 多线程`pytest -vs modulename.py -n 2`

3. **通过读取pytest.ini 配置文件运行**

   在企业中使用。

   - 位置：项目根目录 `pytest.ini`

   - 名称：pytest.ini

   - 编码：必须是ANSI(使用notepad修改)

   - 作用：改变pytest默认行为

   - 运行规则：不管是主函数模式运行还是命令行都会去读文件

     ```ini
     [pytest]
     addopts = -vs
     #测试用例文件夹
     testpaths = ../testcase
     # 配置测试搜索的模块文件名称
     python_files = test*.piy
     # 测试类
     python_classes = Test*
     # 测试搜索的函数名
     python_functions = test
     ```

4. 指定顺序：

   在函数/方法名添加注解

   `@pytest.mark.run(order=1)`

5. 冒烟测试(分布在各个模块)

   添加注解

   `@pytest.mark.smoke` 添加到方法

```python
# 可以任意自定义mark的名称
markers = 
		smoke : 冒烟
    useroperation : 用户操作
    xfail : 预期失败
```

pytest -vs ./ -m "smoke or useroperation"

pytest -vs ./ -m "smoke "

6. 跳过测试用例

   - 无条件跳过

     `@pytest.mark.skip(reason=“原因”)`

   - 有条件跳过

     `@pytest.mark.skipif(条件,reason=“原因”)`

     

7. 测试结果

   | 类型（7） | 表示(6) | 说明                                          |
   | --------- | ------- | --------------------------------------------- |
   | PASSED    | **.**   | 测试通过                                      |
   | FAILED    | **F**   | 测试失败（fail或xpass与strict冲突造成的失败） |
   | SKIPPED   | **s**   | 测试未被执行                                  |
   | xfail     | **x**   | 预计测试失败，并且确实失败                    |
   | XPASS     | **X**   | 预计测试失败，但实际上运行通过，不符合预期    |
   | ERROR     | **E**   | 测试用例之外的触发代码异常                    |

   ```python
   import pytest
   #测试通过
   def test_passing():
       assert (1, 2, 3) == (1, 2, 3)
   
   #测试失败
   def test_failing():
       assert (1, 2, 3) == (3, 2, 1)
   
   #跳过不执行
   @pytest.mark.skip()
   def test_skip():
       assert (1, 2, 3) == (3, 2, 1)
   
   
   #预期失败，确实失败
   @pytest.mark.xfail()
   def test_xfail():
       assert (1, 2, 3) == (3, 2, 1)
   
   
   #预期失败，但是结果pass
   @pytest.mark.xfail()
   def test_xpass():
       assert (1, 2, 3) == (1, 2, 3)
   
   
   ```

   

8. 生成测试报告

   ```python
   [pytest]
   addopts = -vs --html ./report/report.html
   ```

   

## 三、fixture

`@pytest.fixture(scope = "function",params=None,autouse=False,ids=None,name=None)`

### 1. fixture 作用



### 2. 参数scope 控制作用范围

| 取值     | 范围   | 说明                                                         |
| -------- | ------ | ------------------------------------------------------------ |
| function | 函数级 | 每一个函数或方法都会调用                                     |
| class    | 类级别 | 每个测试类只运行一次                                         |
| module   | 模块级 | 每一个.py文件调用一次                                        |
| session  | 会话级 | 每次会话只需要运行一次，会话内所有方法及类，模块都共享这个方法 |

- scope=“function” 类似于setup()

```python
# fixture做为参数传入时，会在执行函数之前执行该fixture函数。再将值传入测试函数做为参数使用，这个场景多用于登录
import pytest
# 每一个方法都会调用login/logout
@pytest.fixture(scope="function")
def login():
  pass

@pytest.fixture(scope="function")
def logout():
  pass

class TestLogin:
    #传入lonin fixture
    def test_001(self, login):
        print("001传入了loging fixture")
        assert login == "account"

    #传入logout fixture
    def test_002(self, logout):
        print("002传入了logout fixture")

    def test_003(self, login, logout):
        print("003传入了两个fixture")

    def test_004(self):
        print("004未传入仍何fixture哦")

if __name__ == '__main__':
    pytest.main()

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201228194522623.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

即使fixture之间支持相互调用，但普通函数直接使用fixture是不支持的，一定是在测试函数内调用才会逐级调用生效
有多层fixture调用时，最先执行的是最后一层fixture，而不是先执行传入测试函数的fixture
上层fixture的值不会自动return,这里就类似函数相互调用一样的逻辑
**类似递归**

- scope="class" 类似setupClass()

  从第一次调用的位置作为前置方法执行，本类只执行一次。

- scope="module"

  **与class相同，只从.py文件开始引用fixture的位置生效**

- scope="session"

  scope = “session”：用法将在conftest.py文章内详细介绍

  session的作用范围是针对.py级别的，module是对当前.py生效，seesion是对多个.py文件生效
  session只作用于一个.py文件时，作用相当于module
  所以session多数与contest.py文件一起使用，做为全局Fixture

### 3. 参数params

- Fixture的可选形参列表，支持列表传入

- 默认None，每个param的值

- fixture都会去调用执行一次，类似for循环

- 可与参数ids一起使用，作为每个参数的标识，详见ids

- 被Fixture装饰的函数要调用是采用：Request.param(固定写法，如下图)
  举个栗子：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201228082811715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

```python
import pytest

@pytest.fixture(params=[1,2,"str",(1,2,3)])
def demo(request):
  return request.param

def test_1(demo):
  print(demo)
```

### 4. ids

- 用例标识ID

- 与params配合使用，一对一关系
  举个栗子：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201228083309450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

### 5. autouse

默认False

- 若为True，刚每个测试函数都会自动调用该fixture,无需传入fixture函数名
  由此我们可以总结出调用fixture的三种方式：
  　　1.函数或类里面方法直接传fixture的函数参数名称
  　　2.使用装饰器@pytest.mark.usefixtures()修饰
  　　3.autouse=True自动调用，无需传仍何参数，作用范围跟着scope走（谨慎使用）
  让我们来看一下，当autouse=ture的效果：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201229080533454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

### 6. name

- fixture的重命名
- 通常来说使用 fixture 的测试函数会将 fixture 的函数名作为参数传递，但是 pytest 也允许将fixture重命名
  如果使用了name,那只能将name传如，函数名不再生效
  调用方法：@pytest.mark.usefixtures(‘fixture1’,‘fixture2’)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201228085239409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

### 7. yield

```python
# -*- coding:utf-8 -*-
'''
@Author     : 测试工程师Jane
@FileName   : a.py
@Description:
'''
import pytest
@pytest.fixture()
def login():
    print("今天的笔记做完了吗？")
    yield
    print("今天的笔记做完啦！！！")

def test_01(login):
    print("我是用例一")
    
if __name__ == '__main__':
    pytest.main()


```

- 总结：

  一般的fixture 函数会在测试函数之前运行，但是如果 fixture 函数包含 yiled，那么会在 yiled处停止并转而运行测试函数，等测试函数执行完毕后再回到该 fixture 继续执行 yiled 后面的代码。
  **可以将 yield前面的代码看作是 setup，yield 后面的部分看作是 teardown 的过程。**
  无论是测试函数中发生了什么是成功还是失败或者 error 等情况，yiled 后面的代码都会被执行，yiled 中的返回数据也可以在测试函数中使用

  

### 8. usefixtures

`@pytest.mark.usefixtures(*fixturename)`

- 单个fixture：在被测试函数之前执行

```python
'''
@Author     : 测试工程师Jane
@FileName   : usefixture.py
@Description:
'''
import pytest
@pytest.fixture()
def one():
    print("我是一个fixture函数")

@pytest.mark.usefixtures('one')
def test_one_fixture():
    print("测试用例一")
    assert 1==1


```

- 多个fixture: 按传值先后顺序执行

```python
'''
@Author     : 测试工程师Jane
@FileName   : usefixture.py
@Description:
'''
import pytest
@pytest.fixture()
def one():
    print("我是第一个fixture函数")

@pytest.fixture()
def two():
    print("我是第二个fixture函数")
    
@pytest.mark.usefixtures('one','two')
def test_one_fixture():
    print("测试用例一")
    assert 1==1


```



- 叠加fixture: 自下至上执行，离测试函数越近的先被执行

```python
# -*- coding:utf-8 -*-
'''
@Author     : 测试工程师Jane
@FileName   : usefixture.py
@Description:
'''
import pytest
@pytest.fixture()
def one():
    print("我是第一个fixture函数")

@pytest.fixture()
def two():
    print("我是第二个fixture函数")

@pytest.mark.usefixtures('one')
@pytest.mark.usefixtures('two')
def test_one_fixture():
    print("测试用例一")
    assert 1==1


```



## 四、conftest.py

### 1. conftest.py是什么？

conftest.py是fixture函数的一个集合，可以理解为公共的提取出来放在一个文件里，然后供其它模块调用。不同于普通被调用的模块，conftest.py使用时不需要导入，Pytest会自动查找

### 2. conftest.py使用场景

如果我们有很多个前置函数，写在各个py文件中是不很乱？再或者说，我们很多个py文件想要使用同一个前置函数该怎么办？这也就是conftest.py的作用

### 3. conftest.py使用原则

conftest.py这个文件名是固定的，不可以更改。
conftest.py与**运行用例在同一个包下**，并且该包中有__init__.py文件使用的时候
不需要导入conftest.py，会自动寻找。

conftest.py

```python
# -*- coding:utf-8 -*-

import pytest
@pytest.fixture(scope="session")
def login():
    print("输入账号密码")
    yield
    print("清理数据完成")

```

Case 文件

```python
# -*- coding:utf-8 -*-

import pytest
class TestLogin1():
    def test_1(self, login):
        print("用例1")

    def test_2(self):
        print("用例2")

    def test_3(self, login):
        print("用例3")
if __name__ == '__main__':
    pytest.main()


```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201229084520343.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

- conftest.py的作用域与Python变量作用域相同
- 内层包内conftest.py不允许被其它包的测试类或方法使用，相当于本地变量
- 外层conftest.py可被内层测试类和方法使用，相当于全局变量

## 五、多参数

Pytest参数化有两种方式：
`@pytest.fixture(params=[])`
`@pytest.mark.parametrize()`
两者都会多次执行使用它的测试函数，但@pytest.mark.parametrize()使用方法更丰富一些

### 1. 定义

`@pytest.mark.parametrize(self,argnames, argvalues, indirect=False, ids=None, scope=None))：`

![image-20210708065950886](/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210708065950886.png)

### 2. argnames argvalues

- 单个参数单个值

  ```python
  @pytest.mark.parametrize('arg',[1])
  #测试函数要将argnames做为形参传入
  def test_one_params(arg):
      print("传入的值为：{}".format(arg))
      assert arg == 1
  ```

- 单个参数多个值

  ```python
  @pytest.mark.parametrize('arg',['abc',1,{'a':1,'b':3},(4,5)])
  def test_one_params(arg):
      print("传入的值为：{}".format(arg))
     # assert isinstance(arg,dict)
  ```

  ```txt
  test_py.py::test_one_params[abc] 传入的值为：abc
  PASSED
  test_py.py::test_one_params[1] 传入的值为：1
  PASSED
  test_py.py::test_one_params[arg2] 传入的值为：{'a': 1, 'b': 3}
  PASSED
  test_py.py::test_one_params[arg3] 传入的值为：(4, 5)
  PASSED
  ```

  

- 多个参数多个值

  其中多个值每个采用元组的方式

  ```python
  @pytest.mark.parametrize("test_input,expected",[("3+5",8),("5-2",1),("5*2",10)])
  def test_params(test_input,expected):
      print("原值：{} 期望值{}".format(test_input,expected))
      assert eval(test_input) == expected
  ```

### 3. indirect

indirect一般与Pytest的request、fixture组合使用
当indrect 为True时，argnames则要传入fixture函数名称，不再是一个普通参数，而是要被调用的fixture函数，argvalues则是要给这个函数传的值
作法其实与@pytest.fixture(params)一样，但使用了@pytest.mark.parametrize相当于参数化了fixture，而不是只有固定的一套数据传入使用
==indirect=True的用法与False用法基本是类似的，唯一的区别是，当它和True的时候会将参数传入fixture再进行一次前置处理，将处理后的返回值再给测试函数使用。False是直接使用。==

- 单fixture 单值

  ```python
  '''
  @Author     : 测试工程师Jane
  @FileName   : parametrizetest.py
  @Description:
  '''
  import pytest
  
  @pytest.fixture()
  def login(request):
      user = request.param
      print("传入的用户名为：{}".format(user))
      return user
  
  user = ['张三','李四']
  
  @pytest.mark.parametrize('login',user,indirect=True)
  def test_one_param(login):
      print("测试类的读到的用户是：{}".format(login))
  
  
  ```

  ```txt
  test_py.py::test_one_param[\u5f20\u4e09] 传入的用户名为：张三
  测试类的读到的用户是：张三
  PASSED
  test_py.py::test_one_param[\u674e\u56db] 传入的用户名为：李四
  测试类的读到的用户是：李四
  PASSED
  ```

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105173011545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

- 单fixture 多值

  **字典类**

```python
userinfo  = [
    {'user':'张三','pwd':123},
    {'user':'李四','pwd':456}
]

@pytest.fixture()
def login(request):
    # 读取单个字典，对单个字典操作
    user = request.param
    print("传入的用户名为：{},密码为:{}".format(user['user'],user['pwd']))
    return user

    # 参数为字典列表，读取字典列表
@pytest.mark.parametrize('login',userinfo,indirect=True)
def test_one_param(login):
    print("测试类的读到的用户是:{} 密码是:{}".format(login['user'],login['pwd']))
```

**元组类/数组类**

```python
userinfo  = [
    ('张三',123),
    ('李四',456)
  #  ['张三',123],
  #  ['李四',456]
]

@pytest.fixture()
def login(request):
    # 读取单个元组，对单个元组操作
    user = request.param
    print("传入的用户名为：{},密码为:{}".format(user[0],user[1]))
    # 返回一个元组
    return user

    # 参数为元组列表，读取元组列表
@pytest.mark.parametrize('login',userinfo,indirect=True)
def test_one_param(login):
    print("测试类的读到的用户是:{} 密码是:{}".format(login[0],login[1]))
```



- 多fixture 多值

  ```python
  '''
  @Time       : 2021/1/5 17:14
  @Author     : 测试工程师Jane
  @FileName   : paratwo.py
  @Description:
  '''
  
  import pytest
  #一个@pytest.mark.parametrize使用多个fixture，传入的数据要是嵌套了元组的列表
  userinfo  = [
      ('张三',123),
      ('李四','pwd')
  ]
  
  @pytest.fixture()
  def login_user(request):
      #读取一个值
      username = request.param
      print("传入的用户名为：{}".format(username))
      return username
  
  @pytest.fixture()
  def login_pwd(request):
      #读取一个值
      pwd = request.param
      print("密码为:{}".format(pwd))
      return pwd
  
    # 读取userinfo列表 发现每个里面有两列 拆分两列分别调用两个fixture
  @pytest.mark.parametrize('login_user,login_pwd',userinfo,indirect=True)
  def test_one_param(login_user,login_pwd):
      print("测试类的读到的用户是:{} 密码是:{}".format(login_user,login_pwd))
  
  
  ```

  

- 叠加fixture（自下而上对应）**（单值列表，执行次数笛卡尔集 N*M）**

```python

user = ['张三','李四']
pwd  = [124,345]

@pytest.fixture()
def login_user(request):
    user = request.param
    print("传入的用户名为：{}".format(user))
    return user

@pytest.fixture()
def login_pwd(request):
    pwd = request.param
    print("密码为:{}".format(pwd))
    return pwd

@pytest.mark.parametrize('login_pwd',pwd,indirect=True)
@pytest.mark.parametrize('login_user',user,indirect=True)
def test_one_param(login_user,login_pwd):
    print("测试类的读到的用户是:{} 密码是:{}".format(login_user,login_pwd))

```

### 4. ids

```python
ids=["case{}".format(i) for i in range(len(userinfo))]
@pytest.mark.parametrize('login_user,login_pwd', userinfo, ids=ids, indirect=True, scope='function')
```

### 5. scope

1. scope的作用范围取值与fixture scope一致，当indirect=True才会被使用
2. scope的作用范围会覆盖fixture的scope范围，如果同一个被调用的fixture有多个parametrize定义了scope,取第一条的范围，见下例：

详细见 https://blog.csdn.net/totorobig/article/details/112235358

### 6. 和pytest.param(参数，markers=pytest.mark.fail) 配合使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210106083536108.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG9yb2JpZw==,size_16,color_FFFFFF,t_70)

## 六、pytest.mark 使用

Pytest允许用户自定义自己的用例标签，用于将用例进行分组，以便在运行用例的时候筛选你想要运行的用例

### @pytest.mark.自定义标签的使用：

1. 可以标记测试方法、测试类，标记名可以自定义，最好起有意义的名字；
2. 同一测试类/方法可同时拥有多个标记；

```python
import pytest
 
@pytest.mark.login
class TestLogin:
    """登陆功能测试类"""
 
    @pytest.mark.smoke
    @pytest.mark.success
    def test_login_sucess(self):
        """登陆成功"""
 
        # 实现登陆逻辑
        pass
 
    @pytest.mark.failed
    def test_login_failed(self):
        """登陆失败"""
 
        # 实现登陆逻辑
        pass
 
 
@pytest.mark.logout
class TestLogout:
    """登出功能测试类"""
 
    @pytest.mark.smoke
    @pytest.mark.success
    def test_logout_sucess(self):
        """登出成功"""
 
        # 实现登出功能
        pass
 
    @pytest.mark.failed
    def test_logout_failed(self):
        """登出失败"""
 
        # 实现登出功能
        pass
```

运行标记

```python
#语法
pytest -m "自定义标签名" 
或：
pytest.main(['-m 自定义标签名'])
#示例：
pytest -m "smoke" testmark.py
```

### 组合运行用例

1. 使用 -m 参数运行标记的测试用例；
2. -m 参数支持 and、or 、not 等表达式；

```python
# 运行登陆功能的用例
pytest.main(['-m login'])
# 运行登出功能的用例
pytest.main(['-m logout'])
# 运行功能成功的用例
pytest.main(['-m success'])
# 运行功能失败的用例
pytest.main(['-m failed'])
# 运行登陆功能但是不运行登陆失败的测试用例
pytest.main(['-m login and not failed'])
# 运行登出功能但是不运行登出成功的测试用例
pytest.main(['-m logout and not success'])
# 运行登陆和登出的用例
pytest.main(['-m login or logout'])
```

注册 mark 标记：

- 首先在项目根目录创建一个文件 pytest.ini ，这个是 pytest 的配置文件； 然后在 pytest.ini 文件的
- markers 中写入你的 mark 标记， 冒号 “:” 前面是标记名称，后面是 mark 标记的说明，可以是空字符串；
- 注意：pytest.ini 文件中只能使用纯英文字符，绝对不能使用中文的字符（尤其是冒号和空格）！

```ini
[pytest]
markers = 
    login   : 'marks tests as login'
    logout  : 'marks tests as logout'
    success : 'marks tests as success'
    failed  : 'marks tests as failed'
```

规范使用 mark 标记：

注册完 mark 标记之后 pytest 便不会再告警，但是有时手残容易写错 mark 名，导致 pytest 找不到用例，一时想不开很难debug，尤其是团队协作时很容易出现类似问题，所以我们需要 “addopts = --strict” 参数来严格规范 mark 标记的使用！

在 pytest.ini 文件中添加参数 “addopts = --strict”；

```ini
[pytest]
markers =
    smoke   : 'marks tests as login'
    login   : 'marks tests as login'
    logout  : 'marks tests as logout'
    success : 'marks tests as success'
    failed  : 'marks tests as failed'
 
addopts = --strict
```

*注意要另起一行，不要在 markers 中添加；
添加该参数后，当使用未注册的 mark 标记时，pytest会直接报错：“ ‘xxx’ not found in `markers` configuration option ”，不执行测试任务；
==注意：pytest.ini 配置文件不支持注释，不支持注释，不支持注释…==

## 七、pytest.ini 详解

### 1. 查看设置选项

使用命令行输入`pytest -h` 即可查看

```python
[pytest] ini-options in the first pytest.ini|tox.ini|setup.cfg file found:
 
 
  markers (linelist):   markers for test functions
  empty_parameter_set_mark (string):
                        default marker for empty parametersets
  norecursedirs (args): directory patterns to avoid for recursion
  testpaths (args):     directories to search for tests when no files or directories are given in the command line.
  usefixtures (args):   list of default fixtures to be used with this project
  python_files (args):  glob-style file patterns for Python test module discovery
  python_classes (args):
                        prefixes or glob names for Python test class discovery
  python_functions (args):
                        prefixes or glob names for Python test function and method discovery
  disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                        disable string escape non-ascii characters, might cause unwanted side effects(use at your own
                        risk)
  console_output_style (string):
                        console output: "classic", or with additional progress information ("progress" (percentage) |
                        "count").
  xfail_strict (bool):  default for the strict parameter of xfail markers when not given explicitly (default: False)
  enable_assertion_pass_hook (bool):
                        Enables the pytest_assertion_pass hook.Make sure to delete any previously generated pyc cache
                        files.
  junit_suite_name (string):
                        Test suite name for JUnit report
  junit_logging (string):
                        Write captured log messages to JUnit report: one of no|system-out|system-err
  junit_log_passing_tests (bool):
                        Capture log information for passing tests to JUnit report:
  junit_duration_report (string):
                        Duration time to report: one of total|call
  junit_family (string):
                        Emit XML for schema: one of legacy|xunit1|xunit2
  doctest_optionflags (args):
                        option flags for doctests
  doctest_encoding (string):
                        encoding used for doctest files
  cache_dir (string):   cache directory path.
  filterwarnings (linelist):
                        Each line specifies a pattern for warnings.filterwarnings. Processed after -W and
                        --pythonwarnings.
  log_print (bool):     default value for --no-print-logs
  log_level (string):   default value for --log-level
  log_format (string):  default value for --log-format
  log_date_format (string):
                        default value for --log-date-format
  log_cli (bool):       enable log display during test run (also known as "live logging").
  log_cli_level (string):
                        default value for --log-cli-level
  log_cli_format (string):
                        default value for --log-cli-format
  log_cli_date_format (string):
                        default value for --log-cli-date-format
  log_file (string):    default value for --log-file
  log_file_level (string):
                        default value for --log-file-level
  log_file_format (string):
                        default value for --log-file-format
  log_file_date_format (string):
                        default value for --log-file-date-format
  faulthandler_timeout (string):
                        Dump the traceback of all threads if a test takes more than TIMEOUT seconds to finish. Not
                        available on Windows.
  addopts (args):       extra command line options
  minversion (string):  minimally required pytest version
 
 
environment variables:
  PYTEST_ADDOPTS           extra command line options
  PYTEST_PLUGINS           comma-separated plugins to load during startup
  PYTEST_DISABLE_PLUGIN_AUTOLOAD set to disable plugin auto-loading
  PYTEST_DEBUG             set to enable debug tracing of pytest's internals
to see available markers type: pytest --markers
to see available fixtures type: pytest --fixtures
(shown according to specified file_or_dir or current dir if not specified; fixtures with leading '_' are only shown with the '-v' option
```

2. addopts

   更改命令行默认选项

   ```ini
   [pytest]
   addopts = -v -reruns 1 --html=../report/report.html
   ```

3. testpaths

   pytest默认是搜索执行当前目录下的所有用例，当pytest.ini配置了testpaths = test_case/lxk或testpaths = test_case/lxk/test_001_case.py就会只执行当前配置的文件夹下或文件里的用例，这样我们就可以灵活的控制运行需要测试的用例了，可配置多个，空格隔开

   ```ini
   [pytest]
   testpaths = ./test_case/admin
   ```

4. norecursedirs

   和testpaths完全相反，是不执行哪些目录下的case

   ```ini
   [pytest]
   norecursedirs = ./test_case/admin
   ```

5. python_files

   运行模块名规则为test开头的模块

   ```ini
   [pytest]
   python_files = test*.py
   ```

6. python_classes

   默认运行类名为Test开头的模块

   ```ini
   [pytest]
   python_classes = Test*
   ```

7. python_functions

   默认运行函数名为test开头的函数

   ```ini
   [pytest]
   python_functions = test*
   ```

   

## 八、断言

1. 多断言

   - pytest断言assert
     一个测试用例中，可以有多个断言。

   ```python
   import pytest
   
   def test_01:
     
     assert 1 == 1
     assert 1 == 2
     assert 2 == 2
   ```

   但是遇到其中一个断言失败就不会继续下面的断言。

   - pytest.assume

   导入pytest-assume package

   ```python
   import pytest
   
   def test_02:
     	
     pytest.assume(1+4==5)
     pytest.assume(1+2==5)
     pytest.assume(1+3==4)
   ```

   即使遇到错误也会执行。

2. assert 断言方法

   - 采用assert断言时，可添加备注信息，当断言失败时，备注信息会以assertionerror抛出，并在控制台输出

     ```python
     def test_01:
       assert 1 == 2, "这里是失败返回信息"
     ```

3. 断言预期的异常

   `with pytest.raises(TypeError):`

   ```python
   # is_leap_year.py
    
   def is_leap_year(year):
       # 先判断year是不是整型
       if isinstance(year, int) is not True:        
           raise TypeError("传入的参数不是整数")    
       elif year == 0:        
           raise ValueError("公元元年是从公元一年开始！！")    
       elif abs(year) != year:        
           raise ValueError("传入的参数不是正整数")    
       elif (year % 4 ==0 and year % 100 != 0) or year % 400 == 0:
           print("%d年是闰年" % year)        
           return True
       else:
           print("%d年不是闰年" % year)        
           return False
   ```

   ```python
   import sys
   sys.path.append(".")
    
   import requests
   import pytest
   import is_leap_year
    
   class TestAssert():
       # 对一个判断是否是闰年的方法进行测试
       def test_exception_typeerror(self):
           with pytest.raises(TypeError):
               is_leap_year.is_leap_year('ss')    
       
       def test_true(self):
           assert is_leap_year.is_leap_year(400) == True
   ```

   - 将异常信息存储到变量中

   `with pytest.raises(ValueError) as excinfo:`

   变量的类型则为异常类，包含异常的type、value和traceback等信息

   `excinfo.value`

   `excinfo.type`

   `excinfo.traceback`

   ```python
   import sys
   sys.path.append(".")
    
   import requests
   import pytest
   import is_leap_year
    
   class TestAssert():
       def test_exception_value(self):
           with pytest.raises(ValueError) as excinfo:
               is_leap_year.is_leap_year(0)        
           
           assert "从公元一年开始" in str(excinfo.value)        
           assert excinfo.type == ValueError
   ```

   定义抛出的异常信息是否与预期的异常信息匹配，若不匹配则用例执行失败

   ```python
   import sys
   sys.path.append(".")
    
   import requests
   import pytest
   import is_leap_year
    
   class TestAssert():
       def test_exception_match(self):
           with pytest.raises(ValueError, match=r'公元33元年是从公元一年开始') as excinfo:
               is_leap_year.is_leap_year(0)        
    
           assert excinfo.type == ValueError
   
   
   ```

   
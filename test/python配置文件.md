# Python 配置文件

见的配置文件格式有很多中：ini、yaml、xml、properties、txt、py等。

## ini文件

### 1. ini文件格式

```ini
; 注释内容

[url] 	; section名称
baidu = http://www.baidu.com
port = 80

[email]
sender = 'xxx@qq.com'
```

### 2. 读取ini文件

使用configparser 

```python
import configparser


inifile='./settings.ini'

class ReadIni:
    def __init__(self,filepath):
				# 构造函数 创建一个对象，并且从指定路径读取文件
        self.conread = configparser.ConfigParser()
        self.conread.read(filepath, encoding='utf-8')
		# 获取所有section
    def getALLSections(self):
        return self.conread.sections()
		# 获取一个section 指定类型字典 还可以指定元组
    def getOneSection(self,target,type='dict'):
        items=self.conread.items(target)
        if type=='tuple' :
            return items
        if type=='dict':
            return dict(items)
		# 获取一option
    def getOneOption(self,section,option):
        return self.conread.get(section=section,option=option)


if __name__ == '__main__':
    url=ReadIni(inifile).getOneOption('url','port')
    print(url)
```


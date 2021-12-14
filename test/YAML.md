# YAML

### YAML 是什么

- YAML Ain't a Markup Language (YAML 不是一种标记语言)
- Yaml与Json类似，都是用来描述数据的
- Yaml有子格式化的特点，风格与Python一致
- Yaml与xml、json一样，具有自我描述性
- Yaml与unittest有着完美的支持

### Yaml 与Json对比

![image-20210606210314818](/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606210314818.png)

### Yaml的基础语法

1. 可以使用Unicode 建议使用utf-8
2. 使用==空白字符==（不能使用Tab）分层，同层元素==左侧对齐==
3. Yaml文件使用==.yml==或者==.yaml== 作为扩展名
4. 注释 ==#== 
5. 每个清单成员以单行表示，并用==短杠+空白(- )== 起始

6. 每个杂凑表成员用==冒号+空白(: )==分开key和value
7. 字符串一般不使用==引号==，但必要的时候可以用引号框住

### Yaml可以描述的数据

单个值： 数值、小数、文本、空值、真与假

列表：多个单个值或对象的排列 List

对象：键值对

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606211558298.png" alt="image-20210606211558298" style="zoom:50%;" />



<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606211818895.png" alt="image-20210606211818895" style="zoom:50%;" />



### 数据类型

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606212246678.png" alt="image-20210606212246678" style="zoom:50%;" />

### 其他语法

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210606212459776.png" alt="image-20210606212459776" style="zoom:50%;" />

### Json语法是yaml的子集

格式良好的JSON文件可以直接被解析


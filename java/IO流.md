# IO

## File 类

### 构造方法

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210604153926106.png" alt="image-20210604153926106" style="zoom:50%;" />

其中File(String pathname) 使用最多

### 方法

```java
file.createNewFile();  //创建一个文件 需要抛出异常
//权限
file.canExcute();
file.canRead();
file.canWrite();
//存在
file.exits();
//获取文件名称
file.getName();  //文件名称
file.getAbsolutePath();
file.getParent(); //获取父类名称
file.getCanonicalPath();  //返回文件路径的规范格式 不同系统不一样写法


File[] files = file.listFile();  //路径下所有文件的集合
File file2 = new File("/a")
file2.mkdir();
File file3 = new File("/c/d/e")
  file3.mkdirs();
```

```java
//递归遍历所有文件
//文件在遍历的时候，会出现空指针，原因在于当前文件受保护，没有权限访问
public static void printFile(File file){
  if(file.isDirectory()){
    File[] files=file.listFiles();
    for(File f:files){
      printFiles(f);
    }
  }else{
    System.out.println("此路径是一个具体文件")
  }
}
```

## 流的基本概念（Stream）

流是一串连串流动的字符，是以先进先出的方式发送信息的通道

流表示从一个文件写数据到另一个文件。

从一个文件读取数据到程序是输入流，从程序到文件是输出流。以程序作为参照物。

### Java流的分类1

- 按流向区分

  <img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210605124511813.png" alt="image-20210605124511813" style="zoom:50%;" />

- 按照处理数据单元划分

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210605124524325.png" alt="image-20210605124524325" style="zoom:50%;" />

字符是抽象概念

当编写IO流的时候，一定要注意==关闭流==

###字节流

#### InputStream

步骤：

	1. 选择合适的IO流对象
 	2. 创建对象
 	3. 传输数据
 	4. 关闭流对象

```java
//单个读取
InputStream inputStream = new fileInputStream("abc.txt"); //抽象类
int read = inputSream.read();  //只能读一个,是字符的ascii码
System.out.println((char)read);  //强制转换

//finally中
inputstream.close();
```

```java
//循环读取
InputStream inputStream = new fileInputStream("abc.txt"); //抽象类
int read = 0;
while(read = inputStream.read()!=-1){
  System.out.println((char)read);
}
```

```java
//添加缓冲区
//每次会讲所有数据添加到缓冲区，当缓冲区满了之后，一次读取，而不是每一个字节读取
InputStream inputStream = new fileInputStream("abc.txt"); //抽象类
int length = 0;
byte[] buffer = new byte[1024];  //新建一个缓冲区
while(length = inputStream.read(buffer)!=-1){
  System.out.println(new String(buffer,0,length);  //从0开始读
}
```

```java
//每五个一输出
InputStream inputStream = new fileInputStream("abc.txt"); //抽象类
int length = 0;
byte[] buffer = new byte[1024];  //新建一个缓冲区
while(length = inputStream.read(buffer,off:5,len:5)!=-1){
  System.out.println(new String(buffer,offset:5,length);  //从0开始读
}
```



#### OutputStream



```java
File file = new File("aaa.txt");
OutputStream outputStream=null;
try{
  outputStream = new FileOutputStream(file);
  outputStream.write(b:99);  //Ascii码为99的字符 c
  outputStream.write("hello".getBytes());
}catch(){
  
}finally{
  outputStream.close();
}
```

#### 文件复制

```java
File src = new File("abc.txt");
File dst = new File("aaa.txt");
InputStream inputStream =null;
OutputStream outputStream = null;
try{
  inputStream = new FileInputStream(src);
  outputStream = new FileOutputStream(dst);
  
  //带缓存的输入输出方式
  byte[] buffer= new byte[1024];
  while(inputStream.read(buffer)!=-1)
  {
    outputStream.write(buffer);
  }
}catch(){
  
}finally{
  outputStream.close();
  inputStream.close();
}
```

#### ByteArrayInputStream

包含一个内部缓冲区，其中包含可以从流中读取的字节

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210605153145514.png" alt="image-20210605153145514" style="zoom:50%;" />

```java
				String str = "www.mashibing.com";
        byte[] buffer = str.getBytes();   //字符串转为bytes流
        ByteArrayInputStream byteArrayInputStream = null;
        byteArrayInputStream = new ByteArrayInputStream(buffer);  //bytes流传给ByteArrayInputStream
        int read = 0;
        while((read = byteArrayInputStream.read())!=-1){   
            byteArrayInputStream.skip(4);
            System.out.println((char)read);
        }
        try {
            byteArrayInputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
```

#### ByteArrayOutputStream

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210605153208213.png" alt="image-20210605153208213" style="zoom:50%;" />

```java
				ByteArrayOutputStream byteArrayOutputStream = null;
        byteArrayOutputStream = new ByteArrayOutputStream();
        byteArrayOutputStream.write(123);
        try {
            byteArrayOutputStream.write("www.mashibing.com".getBytes());   //输出到缓冲区
            System.out.println(byteArrayOutputStream.toString());   //转换为字符串输出
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                byteArrayOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```



### 字符流

#### Reader

```java
Reader reader = null;
try{
  reader = new FileReaser("abc.txt");
  int read = 0;
  while((read = reader.read()!=-1){
    System.out.println(new String(char)read)); 
  } 
}catch(){
  
}finally{
  reader.close();
}
```

```java
Reader reader = null;
try{
  reader = new FileReaser("abc.txt");
  int read = 0;
  char[] buffer = new char[1024];
  while((read = reader.read(buffer))!=-1){
    System.out.println(new String(buffer,0,read)); 
  } 
}catch(){
  
}finally{
  reader.close();
}
```

#### Writer

什么时候要加flush

最保险的方式，在输出流关闭之前每次都flush，再关闭

当某一个输出流对象中带有缓冲区的时候，需要进行flush

```java
Writer writer=null;
try{
  writer = new File("aaa.txt");
  writer.write("hello ");
  writer.write("world");
  
  writer.flush();  
  
}catch(){
  
}finally{
  writer.close();
}
```

#### CharArrayReader

```java
				char[] chars="哈哈哈哈".toCharArray();   //字符数组
        CharArrayReader charArrayReader=new CharArrayReader(chars);  //将字符数组存入对象
        try {
            int read=0;
            while((read = charArrayReader.read())!=-1){  //读取全部
                System.out.println((char)read);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        finally {
            charArrayReader.close();
        }
```

#### CharArrayWriter

```java
 CharArrayWriter charArrayWriter = new CharArrayWriter();
        try {
            charArrayWriter.write("aaa");
            charArrayWriter.write("bbb");
            charArrayWriter.flush();
            System.out.println(charArrayWriter);
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            charArrayWriter.close();
        }
```

#### FilterReader

#### FilterWriter

#### BufferedReader

```java
BufferedReader reader = null;

        try {
            reader = new BufferedReader(new FileReader("aaa.txt"));
           String read;
            while((read=reader.readLine())!=null) {
                System.out.println(read);
            } 
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```



#### BufferedWriter

```java
BufferedWriter bufferedWriter=null;
        FileWriter fileWriter=null;
        try {
            fileWriter = new FileWriter(new File("abc.txt"));
            bufferedWriter=new BufferedWriter(fileWriter);
            bufferedWriter.write("aaaa");
            bufferedWriter.newLine();
            bufferedWriter.append("bbbb");
            bufferedWriter.flush();

        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                bufferedWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            fileWriter.close();
        }
```







### Java流的分类2

- 按功能区分

  节点流：可以直接从数据源或目的地读写数据。

  处理流(包装流)：不直接连接到数据源活目的地，而是其他流进行封装。目的是简化操作和提高性能

  - 关系

    节点流处于IO操作的第一线，所有操作必须通过他们执行

    处理流可以对其他流进行处理（提高效率活操作灵活性）

    <img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210605135528321.png" alt="image-20210605135528321" style="zoom:50%;" />

#### 处理流



#### OutputStreamWriter

```java
File file = new File("abc.txt");
        OutputStreamWriter outputStreamWriter = null;
        FileOutputStream fileOutputStream = null;


        try {
            long time = System.currentTimeMillis();
            fileOutputStream = new FileOutputStream(file);
            outputStreamWriter = new OutputStreamWriter(fileOutputStream,"iso8859-1");
            outputStreamWriter.write(99);
            outputStreamWriter.write("马士兵教育");
            outputStreamWriter.write("abcdefg",0,5);
            long end = System.currentTimeMillis();
            System.out.println(end-time);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //关闭流对象的时候，建议按照创建的顺序的逆序来进行关闭
            try {
                outputStreamWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }


 
```

####InputStreamReader

```java
File file = new File("abc.txt");
        FileInputStream fileInputStream = null;
        InputStreamReader inputStreamReader = null;

        try {
            fileInputStream = new FileInputStream(file);
            inputStreamReader = new InputStreamReader(fileInputStream);
            //为什么没有用循环的方式，因为数据比较少，无法占用1024个缓存区，只需要读取一次即可
            char[] chars = new char[1024];
            int length = inputStreamReader.read(chars);
            System.out.println(new String(chars,0,length));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                inputStreamReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

#### 控制台读入写出

```java
//写在try里不需要再close了      
try(InputStreamReader inputStreamReader = new InputStreamReader(System.in);
            OutputStreamWriter outputStreamWriter = new OutputStreamWriter(System.out);
            BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
            BufferedReader bufferedReader = new BufferedReader(inputStreamReader);) {
            String str = "";
            while (!str.equals("exit")) {

                str = bufferedReader.readLine();
                bufferedWriter.write(str);
                bufferedWriter.flush();

            }
        } catch (IOException e) {
            e.printStackTrace();
        }
```

#### RandomAccessFile

```java
public class RandomAccessFileTest {

    public static void main(String[] args) {
        File file = new File("doc.txt");
        //整个文件袋大小
        long length=file.length();
        //块的大小
        int blockSize=1024;
        //文件可以被切分成多少个块
        int size=(int)Math.ceil(length*1.0/blockSize);
        System.out.printf("要被切成《%d》个块",size);
        int beginPos = 0;
        int actualSize = (int)(blockSize>length?length:blockSize);
        for(int i=0;i<size;i++)
        {
            //每次获取块的时候起始偏移量
            beginPos=i*blockSize;
            if(i==size-1)
            {
                actualSize=(int)length;
            }else{
                actualSize=blockSize;
                length=actualSize;
            }
            System.out.println(i+"----》起始位置是"+beginPos+"-----》读取的大小是"+actualSize);
        }
    }
    public static void readSplit(int i,int beginPos,int actualSize){

        RandomAccessFile randomAccessFile=null;
        try {
            randomAccessFile = new RandomAccessFile(new File("doc.txt"),"r");
            randomAccessFile.seek(beginPos);
            byte[] bytes=new byte[1024];
            int length=0;
            while((length=randomAccessFile.read(bytes))!=-1)
            {
                if (actualSize>length){
                    System.out.println(new String(bytes,0,length));
                    actualSize-=length;

                }else
                {
                    System.out.println(new String(bytes,0, actualSize));
                    break;
                }
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                randomAccessFile.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```



## 总结



![IO流分类](/Users/zhichaozhang/Study/Java/资料汇总/javase/note/IO流分类.png)


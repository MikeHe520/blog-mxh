# IO流（韩顺平版）

## 1.文件

### 1.1文件的概念：

文件是保存数据的地方

- 文件流

文件在程序中是以流的形式来操作

**流：**数据在文件(数据源)和内存(程序)之间经历的路程

**输入流：**数据从文件(数据源)到内存(程序)的路径

**输出流：**数据从内存(程序)到文件(数据源)的路径

![image-20221119184434638](IO.assets/image-20221119184434638.png)

### 1.2创建文件

- 创建文件对象相关构造器和方法
  - new File(String pathname)  //根据路径构建一个File对象
  - new File(File parent,String child)  //根据父目录文件+子路径构建
  - new File(String parent,String child)  //根据父目录+子路径构建
  - createNewFile创建新文件

![image-20221119195207049](IO.assets/image-20221119195207049.png)

![image-20221119195222411](IO.assets/image-20221119195222411.png)

![image-20221119195244126](IO.assets/image-20221119195244126.png)

### 1.3获取文件信息

![image-20221119205327995](IO.assets/image-20221119205327995.png)

![image-20221119205340323](IO.assets/image-20221119205340323.png)

## 2.IO流

### 2.1java IO流的原理

1. I/O是Input/Output的缩写，I/O技术是非常实用的技术，用于处理数据传输；
2. java程序中，对于数据的输入/输出操作以”流(stream)“的方式进行
3. java.io包下提供了各种”流“类和接口，用以获取不同种类的数据，并通过方法输入或输出数据
4. 输入input：读取外部数据（如光盘）到程序(内存)中
5. 输出output：将程序(内存)数据输出到（如光盘）存储设备中

### 2.2流的分类

- 按操作数据单位不同分为：字节流(8bit)，字符流(按字符)
- 按数据流的流向不同分为：输入流、输出流
- 按流的角色的不同分为：节点流、处理流/包装流

| （抽象基类） | 字节流       | 字符流 |
| ------------ | ------------ | ------ |
| 输入流       | InoutStream  | Reader |
| 输出流       | OutputStream | Writer |

### 2.2.1InputStream：字节输入流

- Inputstream抽象类是所有类字节输入流的超类
- InputStream常用的子类
  1. FileInputStream常用的子类
  2. BufferedInputStream：缓冲字节输入流
  3. ObjectInputStream：对象字节输入流

```java
/*
单个字节的读取，效率比较低
 */

@Test
public void readFile01() throws IOException {
    //创建文件路径
    String filePath = "E:\\hello.txt";
    FileInputStream fileInputStream = null;
    int readData = 0;
    try {
        //创建fileInputStream用于读取文件
        fileInputStream = new FileInputStream(filePath);
        //从该输入流读取一个字节的数据，若没有输入可用，此方法阻止
        //返回-1表示文件读取完毕
        while ((readData = fileInputStream.read()) != -1){
            System.out.print((char)readData);
        }

    } catch (Exception e) {
        e.printStackTrace();
    }finally {
        //fileInputStream是一种“流”，是一种资源，不关闭会造成资源浪费
        //关闭文件流，释放资源
        fileInputStream.close();
    }

}
```

```java
//    使用read(byte[] b)

    @Test
    public void readFile02() throws IOException {
        //创建文件路径
        String filePath = "E:\\hello.txt";
        //字节数组
        byte[] buf = new byte[8];
        int readLen = 0;
        FileInputStream fileInputStream = null;

        try {
            //创建fileInputStream用于读取文件
            fileInputStream = new FileInputStream(filePath);
            //从该输入流读取一个字节的数据，若没有输入可用，此方法阻止
            //返回-1表示文件读取完毕
            //若读取正常，返回实际读取的字节数
            while ((readLen = fileInputStream.read(buf)) != -1){
                System.out.print(new String(buf,0,readLen));
            }

        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            //fileInputStream是一种“流”，是一种资源，不关闭会造成资源浪费
            //关闭文件流，释放资源
            fileInputStream.close();
        }

    }
```

### 2.2.2OutputStream字节输出流

- outputstream抽象类是所有类字节输入流的超类

```java
/*
演示使用FileOutputStream将数据写到文件中
若该文件不存在，则创建该文件
 */
@Test
public void write01() throws IOException {
    //创建outputStream对象
    String filePath = "E:\\a.txt";
    FileOutputStream fileOutputStream = null;

    try {
        //得到fileOutputStream对象
        fileOutputStream = new FileOutputStream(filePath);

        //方法1写入一个字节
        fileOutputStream.write('a');

        //方法2写入多个字节-->字符串
        String str = "apple";
        //str.getBytes()可以把字符串转成字节数组
        fileOutputStream.write(str.getBytes());

        //方法3write(byte[] b,int off,int len)
        fileOutputStream.write(str.getBytes(),0,str.length());

    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }finally {
        fileOutputStream.close();
    }
}
```

```java
public static void main(String[] args) throws IOException {

    /*拷贝文件，将E:\\vue-cli.png文件拷贝到d盘
    思路分析：
    1.创建文件的输入流，见文件读取到程序
    2.创建文件的输出流，将读取到的文件数据，写到指定的文件
     */

    String srcFliePath = "E:\\vue-cli.png";
    String destFliePath = "D:\\vue-cli.png";

    FileInputStream fileInputStream = null;
    FileOutputStream fileOutputStream = null;

    try {
        fileInputStream = new FileInputStream(srcFliePath);
        fileOutputStream = new FileOutputStream(destFliePath);

        //定义一个字节数组，提高读取效率
        byte[] buf = new byte[1024];
        int readLen = 0;
        while ((readLen = fileInputStream.read(buf)) != -1){
            //读取到后，就写入文件
            fileOutputStream.write(buf,0,readLen);  //一定要使用此方法读取
        }
        System.out.println("拷贝成功");

    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (fileInputStream != null){
            fileInputStream.close();
        }
        if (fileOutputStream != null){
            fileOutputStream.close();
        }
    }

}
```

### 2.2.3IO流的常用类

- FileReaderr介绍
  - 字符流
  - 相关方法：
    1. new FileReader(File/String)
    2. read：每次读取单个字符，返回该字符，若到文件末尾返回-1
    3. read(char[])：批量读取多个字符到数组，返回读取到的字符数，若到文件末尾返回-1
  - 相关API
    1. new String(char[])：将char[]转换成String
    2. new String(char[],off,len)：将char[]的指定部分转换成String

```java
@Test
public void fileReader1() throws IOException {
    //1.创建fILEReader对象
    String filePath = "E:\\story.txt";
    FileReader fileReader = null;
    int data = 0;
    try {
        fileReader = new FileReader(filePath);
        //循环读取 read
        while ((data = fileReader.read()) != -1){
            System.out.print((char) data);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (fileReader != null){
            fileReader.close();
        }
    }
}
```

```java
@Test
public void fileReader2() throws IOException {
    //1.创建fILEReader对象
    String filePath = "E:\\story.txt";
    FileReader fileReader = null;
    int readLen = 0;
    char[] buf = new char[8];
    try {
        fileReader = new FileReader(filePath);
        //循环读取 read(buf)，返回的是实际读取到的字符数
        //若返回-1，说明文件读取完成
        while ((readLen = fileReader.read(buf)) != -1){
            System.out.print(new String(buf,0,readLen));
        }
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (fileReader != null){
            fileReader.close();
        }
    }
}
```

- FileWriter介绍
  - 常用方法
    1. new FileWriter(File/String)：覆盖模式，相当于流的指针在首段
    2. new FileWriter(File/String，true)：追加模式，相当于流的指针在尾端
    3. write(int)：写入单个字符
    4. write(char[])：写入指定数组
    5. write(char[],off,len)：写入指定数组的指定部分
    6. write(string)：写入整个字符串
    7. write(string,off,len)：写入字符串的指定部分
  - 相关API
    1. String类：toCharArray：将String转换成char[]
  - 注意事项：
    - FileWriter使用后，必须要关闭或刷新，否则写入不到指定的文件

```java
 @Test
    public void Writer1() throws IOException {
        //1.创建一个FileWriter对象
        String filePath = "E:\\note.txt";
        FileWriter writer = null;
        char[] chars = {'a','b','c','d','e','f'};

        try {
            writer = new FileWriter(filePath);
//            1. write(int)：写入单个字符
            writer.write('h');
//            2. write(char[])：写入指定数组
            writer.write(chars);
//            3. write(char[],off,len)：写入指定数组的指定部分
            writer.write("码小贺学java",0,3);
//            4. write(string)：写入整个字符串
            writer.write(" 码小贺学java");
//            5. write(string,off,len)：写入字符串的指定部分
            writer.write("长沙市雨花区",0,3);
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //对应FileWriter，要close或flush
            writer.close();
        }
        System.out.println("文件创建成功");
    }
```

### 2.2.4节点流和处理流

1. 节点流可以从一个特定的数据源读写数据（如FileReader、FileWriter）
2. 处理流/包装流是“连接”已存在的流（节点流/处理流）之上，为程序提供更为强大的读写功能（如BufferedReader、BufferedWriter）

![image-20221120163600119](IO.assets/image-20221120163600119.png)

- 节点流和处理流的区别和联系
  1. 节点流是底层流/低级流，直接与数据源相接
  2. 处理流/包装流包装节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入输出
  3. 处理流/包装流对节点流进行包装，使用了修饰器设计模式，不会直接与数据源相连(模拟修饰器设计模式)
  
- 处理流的功能主要体现在以下两个方面：
  1. 性能的提高：主要以增加缓冲的方式来提高输入输出的效率
  2. 操作的便捷：处理流可能提供了一系列便捷的方法来一次输入输出大批量的数据，使用更加灵活方便
  
- 处理流BufferedInputStream和BufferedOutputStream（可以完成二进制文件传输）

  - 介绍BufferedInputStream
    - 字节流，在创建BufferedInputStream，会创建一个内部缓冲区数组


  ![image-20221121151944462](IO.assets/image-20221121151944462.png)

  - 介绍BufferedOutputStream
    - 字节流，实现缓冲的输出流，可以将多个字节写入底层输出流中，而不必对每次字节写入调用底层系统


![image-20221121152045341](IO.assets/image-20221121152045341.png)

- 对象流ObjectInputStream(序列化)和ObjectOutputStream(反序列化)
  - 序列化和反序列化
    1. 序列化：在保存数据时，保存数据的值和数据类型
    2. 反序列化：恢复数据的值和数据类型
    3. 需要让某个对象支持序列化机制，则必须让其类是可序列化的，为了让某个类是可序列化的，该类必须实现以下两个接口：
       - Serializable(标记接口)
       - Externalizable(该接口有方法需要实现，一般用上面的接口)

![image-20221121184642753](IO.assets/image-20221121184642753.png)

![image-20221121184732814](IO.assets/image-20221121184732814.png)

- 注意事项和细节说明
  1. 读写顺序一致
  2. 要求实现序列化或反序列化对象，需要实现Serializable
  3. 序列化的类中建议添加SerialVersionUID，为了提高版本的兼容性
  4. 序列化对象时，默认将里面所有属性都进行序列化，但除了static或transient修饰的成员
  5. 序列化对象时，要求里面属性的类型也需要实现序列化接口
  6. 序列化具备可继承性，也就是某类已实现了序列化，则该类的所有子类已经默认实现了序列化

```java
public class ObjectOutputStream_ {
    public static void main(String[] args) throws IOException {
        //序列化后保存的文件格式
        String filePath = "E:\\data.java";

        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filePath));

        //序列化数据到E:\\data.java
        oos.writeInt(100);              //int-->Integer包装(实现了Serializable接口)
        oos.writeBoolean(true);      //boolean-->Boolean包装(实现了Serializable接口)
        oos.writeChar('a');          //char-->Character包装(实现了Serializable接口)
        oos.writeDouble(9.9);        //double-->Double包装(实现了Serializable接口)
        oos.writeUTF("码小贺学java"); //String-->UTF包装(实现了Serializable接口)

        //保存一个dog对象
        oos.writeObject(new Dog("大黄",8));

        oos.close();
        System.out.println("数据保存完毕（序列化形式）");

    }
}

class Dog implements Serializable{
    private String name;
    private int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```java
public class ObjectInputStream_ {
    public static void main(String[] args) throws Exception {
        //指定反序列化的文件
        String filePath = "E:\\data.java";

        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filePath));

        //读取(反序列化)的顺序需要和你保存数据(序列化)的顺序一致，否则会出现异常
        System.out.println(ois.readInt());
        System.out.println(ois.readBoolean());
        System.out.println(ois.readChar());
        System.out.println(ois.readDouble());
        System.out.println(ois.readUTF());

        Object dog = ois.readObject();
        System.out.println("运行类型" + dog.getClass());
        System.out.println("dog信息=" + dog);

        //关闭流
        ois.close();
    }
}
```

- 标准输入输出流
  1. System.in标准输入  InputStream  键盘
  2. System.out标准输出 PrintStream  显示器

- 转换流
  1. InputStreamReader
     - Reader的子类，将InputStream（字节流）包装成（字符流）
  2. OutputStreamWriter
     - Writer的子类，实现将OutputStream（字节流）包装成Writer（字符流）
  3. 当处理纯文本数据时，如果使用字符流效率更高，并且可以有效解决中文问题，所以建议将字节流转换成字符流
  4. 可以在使用时指定编码格式（如：utf-8、gbk）

![image-20221122205238882](IO.assets/image-20221122205238882.png)

![image-20221122205518454](IO.assets/image-20221122205518454.png)

```java
public class InputStreamReader_ {
    public static void main(String[] args) throws Exception {
        String filePath = "E://a.txt";
        BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(filePath),"utf-8"));
        String s = br.readLine();
        System.out.println("读取到的内容="+s);
        br.close();
    }
}
```

- 打印流PrintStream和PrintWriter
  - 打印流只有输出流，没有输入流

![image-20221122220107192](IO.assets/image-20221122220107192.png)

![image-20221123140545113](IO.assets/image-20221123140545113.png)

```java
public static void main(String[] args) throws IOException {
    PrintStream out = System.out;
    //在默认请况下，PrintStream输出数据的位置是标准输出，即显示器
    out.print("john,hello");
    //print的底层使用的是Writer，所以可以直接调用writer进行打印/输出
    out.write("码小贺，你好".getBytes());
    out.close();

    //修改打印流输出的位置/设备
    System.setOut(new PrintStream("E:\\f1.txt"));
    System.out.println("hello,码小贺学java");
}
```

### 2.3Properties类

- 介绍
  1. 专门用于读写配置文件的集合类
     - 配置文件的格式：
     - 键=值
     - 键=值
  2. 注意：键值对不需要有空格，值不需要用引号一起来。默认类型是String
- 常见方法
  - load：加载配置文件的键值对到Properties对象
  - list：将数据显示到指定设备
  - getProperties(key)：根据键获取值
  - setPeoperties(key,value)：设置键值对到Properties对象
  - store：将Properties中的键值对存储到配置文件，在idea中，保存信息到配置文件中，如果含有中文，会存储为unicode码

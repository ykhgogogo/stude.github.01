# IO流

## 文件

文件就是保存数据的地方

### 文件流

文件在程序中是以流的形式来操作的

流：数据在数据源（文件）和程序（内存）之间经历的路径

输入流：数据从数据源（文件）到程序（内存）的路径

输入流：数据从程序（内存）到数据源（文件）的路径

**输入和输出是针对Java程序而言的**，类似有人喝水，人吐水

![image-20221001195423045](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20221001195423045.png)

### 常用的文件操作

#### 创建文件相关的构造器和方法

1. ```java
   new File(String pathname)//根据路径创建一个File对象
   ```

2. ```java
   new File(File parent,String child)
   //根据父目录文件+子路径
   ```

3. ```java
   new File(String parent,String child)
   //根据父目录+子路径创建
   ```

**注意点：new File()操作只是在内存中创建了一个文件，并没有输出，没有在磁盘中创建对应的文件，要想在磁盘中创建出对应的文件名，需要调用文件的createNewFile()方法，才会把创建的文件写入磁盘**

三种创建方式得代码演示

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.IOException;

public class IODemo01 {
    public static void main(String[] args) {

    }

    @Test
    public void creatfile01() {
        String filename01 = "d:\\creatfile01";
        File file01 = new File(filename01);
        try {
            boolean newFile01 = file01.createNewFile();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    @Test
    public void craetfile02() {
        File parentfile = new File("d:\\");
        File creatfile02 = new File(parentfile, "creatfile02");
        try {
            creatfile02.createNewFile();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    @Test
    public void creatfile03() {
        String parentfilename = "d:\\";
        String filename = "creatfile03";
        File file03 = new File(parentfilename, filename);
        try {
            file03.createNewFile();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

#### 获取文件信息

会查询即可

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.File;

public class IODemo02 {
    public static void main(String[] args) {

    }

    @Test
    public void information() {
        File file = new File("d:\\creatfile01");
        System.out.println("文件名 = " + file.getName());
        System.out.println("文件绝对路径 = " + file.getAbsolutePath());
        System.out.println("文件父级目录 = " + file.getParent());
        System.out.println("文件大小（字节） = " + file.length());
        System.out.println("文件是否存在 = " + file.exists());
        System.out.println("是否是文件 = " + file.isFile());
        System.out.println("文件是不是目录 = " + file.isDirectory());
    }
}
```

#### 目录的操作和文件的删除

mkdir创建一级目录、mkdirs创建多级目录、delete删除空目录或文件

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.File;

public class IODemo03 {
    public static void main(String[] args) {

    }

    //判断文件是否存在，存在就删除文件
    @Test
    public void delet01() {
        File file = new File("d:\\creatfile02");
        if (file.exists()) {
            if (file.delete()) {
                System.out.println("删除成功");
            } else {
                System.out.println("删除失败");
            }
        } else {
            System.out.println("文件不存咋");
        }
    }

    //判断目录是否存在，存在就删除
    //在Java编程中，目录也被当初文件
    @Test
    public void delet02() {
        File file = new File("d:\\demo02");
        if (file.exists()) {
            if (file.delete()) {
                System.out.println("删除成功");
            } else {
                System.out.println("删除失败");
            }
        } else {
            System.out.println("目录不存咋");
        }
    }

    //判断目录是否存在，如果存在就提示存在，不存在就创建
    @Test
    public void delet03() {
        File directoryPath = new File("d:\\demo02\\a\\b");
        if (directoryPath.exists()) {
            System.out.println("该目录存在");
        } else {
            if (directoryPath.mkdirs()) {
                System.out.println("创建成功");
            }
        }
    }
}
```

## IO流原理和流的分类

### Java IO流原理

- I/O是input和output的缩写，I/O技术是非常实用的技术，用于处理数据传输
- Java程序中，对于数据得输入/输出操作以流的方式进行
- java.io包下提供了各种流的接口和类，用以获取不同种类的数据，并通过方法输入或输出数据
- 输入流：读取外部数据(磁盘、光盘等存储设备的数据)到程序（内存）中
- 输出流：将程序（内存）中数据输出到磁盘、光盘等存储设备中
- ![image-20221002092337969](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20221002092337969.png)

---

### 流的分类

- 按操作数据单位不同分为：字节流（8 bit）是按照二进制操作的，可以保准无损传输；字符流（按字符）文本文件，传输效率更高
- 按数据流的流向不同分为：输入流和输出流
- 按流的角色的不同分为：节点流，处理流/包装流

字节流中 输出流父类是：InputStream

​				 输出流父类是：OutputStream

字符流中 输出流父类是：Reader

​				 输出流父类是：Writer

**总结：以上四个都是抽象类，只能通过子类对象实例化**

- 流和文件：相当于快递员送快递的过程，快递员从快递中心取出快递，送往用户手中，类似于输入流的过程；用户让快递员寄出快递的过程类似

## 节点流和处理流

## 输入流和输出流

继承关系图

![image-20221002104526747](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20221002104526747.png)

### 字节输入流

#### InputStream

InputStream抽象类是所有类字节输入流的超类

InputStream常见的子类有：

FileInputStream：文件输入流

BufferInputStream：缓冲字节流

ObjectInputStream：对象字节流

---

##### FileInputStream（文件输入流）

FileInputStream是InputStream子类，文件 →程序过程

###### 读取文件read方法

- ```java
  read();
  ```

- ```java
  read(byte[] b);
  ```

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.FileInputStream;
import java.io.IOException;

public class InputStreamDemo01 {
    public static void main(String[] args) {

    }

    //read()单个字节读取，效率低，所以引出第二种方法read(byte[] b)
    @Test
    public void FileRead01() {
        String Filepath = "d:\\helloworld.txt";//读取的文件路径
        int readchar = 0;
        //定义在外面扩大作用域
        FileInputStream fileInputStream = null;
        try {
            //创建了FileInputStream对象以后用于读取Filepath文件
            fileInputStream = new FileInputStream(Filepath);
            //读取文件内容
            //调用read()方法会把字节内容返回int类型数据，从该输入流中读取一个字节的数据，如果没有输入可用，方法阻止
            //如果返回-1，表示读取完了
            //通过循环把所有内容读取到
            while ((readchar = fileInputStream.read()) != -1) {
                System.out.print((char) readchar);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //finally中一定要关闭流，流对象是资源，关闭是为了避免资源浪费
            //写代码过程中发现fileInputStream如果在try中定义必定无法在finally中访问，因此需要定义在外面
            try {
                fileInputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
    //在以上代码中helloword.txt文件不能包含汉字，因为UTF-8中一个汉字是三个字节，read()方法中是一个一个自己
    //读取，所以必然会导致输出结果是乱码

    //read(byte[] b)读取方法提高效率
    @Test
    public void FileRead02() {
        String Filepath = "d:\\helloworld.txt";//读取的文件路径
        int readlength = 0;
        //定义在外面扩大作用域
        //首先定义字节数组
        byte[] b = new byte[8];//表示一次读取8个字节
        FileInputStream fileInputStream = null;
        try {
            //创建了FileInputStream对象以后用于读取Filepath文件
            fileInputStream = new FileInputStream(Filepath);
            //读取文件内容，如果返回-1，表示读取完了
            //根据定义的字节数组的长度循环读取
            //fileInputStream.read(byte[] b)返回的是实际读取字节的个数，如果小于定义数，就是实际读取个数
            while ((readlength = fileInputStream.read(b)) != -1) {
                //文件中的数据内容已经读取到了btye[] 集合中，只需要把btye[]转换从string字符串类型即可
                System.out.print(new String(b, 0, readlength));
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileInputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
    //read(byte[] b)该案例中就是第一次读取8个字节；第二次只读取了3个字节，读取到的三个字节ASCII码值会
    //把第一次的ASCII码值覆盖，后五个则没有覆盖
}
```

### 字节输出流

#### OutputStream

##### FileOutputStream（文件输入流）

使用FileOutputStream写入文件，如果文件不存在会创建文件

写入有三种方法:

1. ```java
   write(int b) //写入一个字节内容
   ```

2. ```java
   write(byte[] b) //写入字节数组
   ```

3. ```java
   write(byte[] b, int off, int len) //写入指定长度的字节数组
   //将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此文件输出流
   ```

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.FileOutputStream;
import java.io.IOException;

public class OutputStreamDemo01 {
    public static void main(String[] args) {

    }

    @Test
    public void outputStream01() {
        String writefile = "d:\\a.txt";
        FileOutputStream fileOutputStream = null;
        try {
            fileOutputStream = new FileOutputStream(writefile);
            fileOutputStream.write('y');
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }

    @Test
    public void outputStream02() {
        String writefile = "d:\\a.txt";
        FileOutputStream fileOutputStream = null;
        try {
            fileOutputStream = new FileOutputStream(writefile);
            String str = "hi ykh";
            //通过getBytes()方法将String类型的 字符串->字节数组
            fileOutputStream.write(str.getBytes());
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }

    @Test
    public void outputStream03() {
        String writefile = "d:\\a.txt";
        FileOutputStream fileOutputStream = null;
        try {
            fileOutputStream = new FileOutputStream(writefile);
            String str = "hi ykh";
            //通过getBytes()方法将String类型的 字符串->字节数组
            fileOutputStream.write(str.getBytes(), 0, 2);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

注意点：使用**[FileOutputStream](../../java/io/FileOutputStream.html#FileOutputStream(java.io.File))**(File file)构造器创建对象的时候操作过程中会对文件数据内容覆盖，如果想要不覆盖，而是追加内容采用**[FileOutputStream](../../java/io/FileOutputStream.html#FileOutputStream(java.io.File, boolean))**(File file,  boolean append)方法创建

```java
@Test
public void outputStream04() {
    String writefile = "d:\\a.txt";
    FileOutputStream fileOutputStream = null;
    try {
        fileOutputStream = new FileOutputStream(writefile, true);
        String str = "hi ykh";
        fileOutputStream.write(str.getBytes(), 0, 2);
    } catch (IOException e) {
        throw new RuntimeException(e);
    } finally {
        try {
            fileOutputStream.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

---

copy文件

```java
package com.hspedu.IO;

import org.junit.jupiter.api.Test;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FilecopyDemo01 {
    public static void main(String[] args) {

    }

    @Test
    public void copy() {
        String filepath = "E:\\青岛威海\\1.JPG";
        String copypath = "d:\\1.JPG";
        FileInputStream fileInputStream = null;
        FileOutputStream fileoutputStream = null;
        int readleng = 0;
        byte[] b = new byte[1024];
        try {
            fileInputStream = new FileInputStream(filepath);
            fileoutputStream = new FileOutputStream(copypath);
            while ((readleng = fileInputStream.read(b)) != -1) {
                //采取边读边写的方式实现，不是全部du'qu
                fileoutputStream.write(b);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (fileInputStream != null) {
                    fileInputStream.close();
                }
                if (fileoutputStream != null) {
                    fileoutputStream.close();
                }
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

---

### 字符输入流

FileReader和FileWrite是字符流，FileReader的继承关系图如下：

![image-20221003081348446](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20221003081348446.png)

读取文件有两个方法：read();和read(char[] c)

代码演示：

```java
package com.hspedu.IO.FileReaderandwiter;

import org.junit.jupiter.api.Test;

import java.io.FileReader;
import java.io.IOException;

public class FileReaderDemo01 {
    public static void main(String[] args) {

    }

    //read()；单个字符的读取
    @Test
    public void Filereader01() {
        String filepath = "d:\\a.txt";
        FileReader fileReader01 = null;
        int i = 0;
        try {
            fileReader01 = new FileReader(filepath);
            //读取都要使用到循环读取的方式
            while ((i = fileReader01.read()) != -1) {
                System.out.print((char) i);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            if (fileReader01 != null) {
                try {
                    fileReader01.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }

    //read(char[] c)；字符数组读取
    @Test
    public void Filereader02() {
        String filepath = "d:\\a.txt";
        FileReader fileReader01 = null;
        char[] c = new char[100];
        int readlength = 0;
        try {
            fileReader01 = new FileReader(filepath);
            //读取都要使用到循环读取的方式
            while ((readlength = fileReader01.read(c)) != -1) {
                System.out.print(new String(c, 0, readlength));
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            if (fileReader01 != null) {
                try {
                    fileReader01.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

### 字符输出流

FileWrite继承关系图如下：

![image-20221003082043096](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20221003082043096.png)



**注意点：**

- **使用FileWrite后，必须将FileWrite关闭（close）或者刷新（flush）否则写入不到文件当中去**
- **使用FileWrite时，创建对象要根据业务逻辑确定是覆盖内容还是追加的方式创建**

```java
package com.hspedu.IO.FileReaderandwiter;

import org.junit.jupiter.api.Test;

import java.io.FileWriter;
import java.io.IOException;

public class FileWriterDemo01 {
    public static void main(String[] args) {

    }

    @Test
    public void filewritertest01() {
        String writerpath = "d:\\b.txt";
        FileWriter fileWriter01 = null;
        char[] c = {'f', 'f', '1'};
        try {
            fileWriter01 = new FileWriter(writerpath);
            //如果数据量大同样采用循环写入的方式
            //没有定义append未true，所以是覆盖的方式，否则为追加
            fileWriter01.write('y');
            fileWriter01.write("kh");
            fileWriter01.write("浙江桐庐", 1, 2);
            fileWriter01.write(c);
            fileWriter01.write(c, 2, 1);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //写入完毕以后一定要关闭或者刷新流，否则就会导致数据只存在内存中，而没有写入文件中
            if (fileWriter01 != null) {
                try {
                    fileWriter01.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

## Properties类
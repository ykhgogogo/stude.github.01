# 常见API

## 关键点：学会API帮助文档查阅

## Math 类

Math常用的方法基本都是静态方法，因此可以直接通过math.访问

### Math类中常见方法

```java
package com.hspedu.math;

public class mathDemo01 {
    public static void main(String[] args) {
        int abs1 = Math.abs(-3);//绝对值
        System.out.println(abs1);//3
        //abs由于取值范围原因，最小值取反对应的数据超出范围是个Bug
        //可以使用absExact方法，当不满足条件是会提示异常.ArithmeticException
        //int abs1_2 = Math.absExact(-2147483648);
        //System.out.println(abs1_2);
        double abs2 = Math.pow(2, 6);//n次方
        System.out.println(abs2);//64.0
        double abs3 = Math.ceil(9.1);//向上取整
        System.out.println(abs3);//10.0
        double abs4 = Math.floor(8.8);//向下取整
        System.out.println(abs4);//8.0
        long abs5 = Math.round(-5.5);//四舍五入
        System.out.println(abs5);//-5
        double abs6 = Math.sqrt(9.0);//开平方
        System.out.println(abs6);//3.0
        System.out.println(Math.cbrt(8));//cbrt开立方根；2.0
        for (int i = 0; i < 10; i++) {
            System.out.println((int)(Math.random() * 6 + 2));
        }
        //想要获得[a,b]范围的随机数
        //a <= x <= (int)(a + random() * (b - a + 1))
        int max = Math.max(10,1);
        int min = Math.min(10,1);
        System.out.println(max);
        System.out.println(min);
        //获取a的b次幂
        System.out.println(Math.pow(2,3));//8.0
        //b的数据类型是double，可以为正可以为负，和数学一致
    }
}
```

---

### 判断质数习题优化

- 一个数的一对因子必然一个大于平方根，一个小于平方根，所以可以减少循环次数

```java
package com.hspedu.math;

public class mathDemo02 {
    public static void main(String[] args) {
        System.out.println(con(13));
        System.out.println(con(26));
    }
    public static String con(int i){
        for (int j = 2; j < Math.sqrt(i); j++) {
            if (i % j == 0){
                return "不是";
            }
        }
        return "是";
    }
}
```

- 水仙花统计

```java
package com.hspedu.math;

public class mathDemo03 {
    public static void main(String[] args) {
        System.out.println(com());
    }
/*    public static int cn(){
        int c = 0;
        for (int i = 100; i <= 999; i++) {
            int g =i %10;
            int s =i /10 %10;
            int b =i /100 %10;
            double sum = Math.pow(g,3)+Math.pow(s,3)+Math.pow(b,3);
            if (sum == i){
                System.out.println(i);
                c++;
            }else {}
        }
        return c;
    }*/

    public static int com() {
        int count = 0;
        for (int i = 100; i <= 999; i++) {
            double sum = 0;
            int temp = i;
            for (int j = 0; j < 3; j++) {
                int g = temp % 10;
                temp = temp / 10;
                double pow = Math.pow(g, 3);
                sum = (pow) + sum;
            }
            if (sum == i){
                count++;
                System.out.println(i);
            }else {
            }
        }
        return count;
    }
}
```

---

## System 类

### System类中常见方法

1. exit退出方法：

```java
System.out.println("OK1");
//EXIT方法表示退出，0表示一个状态，正常退出
//非0表示虚拟机异常停止
System.exit(0);//OK2不能输出
System.out.println("OK2");
```

2. currentTimeMillis方法：当前时间与协调世界时 1970 年 1 月 1 日午夜之间的时间差（以毫秒为单位测量）
3. arraycopy方法：

```java
int[] arr1 = {1,2,3};
int[] arr2 = new int[3];
System.arraycopy(arr1,0,arr2,0,3);//其中需要传入五个数据，分别是：数据源，源数组拷贝起始索引，数据目的地，目的地数组索引，拷贝的个数
/* 
* @param      srcPos   starting position in the source array.
* @param      dest     the destination array.
* @param      destPos  starting position in the destination data.
* @param      length   the number of array elements to be copied.
*/
System.out.println(Arrays.toString(arr2));
```

使用注意点：

- 如果数据源数组和目的地数组都是存放基本数据类型，那么两者的类型必须一致，否则抛出异常

```java
int[] arr1 = {1,2,3};
double[] arr2 = new double[3];
System.arraycopy(arr1,0,arr2,0,3);//ArrayStoreException
```

- 在拷贝的时候需要考虑数组的长度，如果超出范围会报错

```java
int[] arr1 = {1,2,3};
int[] arr2 = new int[1];
System.arraycopy(arr1,0,arr2,0,3);//ArrayIndexOutOfBoundsException
```

- 如果数据源数组和目的地数组都是引用数据类型，那么子类类型可以赋值给父类类型

```java
student ip1 = new student("jack");
student ip2 = new student("tom");
student ip3 = new student("smith");
student[] stu = {ip1, ip2, ip3};
student[] stu2 = new student[3];
System.arraycopy(stu, 0, stu2, 0, 3);
for (int i = 0; i < arr1.length; i++) {
    System.out.println(stu2[i].getName());
}
person[] stu3 = new person[3];
System.arraycopy(stu, 0, stu3, 0, 3);
for (int i = 0; i < arr1.length; i++) {
    System.out.println(stu3[i].getName());
}
```

---

## Runtime 类

```java
package com.hspedu.runtime;

import java.io.IOException;

public class runtime {
    public static void main(String[] args) throws IOException {
        //1.获得runtime对象，不能通过new创建对象
        //通过底层源代码可以发现是单例设计模式，已经new了一个对象
        //并且构造器私有化，因为JVM虚拟机只有一个运行环境
        //Runtime r = new Runtime();
        Runtime r1 = Runtime.getRuntime();
        Runtime r2 = Runtime.getRuntime();
        System.out.println(r2 == r1);//true

        //2.退出程序，停止虚拟机
        //Runtime.getRuntime().exit(0);

        //3.获得Cpu线程数
        System.out.println(Runtime.getRuntime().availableProcessors());//16

        //4.总内存大小，单位是byte
        System.out.println(Runtime.getRuntime().maxMemory());

        //5.已经获取的总内存数，单位是byte
        System.out.println(Runtime.getRuntime().totalMemory());

        //6.剩余内存，单位是byte
        System.out.println(Runtime.getRuntime().freeMemory());

        //7.运行cmd
        Runtime.getRuntime().exec("notepad");
    }
}
```

---

## Object 类

Object类是Java中的顶级父类

### Object的构造方法

```java
public Object();
```

- 只有空参构造，因为Object是所有类的父类，所有类不存在共性属性，所以没有带参构造器
- 其他类中构造器的第一行隐藏了super();语句，因为Object只有无参构造器

### Object的成员方法

#### toString方法：

 返回对象的字符串形式，Object类中的toString方法返回的是地址值，所以可以通过重写toString方法输出字符串形式

- 细节：System：类名

​					out：静态变量

​					System.out：获取打印对象

​					println()：方法

```java
public class Object {
    public static void main(String[] args) {
        Object obj = new Object();
        System.out.println(obj);
        //com.hspedu.Object.Object@119d7047
        System.out.println(obj.toString());
        //com.hspedu.Object.Object@119d7047
    }
}
```

#### equals方法

细节：如果子类没有重写equals方法，调用的是Object类中的equals方法，比较的是地址值；往往重写之后比较内容

```java
String str1 = "abc";
StringBuilder str2 = new StringBuilder("abc");
System.out.println(str1.equals(str2));//false 
System.out.println(str2.equals(str1));//false
```

解释：str1.equals(str2)调用的是str1中String的equals方法

```java
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String aString = (String)anObject;
            if (!COMPACT_STRINGS || this.coder == aString.coder) {
                return StringLatin1.equals(value, aString.value);
            }
        }
        return false;
    }
```

因为比较的对象是StringBuilder，并且StringBuilder不是String的子类，所以直接返回false

str1.equals(str2)调用的是str2中StringBuilder的equals方法，StringBuilder没有equals方法，所以调用父类的equals方法，比较的是地址值

#### clone方法（拷贝）



---

## Objects工具类

### equals方法

```java
import java.util.Objects;

public class objects {
    public static void main(String[] args) {
        student stu1 = new student("jack", 20);
        student stu2 = new student("tom", 10);
        boolean reuslt1 = Objects.equals(stu1, stu2);
        System.out.println(reuslt1);//false
    }
}
```

equals源代码：

1. 先判断两个对象是否是同一个对象
2. 在判断a是不是null，如果不是空，继续进行equals方法判断
3. 调用a类中的equals方法比较，如果a类没有equals没有重写equals方法，就会调用父类的equals方法比较地址值

```java
public static boolean equals(Object a, Object b) {
return (a == b) || (a != null && a.equals(b));
}
```

### isNull

```java
student stu3 = new student("smith", 23);
       System.out.println(Objects.isNull(stu3));//false
```

方法用于判断是否为空

### nonNulli

和isNull想反，用法一致

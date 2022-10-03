#  泛型（Generic）

泛型的好处：

- 编译时，检查添加的元素类型，提高了安全性
- 减少了类型转换的次数，提高了效率
- 不再提示编译警告

---

##  泛型介绍

**泛型 可以理解为表示数据类型的类型**

- 泛型又称为参数化类型（类型形参传实参），JDK5以后出现的新特征，解决数据类型安全性问题
- 在**类声明**或**实例化**时只要制定好需要的具体类型即可
- 泛型可以保证如果程序在编译时没有发出警告，运行时就不会产生ClassCastException异常。同时，代码更加简介、健壮
- 泛型的作用是：可以在类声明时通过一个标识表示类中某个属性的类型，或者是某个方法的返回值类型，或者参数类型

简单示例：

```java
import java.util.ArrayList;

public class genericDemo01 {
    public static void main(String[] args) {
        ArrayList<Dog> dogs = new ArrayList<Dog>();//限定了集合中可以加入的类型
        dogs.add(new Dog<String>("javk", "1"));
        dogs.add(new Dog<String>("tom", "2"));
        dogs.add(new Dog<String>("smith", "3"));
        for (Dog dog : dogs) {//省略了Object类型数据向下转型为Dog类型
            System.out.println(dog);
        }
    }
}

class Dog<E> {//泛型的字母使用可以是E，可以是Q都可以只是代表形式，具体数据类型在创建或者实例化时指定
    //类似于模板
    //编译期间就确定了是什么类型
    private E Name;
    private E id;

    public Dog(E name, E id) {
        Name = name;
        this.id = id;
    }

    public E getName() {
        return Name;
    }

    public void setName(E name) {
        Name = name;
    }

    public E getId() {
        return id;
    }

    public void setId(E id) {
        this.id = id;
    }

    public E re() {//方法的返回值类型同样可以通过泛型指定
        return Name;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "Name=" + Name +
                ", id=" + id +
                '}';
    }
}
```

##  泛型使用细节和说明

1. ```java
   interface List<T>{}
   public class HashSet<E>{}...等
   //说明：T，E只能是引用数据类型
   List<Integer> list = new ArraList<Integer>();//对
   List<int> list = new ArraList<Integer>();//错
   ```

2. 在指定泛型具体类型后，可以传入该类型或者其子类型
3. 泛型的使用形式:

```java
List<Integer> list = new ArraList<>();
List list = new ArraList();//如果不写就是默认Object类型
```

```java

public class genericDemo03 {
    public static void main(String[] args) {
        ArrayList<Integer> i1 = new ArrayList<Integer>();
        ArrayList<Integer> i2 = new ArrayList<>();
        ArrayList<AA> i3 = new ArrayList<>();
        ArrayList<BB> i4 = new ArrayList<>();
        CC<AA> i5 = new CC<>(new AA());
        CC<AA> i6 = new CC<>(new BB());//如果BB继承了子类，同样可以传入泛型
        i5.f();//运行类型是AA
        i6.f();//运行类型是BB
    }
}
class AA{}
class BB extends AA{}
class CC<E>{
    public E name;

    public CC(E name) {
        this.name = name;
    }

    public E getName() {
        return name;
    }

    public void setName(E name) {
        this.name = name;
    }
    public void f(){
        System.out.println(name.getClass());
    }
}
```

![image-20220926222606812](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220926222606812.png)

```java
import java.util.ArrayList;
import java.util.Comparator;

@SuppressWarnings("all")
public class genericDemo04 {
    public static void main(String[] args) {
        ArrayList<employee> list = new ArrayList<>();
        employee ip1 = new employee("jack", "300000", new mydate("1999", "2", "1"));
        employee ip2 = new employee("tom", "400000", new mydate("2200", "8", "19"));
        employee ip3 = new employee("tom", "650000", new mydate("2200", "8", "20"));
        list.add(ip1);
        list.add(ip2);
        list.add(ip3);
        list.sort(new Comparator<employee>() {
            @Override
            public int compare(employee o1, employee o2) {
                if (!(o1.getName()).equals(o2.getName())) {
                    return (o1.getName()).compareTo(o2.getName());
                } else {//当名字相同的时候，需要对年月日进行判断
                    //此处较为啰嗦可把这个判断放回mydate类中进行
                    /*if (!(o1.getDate().getYear()).equals(o2.getDate().getYear())) {
                        return (o1.getDate().getYear()).compareTo(o2.getDate().getYear());
                    } else if (!(o1.getDate().getMonth()).equals(o2.getDate().getMonth())) {
                        return (o1.getDate().getMonth()).compareTo(o2.getDate().getMonth());
                    } else {
                        return (o1.getDate().getDay()).compareTo(o2.getDate().getDay());
                    }*/
                    return (o1.getDate()).compareTo(o2.getDate());
                }
            }
        });
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }
}

class employee {
    private String name;
    private String sal;
    private mydate date;

    public employee(String name, String sal, mydate date) {
        this.name = name;
        this.sal = sal;
        this.date = date;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSal() {
        return sal;
    }

    public void setSal(String sal) {
        this.sal = sal;
    }

    public mydate getDate() {
        return date;
    }

    public void setDate(mydate date) {
        this.date = date;
    }

    @Override
    public String toString() {
        return "employee{" +
                "name='" + name + '\'' +
                ", sal='" + sal + '\'' +
                ", date=" + date +
                '}';
    }
}

class mydate implements Comparable<mydate>{
    private String year;
    private String month;
    private String day;

    public mydate(String year, String month, String day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    public String getYear() {
        return year;
    }

    public void setYear(String year) {
        this.year = year;
    }

    public String getMonth() {
        return month;
    }

    public void setMonth(String month) {
        this.month = month;
    }

    public String getDay() {
        return day;
    }

    public void setDay(String day) {
        this.day = day;
    }

    @Override
    public String toString() {
        return "mydate{" +
                "year='" + year + '\'' +
                ", month='" + month + '\'' +
                ", day='" + day + '\'' +
                '}';
    }

    @Override
    public int compareTo(mydate o) {
        if (!year.equals(o.getYear())) {
            return (year).compareTo(o.getYear());
        } else if (!(month).equals(o.getMonth())) {
            return (month).compareTo(o.getMonth());
        } else {
            return (day).compareTo(o.getDay());
        }
    }
}
```

---

##  自定义泛型

###  自定义泛型类

1. 基本语法：

```java
class 类名<T,R....>{//可以是类名，也可以是接口；其中...表示可以指定多个类型
	//T,R就是泛型的标识符
    //成员
}
```

2. 注意细节：

- 普通成员可以使用泛型（属性、方法）
- 使用泛型的数组，不能初始化
- 静态方法中不能使用类的泛型
- 泛型类的类型，是在创建对象时确定的（因为创建对象时，需要指定确定类型）
- 如果在创建对象时，没有指定类型，默认为Object

```java
public class genericDemo05 {
    public static void main(String[] args) {
        animal<Double, String, Integer> animal01 = new animal<Double, String, Integer>("jack", 10.9, "tom", 100);
        animal01.setAge("12");
        System.out.println(animal01);
    }
}

class animal<T, R, Q> {
    private String NAME;
    private T id;
    private R age;
    private Q vb;
    //输出使用泛型时，不能进行初始化，因为实例化数组过程中
    //没有指定数组的类型，没法明确数组需要开辟多大的空间
    //T[] LIST = new T[8];

    //静态方法没法使用泛型，因为静态随着类加载而加载
    //当进行类加载过程中就可能进行相关操作，而此时没有指定类型
    //而泛型是在创建/定义对象的时候才会确定
    //public void str1(T t){}
    //public static void str2(T t){}

    //构造器使用泛型
    public animal(String NAME, T id, R age, Q vb) {
        this.NAME = NAME;
        this.id = id;
        this.age = age;
        this.vb = vb;
    }

    public String getNAME() {
        return NAME;
    }

    public void setNAME(String NAME) {
        this.NAME = NAME;
    }

    public T getId() {
        return id;
    }

    public void setId(T id) {
        this.id = id;
    }

    public R getAge() {
        return age;
    }

    public void setAge(R age) {
        this.age = age;
    }

    public Q getVb() {
        return vb;
    }

    public void setVb(Q vb) {
        this.vb = vb;
    }

    @Override
    public String
    toString() {
        return "animal{" +
                "NAME='" + NAME + '\'' +
                ", id=" + id +
                ", age=" + age +
                ", vb=" + vb +
                '}';
    }
}
```

### 自定义泛型接口

- 基本语法：

```JAVA
interface 接口名<T,R...>{}
```

- 注意细节：

1. 接口中，静态成员也不能使用泛型（和泛型类规定一样）
2. 泛型接口的类型，在继承接口或者实现接口时确定
3. 没有指定类型，默认为Object

```java
public class genericDemo06 {
    public static void main(String[] args) {

    }
}

interface interfaceusb<T, R> {
    //接口中指定普通方法可以使用接口泛型，抽象方法所以没有方法体
    T get();

    T get(T i);

    void set(R user, T back);

    //默认方法也可以使用泛型
    default R method(T i) {
        return null;
    }

    //属性不能使用泛型修饰，因为接口中属性是final，static修饰的
    //static修饰会导致随着类的加载无法确定属性的泛型类型
    //接口中静态成员不能使用泛型
    //int i = 10;
    //T number = 10;
}

//IA接口继承了interfaceusb接口以后，当aa类实现了IA接口时
//重写方法会自动填充类型相应的位置
interface IA extends interfaceusb<String, Integer> {
}

class aa implements IA {
    @Override
    public String get() {
        return null;
    }

    @Override
    public String get(String i) {
        return null;
    }

    @Override
    public void set(Integer user, String back) {

    }
}

//实现接口时需要指定接口泛型
class bb implements interfaceusb<Character, Double> {
    @Override
    public Character get() {
        return null;
    }

    @Override
    public Character get(Character i) {
        return null;
    }

    @Override
    public void set(Double user, Character back) {

    }
}
```

### 自定义泛型方法

- 基本语法：

```java
修饰符<T,R>返回值类型 方法名（参数列表）{}
```

- 注意细节：

1. 泛型方法，可以定义在普通类中，也可以定义在泛型类中

2. 当泛型方法被调用时，类型会确定

3. ```java
   public void eat(E e){}//修饰符后面没有<T,R>,方法不是泛型方法，而是使用了泛型
   ```

```java
import java.util.ArrayList;

public class genericDemo07 {
    public static void main(String[] args) {
        car car1 = new car();
        //当调用泛型方法时，传入具体数据
        //编译器会自动判断泛型类型
        car1.run2("jack",12);
        car1.run2('c',12.99);
        car2<ArrayList, String> car2 = new car2<>();
        car2.ok(new ArrayList(),12.9F);
    }
}
//泛型方法可以定义在普通类中，也可以定义在泛型类中
class car{
    public void run1(){}//普通方法
    //<T,R>就是泛型，提供给run方法使用的
    public<T,R> void run2(T t,R r){//泛型方法
        System.out.println(t.getClass());//class java.lang.String
        System.out.println(r.getClass());//class java.lang.Integer
        //调用方法时传入的数据是10，之前有说泛型必须是引用数据类型
        //所以传入10的是int数据类型，因此编译器会进行自动装箱的过程，转换成引用数据类型Integer
        //并且数据类型会根据传入数据的变化而变化
        System.out.println("=========");
        System.out.println(t.getClass());//class java.lang.Character
        System.out.println(r.getClass());//class java.lang.Double
    }
}
class car2<T,R>{
    public<U,M> void run3(U u,M m){}
    //泛型方法和方法使用泛型
    //访问修饰符后面没有定义泛型，此处只是使用了泛型类声明的泛型
    public void HI(T t){}
    //泛型方法可以使用泛型类声明的泛型，也可以使用自身声明的泛型
    public<Q> void ok(T t,Q q){
        System.out.println(t.getClass());//class java.util.ArrayList
        System.out.println(q.getClass());//class java.lang.Float
    }
}
```

## 泛型继承和通配符

1. 泛型不具备继承性
2. <?> 表示支持任意泛型类型
3. <? extends A> 表示支持A类以及A类的子类，规定了泛型的上限
4. <? super A> 表示支持A类以及A类的父类，不限于直接父类，规定了泛型的下限

```java
import java.util.ArrayList;
import java.util.List;

public class genericDemo08 {
    public static void main(String[] args) {

        Object i1 = new String("xx");
        //泛型不具备继承性所以不能这么写
        //ArrayList<Object> list = new ArrayList<String>();
        List<Object> list1 = new ArrayList<>();
        List<String> list2 = new ArrayList<>();
        List<id1> list3 = new ArrayList<>();
        List<id2> list4 = new ArrayList<>();
        List<id3> list5 = new ArrayList<>();
        
        add1(list1);
        add1(list2);
        add1(list3);
        add1(list4);
        add1(list5);
        
        //add2(list1);
        //add2(list2);
        add2(list3);
        add2(list4);
        add2(list5);
        
        add3(list1);
        //add3(list2);
        add3(list3);
        //add3(list4);
        //add3(list5);
    }
    public static void add1(List<?> list){}
    public static void add2(List<? extends id1> list){}
    public static void add3(List<? super id1> list){}
}
class id1{}
class id2 extends id1{}
class id3 extends id1{}
```

---

## JUnit

JUnit是Java语言的单元测试框架 **@Test** 写方法前


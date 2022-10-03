# 线程(Thread)基础

##  线程相关的基本概念

### 程序

- 是为完成特定任务、用某种语言编写的一组指令的集合，即写的代码

### 进程

- 进程是指运行中的程序，当使用一个程序时，操作系统就会为该进程分配内存空间。
- 进程是程序一次执行过程，或是正在运行的一个程序。是动态过程：有自身的产生、存在和消亡过程

### **线程**

- 线程由进程创建，是进程的一个实体
- 一个进程可以拥有多个线程

---

## 线程

### 1. 单线程

同一个时刻，只允许执行一个线程

### 2. 多线程

同一个时刻可以执行多个线程

### 3. 并发

同一个时刻，多个任务交替执行，造成一种“貌似同时”的错觉，简单说对于单核Cpu实现的多任务就是并发

![image-20220927151636105](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220927151636105.png)

### 4. 并行

同一个时刻，多个任务同时执行。多核Cpu可以实现并行。**并行和并发会同时出现**

![image-20220927151700154](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220927151700154.png)

---

## 线程基本使用

### 创建线程的两种方式

#### 1. 继承Thread类 重写run方法

入门代码：

```java
package com.hepedu.thread;

public class threadDemo01 {
    public static void main(String[] args) {
        //创建一个cat对象，可以当作线程使用
        cat cat = new cat();
        cat.start();//启动线程
    }
}

//说明：
//1.当一个类继承了Thread类，该类可以当作线程使用
//2.我们会重写run方法，方法体内写上自己业务逻辑
//3.Thread类中的run方法其实是因为实现了Runable接口的run方法
    /*@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }*/
//
class cat extends Thread {
    int count;

    @Override
    public void run() {
        while (true) {
            System.out.println("cat,cat,cat" + (++count));
            //让线程休眠sleep
            //调用会抛出异常，使用try-catch捕捉异常处理
            //这里try-catch是保证该线程在sleep时还是能感知响应，能够响应中断，不会睡死
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if (count == 8) {
                break;
            }
        }
    }
}
```

注意：可以使用JConsole监控线程执行情况，可以明显看出运行线程的过程；点击run的时候开启了进程，由进程开启了一个主线程，随后也会开启子线程；当主线程运行结束以后，主线程退出，但是如果子线程还没有运行结束时，进程不会退出，只有**所有线程运行结束时，进程才会结束退出**。多线程过程如下：

![image-20220929134321672](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929134321672.png)

串行化过程：

![image-20220929135621705](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929135621705.png)

多线程过程示意图：

![image-20220929204242501](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929204242501.png)

---

##### start方法和run方法调用的区别

- ```java
  cat.start();//启动线程
  cat.run();//如果调用run方法，并没有启动子线程，调用run方法线程名也是main，而不是Thread-0
  //这样调用run方法就会导致主线程阻塞，只有把run方法执行完毕以后才能继续运行主线程，这是串行化过程，不是多线程
  //主线程start启动子线程以后，主线程不会被阻塞而是会交替运行
  ```

---

##### start方法介绍

![image-20220929141402787](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929141402787.png)

1. ```java
   public synchronized void start() {..start0();..}
   ```

2. ```java
   private native void start0();
   //start0是native修饰的本地方法，是JVM调用的，底层时C或者C++
   //实际上实现多线程的是start0()方法，而不是run方法；start0调用多线程机制实现run方法
   ```

---

```java
package com.hepedu.thread;

public class threadDemo01 {
    public static void main(String[] args) throws InterruptedException {
        //创建一个cat对象，可以当作线程使用
        cat cat = new cat();
        cat.start();//启动线程
        //cat.run();//如果调用run方法，并没有启动子线程，调用run方法线程名也是main，而不是Thread-0
        //这样调用run方法就会导致主线程阻塞，只有把run方法执行完毕以后才能继续运行主线程，这是串行化过程，不是多线程
        //主线程start启动子线程以后，主线程不会被阻塞而是会交替运行
        System.out.println("主线程名字 =" + Thread.currentThread().getName());//主线程名称main
        for (int i = 0; i < 60; i++) {
            System.out.println("main线程 i=" + i);
            //让主线程也休眠
            Thread.sleep(1000);
        }
    }
}

//说明：
//1.当一个类继承了Thread类，该类可以当作线程使用
//2.我们会重写run方法，方法体内写上自己业务逻辑
//3.Thread类中的run方法其实是因为实现了Runable接口的run方法
    /*@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }*/
//
class cat extends Thread {
    int count;

    @Override
    public void run() {
        while (true) {
            System.out.println("cat,cat,cat" + (++count) + Thread.currentThread().getName());
            //Thread.currentThread().getName()就是获取线程名：Thread-0
            //让线程休眠sleep
            //调用会抛出异常，使用try-catch捕捉异常处理
            //这里try-catch是保证该线程在sleep时还是能感知响应，能够响应中断，不会睡死
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if (count == 80) {
                break;
            }
        }
    }
}
```

##### 创建线程步骤：

1. 让一个类继承Thread类，该类即可被当作线程使用

2. 重写Runable接口中的Run方法，添加业务逻辑

3. 创建对象实例，调用start()方法，启动线程

4. ```java
   Thread.currentThread().getName()//获得线程名称
   ```

---

#### 2. 实现Runnable接口 重写run方法

##### 实现Runnable接口说明：

1. Java是单继承机制，在某些情况下一个类可能已经继承了某个父类，这是继承Thread类方法来创建线程显然是不可能的；例如A继承了B，A就不能通过继承Thread类创建线程

2. 通过实现Runnable接口实现创建接口

代码演示

```java
package com.hepedu.thread;

public class threadDemo02 {
    public static void main(String[] args) {
        dog dog = new dog();
        //dog.run();同样不能够调用run方法创建线程，因为run方法始终是一个普通方法
        Thread thread = new Thread(dog);
        thread.start();
    }
}

class dog implements Runnable {
    @Override
    public void run() {
        int count = 0;
        while (true) {
            System.out.println("hi dog" + (++count) + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if (count == 10) {
                break;
            }
        }
    }
}
```

##### 静态代理模式简述

实现Runnable接口的底层实际上是静态代理模式，简要模拟：

1. ThreadProxy就是代理实现的中介，等价于上面的Thread类，ThreadProxy代理类实现Runnable接口，就需要重写Runnable中的run方法
2. 提供带参构造器，为了在创建ThreadProxy代理类的时候可以传入Runnable接口子类的实例化对象
3. 由于tiger类继承了animal类，Java是单继承机制，因此无法再继承Thread类（ThreadProxy中介类），就不能通过Thread.start();方法创建线程，所以采用实现Runnable接口的方式
4. 首先创建tiger对象，再创建ThreadProxy中介类对象，传入tiger对象，因为ThreadProxy类中的带参构造器可以接受Runnable接口的实现类，是ThreadProxy中介类中的target不为null
5. ThreadProxy中介类中含有start()方法，所以可以调用ThreadProxy中的start()方法，再调用start0()关键方法，再调用ThreadProxy中的run方法，根据动态绑定机制，会调用tiger运行类型的run方法，所以回到tiger类中的run方法

```java
package com.hepedu.thread;

public class threadDemo02 {
    public static void main(String[] args) {
        tiger tiger = new tiger();
        ThreadProxy threadProxy = new ThreadProxy(tiger);
        threadProxy.start();
    }
}

class animal {
}

class tiger extends animal implements Runnable {

    @Override
    public void run() {
        System.out.println("tiger");
    }
}

class ThreadProxy implements Runnable {
    private Runnable target = null;

    @Override
    public void run() {
        target.run();
    }

    public ThreadProxy(Runnable target) {
        this.target = target;
    }

    public void start() {
        start0();
    }

    public void start0() {
        run();
    }
}
```

### 继承Thread 对比 实现Runnable的区别

1. 从Java的实现来看，通过继承Thread或者实现Runnable接口来创建线程本质上没有区别，从JDK帮助文档可以看出Thread类本身就实现了Runnable接口，即都通过start0();方法创建线程
2. 实现Runnable接口方式更加适合多个线程共享一个资源的情况，并且避免了单继承的限制

![image-20220929204843716](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929204843716.png)

3. 建议使用Runnable接口方式创建接口

---

### 线程终止

1. 当线程完成任务后，会自动退出
2. 当可以通过使用变量来控制run方法退出的方式停止线程，即**通知方式**

main方法中让线程终止：如果想让t1通知t2线程结束

1. 在t2方法中定义私有化成员属性loop，使t2中的while循环判断条件为loop

   ```java
   private boolean loop = true;
   ```

2. t2中提供Set方法，可以提供给main方法中修改loop

   ```java
   t1.setLoop(false);
   ```

3. 代码展示

   ```java
   package com.hepedu.thread;
   
   public class threadExit {
       public static void main(String[] args) throws InterruptedException {
           T t1 = new T();
           t1.start();
           //如果希望main线程去控制t1线程的终止，必须修改loop
           //让t1退出run方法，从而终止t1线程 ->通知方式；set方法修改
           Thread.sleep(10000);//主线程休眠10秒之后在通知t1退出
           t1.setLoop(false);
       }
   }
   
   class T extends Thread {
       private boolean loop = true;
   
       public boolean isLoop() {
           return loop;
       }
   
       public void setLoop(boolean loop) {
           this.loop = loop;
       }
   
       int count = 0;
   
       @Override
       public void run() {
           while (loop) {
               try {
                   Thread.sleep(50);
               } catch (InterruptedException e) {
                   throw new RuntimeException(e);
               }
               System.out.println("T运行中" + (++count));
           }
       }
   }
   ```

---

## 线程常用方法

1. 第一组方法

- setName //设置线程名称，使之与参数name相同
- getName //返回该线程的名称
- start //是该线程开始执行；Java虚拟机底层调用该线程的start0();方法
- run //调用线程对象run方法
- setPriority //更改线程的优先级
- getPriority //获取线程的优先级
- sleep //在指定的毫秒数内让当前正在执行的线程休眠（暂停执行）
- interrupt //中断线程

说明：

- start底层会创建新的线程，调用run方法，run方法本身就是一个简单的方法调用，不会启动新线程
- 线程优先级访问，最大10，最小1，常见5
- interrupt **中断**线程，但是并没有真正结束线程，不同于**终止**，所以一般用于中断正在休眠的线程
- sleep 线程的静态方法，使当前线程休眠

```java
package com.hepedu.thread;

public class threadMethod01 {
    public static void main(String[] args) throws InterruptedException {
        TT tt = new TT();
        tt.setName("ykh");
        tt.setPriority(Thread.MIN_PRIORITY);
        System.out.println(tt.getName());//1
        System.out.println(tt.getPriority());
        tt.start();
        for (int i = 0; i < 5; i++) {
            Thread.sleep(1000);
            System.out.println("hi" + i);
        }
        tt.interrupt();
        //注意对于非阻塞线程中调用interrupt方法，会提前中断线程的运行，不到20s提前循环，但是子线程不会完全中断，后续还是会继续执行
        //后面的run方法还是会执行Thread.sleep(20000);，休眠达到20s再循环
        //即主程序运行结束了退出，当时子程序并没有退出
    }
}

class TT extends Thread {
    @Override
    public void run() {
        while (true) {
            for (int i = 0; i < 100; i++) {
                System.out.println(Thread.currentThread().getName() + "运行中" + i);
            }
            try {
                System.out.println(Thread.currentThread().getName() + "休眠中");
                Thread.sleep(20000);
            } catch (InterruptedException e) {
                //当休眠过程中遇到interrupt方法是，就会被catch到一个异常，进入catch方法体中，执行其中的代码
                //InterruptedException 表示捕获到一个中断异常
                System.out.println(Thread.currentThread().getName() + "被interrupt");
                //throw new RuntimeException(e);
            }
        }
    }
}
```

---

2. 第二组方法

- yield //静态方法，线程的礼让；让出cpu，让其他线程执行，但礼让的时间不确定，所以也不一定礼让成功；当cpu资源充足时，就可能礼让失败
- join //线程的插队；插队的线程一旦插队成功，则肯定先**执行完**插队的线程所有的任务，在回到原来的线程中执行，类似于让人买票插队，等别人所有的手续买完了就轮到你买

```java
package com.hepedu.thread;

public class threadMethod02 {
    public static void main(String[] args) throws InterruptedException {
        join join = new join();
        join.start();
        int count = 0;
        for (int i = 0; i < 20; i++) {
            System.out.println("hi" + (++count));
            Thread.sleep(1000);
            if (count == 5) {
                join.join();
            }
        }
    }
}

class join extends Thread {
    @Override
    public void run() {
        int count = 0;
        while (true) {
            System.out.println("hello" + (++count));
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if (count == 20) {
                break;
            }
        }
    }
}
```

![image-20220929231944378](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220929231944378.png)

- 用户线程 //也叫做工作线程，当线程的任务执行完成或者通知方式结束
- 守护线程 //一般是为工作线程服务的，当所有的用户线程结束，守护线程自动结束；常见的守护线程：垃圾回收机制

设置为守护线程的方式

说明：例如主线程main下有一个死循环子线程ti，正常情况下当主线程结束以后，子线程t1并不会退出，因为t1不是守护线程；所以我们希望将t1设置成守护线程，当主方法结束后，t1也结束。

```java
package com.hepedu.thread;

public class threadMethod03 {
    public static void main(String[] args) throws InterruptedException {
        MyDeamonThread myDeamonThread = new MyDeamonThread();//创建线程
        myDeamonThread.setDaemon(true);//注意守护线程的定义要放在前面，否则排除异常
        myDeamonThread.start();
        for (int i = 0; i <= 10; i++) {
            System.out.println("hi");
            Thread.sleep(1000);
        }
    }
}

class MyDeamonThread extends Thread {
    @Override
    public void run() {
        for (; ; ) {
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("hello tom");
        }
    }
}
```

---

## 线程状态

从官方的Thread.State说明文档指出一共六种状态

- [`NEW`](../../java/lang/Thread.State.html#NEW)
  至今尚未启动的线程处于这种状态。 
- [`RUNNABLE`](../../java/lang/Thread.State.html#RUNNABLE)
  正在  Java 虚拟机中执行的线程处于这种状态。 
- [`BLOCKED`](../../java/lang/Thread.State.html#BLOCKED)
  受阻塞并等待某个监视器锁的线程处于这种状态。 
- [`WAITING`](../../java/lang/Thread.State.html#WAITING)
  无限期地等待另一个线程来执行某一特定操作的线程处于这种状态。 
- [`TIMED_WAITING`](../../java/lang/Thread.State.html#TIMED_WAITING)
  等待另一个线程来执行取决于指定等待时间的操作的线程处于这种状态。 
- [`TERMINATED`](../../java/lang/Thread.State.html#TERMINATED)
  已退出的线程处于这种状态。
- 有六种的说法，也有**七种**的说法：当创建完线程以后调用start方法会使线程进入Runnable状态，**Runnable状态表示可以运行，但不一定直接运行，是否直接Running取决于线程调度器，不是程序能控制得，是操作系统内核决定的，，当拥有cpu为Running状态；处于Runnable状态细化为两个状态 Ready状态 或 Running状态**；如果在Runnable状态调用下图的不同方法会进入不同状态：Blocked状态 ，Waiting状态 ， TimedWaiting状态，当处于这三种状态结束后返回Runnable状态，不是一定处于Running状态；线程结束后为Teminated状态。图中也可以看出调用yeild状态是切换成Ready状态，不一定礼让成功；而join方法则是一定切换成Waiting状态。

![image-20220930084231766](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220930084231766.png)

## 线程同步机制

### 同步机制

1. 在多线程编程，一些敏感数据不允许被多个线程同时访问，此时就使用同步访问技术，保证数据在任何同一时刻，最多只有一个线程访问，以保证数据的完整性
2. 线程同步，即当有一个线程在对内存进行操作时，其他线程都不可以对这个内存地址进行操作，直到该线程完成操作，其他线程才能对该内存的地址进行操作

### Synchronized-同步实现方法

- 同步代码块

```java
synchronized(对象){//得到对象的锁，才能操作同步代码
	//需要被同步的代码；
}
```

- Synchronized还可以放在方法声明中，表示整个方法为同步方法

```java
public synchronized void m(String name){
	//需要被同步的代码；
}
```

#### 1. 互斥锁

- Java语言中引入对象互斥锁的概念，来保证共享数操作的完整性
- 每个对象都对应于一个可称为“互斥锁”的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象
- 关键字synchronized来与对象的互斥联系，当某个对象用synchronized修饰时，表明该对象在任一时刻只能有一个线程访问
- 同步的局限性：导致程序的执行效率降低
- **同步方法（非静态的）的锁可以是this，也可以是其他对象（要求是同一个对象）；同步方法没有使用static：默认对象为this，是非公平锁**
- **同步方法（静态的）的锁为当前类本身；如果方法使用static修饰默认锁对象：当前类.class**
- **要求多个线程的锁是用一个锁，不然锁不住**

解释：如果创建多个类对象分别调用start方法创建子线程，this就代表三个对象不同的锁，而锁的内容是一样的代码，形象理解：厕所本来只有一个门一把锁；创建多个对象再创建多个子线程变成了，一个厕所三个门对于三个锁

```java
package com.hepedu.thread;

public class threadDemo03_2 {
    public static void main(String[] args) {
        windows window1 = new windows();
        Thread thread1 = new Thread(window1);
        Thread thread2 = new Thread(window1);
        Thread thread3 = new Thread(window1);
        thread1.start();
        thread2.start();
        thread3.start();
    }
}

class windows implements Runnable {
    private boolean loop = true;
    private int count = 100;
    no n = new no();

    //同一对象开启的多个线程，是同一锁，同类不同对象开启对应线程，是多把不同的锁，想要锁住就加静态，这样锁的就是该类创建的所有对象了
    //静态方法随着类的加载而加载
    public synchronized static void m1() {//静态方法加上互斥锁，锁是加在类名.class上的，即windows.class
    }

    //括号中的对象不能传入this，因为静态方法在类的加载就加载，此时this对象还没有创建完成
    //括号中无法写入this，因为静态方法是共享的
    public static void m2() {
        synchronized (windows.class) {//互斥锁加载静态方法的代码块中，括号中不能加入this对象，而是windows.class
            System.out.println("hi");
        }
    }

    public /*synchronized*/ void sell() {//如果这样写就是同步方法，锁处于this对象
        //除了方法上加锁，也可以在代码块上加锁，即同步代码块
        synchronized (this /*n*/) {//同步代码块中的对象只要是多线程同时操作的对象就可以放入括号中
        //如果写成synchronized (new no),就表示每个线程都会创建一个no对象，并不是一个对象，就没有锁住还是会超卖
            System.out.println(this.getClass());
            if (count <= 0) {
                System.out.println("销售结束");
                loop = false;
                return;
            }
            System.out.println("tick剩余" + (--count) + Thread.currentThread().getName());
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }

    @Override
    public void run() {
        //int count = 100;
        while (loop) {
            sell();
        }
    }
}

class no {
    private String name;
}
```

## 线程死锁

- 多个线程都占用了对方的锁资源，都不肯想让，导致了死锁，代码中应该避免死锁

```java
package com.hepedu.thread;

public class threadMethod04 {
    public static void main(String[] args) {
        deadlock deadlock = new deadlock(true);
        deadlock deadlock1 = new deadlock(false);
        new Thread(deadlock).start();
        new Thread(deadlock1).start();
        //当o1和o2是静态才会发生死锁，普通对象不会被死锁
    }
}

class deadlock implements Runnable {
    static Object o1 = new Object();//保证多线程共享一个对象
    static Object o2 = new Object();
    private boolean flag;

    public deadlock(boolean flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        //当flag为true时，线程1会先得到o1对象互斥锁，然后尝试去获取o2对象互斥锁
        //如果得不到o2就会blocked
        //当flag为false时，线程2会先得到o2对象互斥锁，然后尝试去获取o1对象互斥锁
        //如果得不到o1就会blocked
        if (flag) {
            synchronized (o1) {
                System.out.println("获得A");
                synchronized (o2) {
                    System.out.println("获得B");
                }
            }
        } else {
            synchronized (o2) {
                System.out.println("获得B");
                synchronized (o1) {
                    System.out.println("获得A");
                }
            }
        }
    }
}
```

---

## 释放锁

释放锁的情况：

1. 当前线程的同步方法、同步代码执行结束
2. 当前线程在同步代码块，同步方法中遇到break；return;
3. 当前线程在同步代码块，同步方法中出现了未处理的Error或者Exception，导致异常结束
4. 当前线程在同步代码块、同步方法中执行了线程对象的wait方法，当前线程暂停，并释放锁

不会释放锁：

1. 线程执行同步代码块或者同步方法时，程序调用Thread.sleep()方法、Thread.yield()方法暂停当前线程的执行，不会释放锁
2. 线程执行同步代码块时，其他线程调用了改线程的suspend()方法，将该线程挂起，该线程不会释放锁

提示：控制线程的suspend方法和resume方法已经过时，不推荐使用


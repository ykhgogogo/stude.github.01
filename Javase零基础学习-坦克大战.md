# 坦克大战

## Java绘图坐标体系

### 坐标体系-介绍

- **坐标原点位于左上角，以像素为单位**。在Java坐标系中，第一个是x坐标，表示当前位置为垂直方向，距离坐标原点x个像素；第二个是y坐标，表示当前位置是垂直方向，距离坐标原点y个像素

![image-20220927203257870](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220927203257870.png)

### 坐标体系-像素

- 像素是一个密度单位，而厘米是长度单位，不可比较。

---

## Java绘图技术

![image-20220927205256625](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220927205256625.png)

### 绘制代码

```java
package com.hspedu.tank;

import javax.swing.*;
import java.awt.*;
//4.首先需要继承JFrame类，类似于窗口，窗口中嵌入了面板/画板
public class drawCircle extends JFrame{
    //5.定义一个面板
    private Mypanel mp = null;
    public static void main(String[] args) {
        //创建实例
        new drawCircle();
    }
    //6.在构造器中初始化面板的大小
    public drawCircle(){
        mp = new Mypanel();
        //7.把创建的面板放入窗口中
        this.add(mp);
        //设置窗口大小
        this.setSize(400,300);
        //当点击窗口×的时候可以退出程序
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        //可以显示
        this.setVisible(true);
    }
}
//1.先定义一个Mypanel，继承JPanel类，在面板上画图形
//Mypanel对象就可以理解为一个面板
class  Mypanel extends JPanel {
    //2.重写paint绘制方法
    @Override
    public void paint(Graphics g){
        super.paint(g);//调用父类的带参构造器，完成初始化 保留
        //3.Graphics就可以理解为一支画笔,Graphics提供了很多绘图方法
        System.out.println("paint方法被调用");
        g.drawOval(10,10,100,100);
    }
}
```

#### Graphics 类

```java
package com.hspedu.tank;

import javax.swing.*;
import java.awt.*;

//4.首先需要继承JFrame类，类似于窗口，窗口中嵌入了面板/画板
public class drawCircle extends JFrame {
    //5.定义一个面板
    private Mypanel mp = null;

    public static void main(String[] args) {
        //创建实例
        new drawCircle();
    }

    //6.在构造器中初始化面板的大小
    public drawCircle() {
        mp = new Mypanel();
        //7.把创建的面板放入窗口中
        this.add(mp);
        //设置窗口大小
        this.setSize(2000, 2000);
        //当点击窗口×的时候可以退出程序
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        //可以显示
        this.setVisible(true);
    }
}

//1.先定义一个Mypanel，继承JPanel类，在面板上画图形
//Mypanel对象就可以理解为一个面板
class Mypanel extends JPanel {
    //2.重写paint绘制方法
    @Override
    public void paint(Graphics g) {
        super.paint(g);//调用父类的带参构造器，完成初始化 保留
        //3.Graphics就可以理解为一支画笔,Graphics提供了很多绘图方法
        System.out.println("paint方法被调用");
        g.drawOval(10, 10, 100, 100);

        //调用Graphics类中的方法绘制不同图形
        //画直线
        g.drawLine(10, 10, 100, 100);
        //画矩形边框
        g.drawRect(10, 10, 100, 200);
        //画出填充矩形/椭圆
        //需要设置画笔的演示
        g.setColor(Color.BLUE);
        g.fillRect(30, 30, 20, 20);
        //画图片
        //首先需要获取图片资源,/IMG_9604(20220627-202903).JPG表示该项目的根目录去获取图片资源，其他是固定写法
        //把图片放到对应src包下就可以
        Image image = Toolkit.getDefaultToolkit().getImage(Mypanel.class.getResource("/IMG_9604(20220627-202903).JPG"));
        g.drawImage(image, 2, 2, 1920, 1440, this);
        //画字符串
        //设置画笔颜色和字体
        g.setColor(Color.red);
        g.setFont(new Font("楷书", Font.BOLD, 50));
        g.drawString("杭州你好", 100, 100);//左边是左下角和其他左上角不同
    }
}
```

---

### 绘图原理

- Component类提供了两个和绘图相关的重要方法：

1. paint（Graphics g）绘制组件的外观
2. repaint（）刷新组件的外观

- paint（）被调用的情况：

1. 当第一次在频幕显示的时候，程序会自动调用paint（）方法来绘制组件
2. 窗口最小化，再最大化
3. 窗口的大小发生变化，因为需要确保图形坐标位置不变
4. repaint方法被调用

---

## Java事件处理机制

### 基本说明

Java时间处理是采取“委派事件”，当事件发生时，产生事件的对象，对把此类“信息”传递给事件的监听者处理。

![image-20220928143751630](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220928143751630.png)

1. 事件源：事件源是一个产生事件的对象，比如按钮、窗口
2. 事件：事件就是承载事件源状态改变时的对象，比如键盘事件、鼠标事件、窗口事件等等，会产生一个事件对象，该对象保存着当前事件很多信息，比如KeyEvent对象含有被按下键的code值
3. 事件类型：查阅JDK文档

![image-20220928144300572](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220928144300572.png)

4. 事件监听器接口：

- 当事件产生一个事件，可以传送事件监听者处理
- 事件监听者实际就是一个类，该类实现了某个事件监听器接口，比如Mypanel就是一个类，它实现了KeyListenter接口，就可以作为一个事件监听者，对接收到的事件处理
- 事件监听器接口有多种，不同事件监听器可以监听不同事件，一个类可以实现多个监听接口
- 查看JDK即可

```java
package com.events;

import javax.swing.*;
import java.awt.*;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;

public class ballmove extends JFrame {
    Mypanal mp = null;

    public static void main(String[] args) {
        ballmove ballmove = new ballmove();
    }

    public ballmove() {
        mp = new Mypanal();
        this.add(mp);
        this.setSize(400, 300);
        //JFrame可以通过addKeyListener监听mp
        this.addKeyListener(mp);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setVisible(true);
    }
}

//实现KeyListener接口可以监听键盘录入
class Mypanal extends JPanel implements KeyListener {

    int x = 10;
    int y = 10;

    @Override
    public void paint(Graphics g) {
        super.paint(g);
        g.fillOval(x, y, 20, 20);
    }

    //有字符输出时就会有触发
    @Override
    public void keyTyped(KeyEvent e) {

    }

    //当有键被按压被触发
    @Override
    public void keyPressed(KeyEvent e) {
        //System.out.println((char) e.getKeyCode() + "被按下");
        //根据上下左右控制小球移动
        if (e.getKeyCode() == KeyEvent.VK_DOWN) {
            y++;
        } else if (e.getKeyCode() == KeyEvent.VK_UP) {
            y--;
        } else if (e.getKeyCode() == KeyEvent.VK_LEFT) {
            x--;
        } else if (e.getKeyCode() == KeyEvent.VK_RIGHT) {
            x++;
        }
        //按键完成以后需要重写绘制画板，需要调用repaint方法
        this.repaint();
    }

    //当有键被松开被触发
    @Override
    public void keyReleased(KeyEvent e) {

    }
}
```


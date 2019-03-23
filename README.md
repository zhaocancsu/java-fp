# java-fp
java函数式编程简介

## 基本概念
* 用纯函数的方式来构造程序
* 函数无副作用
  * 修改一个变量
  * 直接修改数据结构
  * 设置一个对象的成员
  * 抛出一个异常或以一个错误停止
  * 打印到终端或读取用户的输入
  * 读取或写入一个文件
  * 在屏幕上绘画

## 思想理解
```Java
public class Cafe {

    public Coffee buyCoffee(CreditCard  cc){
        Coffee cup = new Coffee();
        cc.charge(cup.price());//计费
        return cup;
    }

}
```

模块化与可测试性

```Java
//单独抽取一个付费模块
public class Cafe {
    public Coffee buyCoffee(CreditCard  cc, Payments p){
        Coffee cup = new Coffee();
        p.charge(cc ,cup.price());
        return cup;
    }
}
```

![对比](https://github.com/zhaocancsu/java-fp/blob/master/side-effect-fp.png)

将支付的创建与实际处理进行分离
```Java
    public Pair<Coffee, Charge> buyCoffee(CreditCard cc) {
        Coffee cup = new Coffee();
        return new ImmutablePair(cup, new Charge(cc, cup.price()));
    }
    
    //买多次并计算付费额度
    public Pair<List<Coffee>, Charge> buyCoffees(CreditCard cc, int n) {
        List<Pair<Coffee, Charge>> collect = IntStream.range(0, n).mapToObj(i -> buyCoffee(cc)).collect(Collectors.toList());
        List<Coffee> coffees = collect.stream().map(Pair::getLeft).collect(Collectors.toList());
        Charge c = new Charge(cc, collect.stream().map(Pair::getRight).mapToDouble(Charge::getAmount).sum());
        return new ImmutablePair<>(coffees, c);
    }
```
例子中将计费的创建过程与处理过程分离，总的来说，就是通过把这些产生副作用的代码推到程序的外层，来转换任何带有副作用的函数<br>
* ***从函数式的表达形式来说,程序的实现应该有一个纯的内核和一层很薄的外围来处理副作用***
* ***从实现角度来考虑，函数式的方式更容易进行推理转换***

## java中的实现和使用
* ***匿名内部类和lambda***
  
  以启动一个线程为例以前这样写
 ```Java
 new Thread(new Runnable(){
    @Override
    public void run(){
        //do something
    }
}).start();
 ```
 现在可以改成这样
 ```Java
 new Thread(
        () -> {
            //do something
        }
).start();
 ```
 **怎么做到的？？**
 
 1.基于编译器的类型推断<br>
 2.推断依据:函数接口(只有一个抽象方法的interface)<br>
 3.lambda表达式的类型为它的函数接口类型
 
 例如Runnable接口现在是这样定义的
 ```Java
 @FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
 ```
 FunctionalInterface是可选的,显示加上后编译器会检查该接口是否符合函数接口规范(但可以定义默认方法,静态方法和Object类里的public方法)
 
 **匿名内部类==lambda表达式？？**
 功能上可能是相似的<br>
 实现原理上会有些差异
 
![匿名内部类编译](https://github.com/zhaocancsu/java-fp/blob/master/diff.png)

匿名内部类的字节码
```Java
Compiled from "TestMain.java"
public class TestMain {
  public TestMain();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: new           #3                  // class TestMain$1
       7: dup
       8: invokespecial #4                  // Method TestMain$1."<init>":()V
      11: invokespecial #5                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      14: invokevirtual #6                  // Method java/lang/Thread.start:()V
      17: return
}
```
lambda的字节码
```Java
Compiled from "TestMain.java"
public class TestMain {
  public TestMain();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: invokedynamic #3,  0              // InvokeDynamic #0:run:()Ljava/lang/Runnable;
       9: invokespecial #4                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      12: invokevirtual #5                  // Method java/lang/Thread.start:()V
      15: return

  private static void lambda$main$0();
    Code:
       0: return
}
```

* ***Streaming***

* ***闭包***

* ***柯里化***

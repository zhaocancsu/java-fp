# java-fp
java函数式编程简介


```
public class Cafe {

    public Coffee buyCoffee(CreditCard  cc){
        Coffee cup = new Coffee();
        cc.charge(cup.price());//计费
        return cup;
    }

}
```

模块化与可测试性

```
//单独抽取一个付费模块
public class Cafe {
    public Coffee buyCoffee(CreditCard  cc, Payments p){
        Coffee cup = new Coffee();
        p.charge(cc ,cup.price());
        return cup;
    }
}
```

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

![对比](https://github.com/zhaocancsu/java-fp/blob/master/side-effect-fp.png)

将支付的创建与实际处理进行分离
```
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
例子中将计费的创建过程与处理过程分离，总的来说，就是通过把这些产生副作用的代码推到程序的外层，来转换任何带有副作用的函数，<br>
从函数式的表达形式来说,程序的实现应该有一个纯的内核和一层很薄的外围来处理副作用，从实现角度来考虑，函数式的方式更容易进行推理转换。

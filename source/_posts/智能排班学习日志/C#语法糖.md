# C#语法糖

首先需要声明的是“语法糖”这个词绝非贬义词，它可以给我带来方便，是一种便捷的写法，编译器会帮我们做转换；而且可以提高开发编码的效率，在性能上也不会带来损失。

## 1． 经过简化的Property

早些时候我们这样声明Property

```c#
private string _myName;
 
public string MyName
 
{ 
    get { return _myName;  }
    set { _myName = value; }
}
```

千篇一律的这样声明，没有多大意义，于是C#的设计人员将这个千篇一律的工作交给了编译器帮我们做了，我们现在可以这样声明

```c#
public string MyName { get; set; }
```

当然他不会牺牲灵活性，我们可以单独给get或者set设定访问限制符，例如

```c#
public string MyName { get; protected internal set; }
```

## 2． 经过两次变异的委托写法

net 1.1时我们不得不声明方法后才在委托中使用，在.net 2.0之后我们可以使用匿名委托，他不单可以简化写法，还可以在匿名委托中访问范围内的变量；再后来拉姆达表达式来了，写法就更简便了。

```c#
class MyClass
{
    public delegate void DoSomething(int a);
 
    //定义方法委托
    private void DoIt(int a) {
        Console.WriteLine(a);
    }
 
    private void HowtoDo(DoSomething doMethod,int a) {
        doMethod(a);
    }
 
    public static void Main(string[] args) {
        MyClass mc = new MyClass();
        //调用定义的方法委托
        mc.HowtoDo(new DoSomething(mc.DoIt), 10);
        int x = 10;
        //使用匿名委托
        mc.HowtoDo(delegate(int a){
            Console.WriteLine(a + x);
        },10);
 
        //使用lamda表达式
        mc.HowtoDo(a=>Console.WriteLine(a+x),10);
 
        Console.ReadLine();
    }
}
```



Post on Aug 7, 2021.  
Tags: langdv, lambda

# Lambda in Java JDK8

自从JDK8以来，lambda表达式被支持，虽然关于lambda的使用 有一套规约，但是它在使用上极大的提高了便利和简明性。

今天将主要关注，lambda是如何捕捉(capture)外部变量的，是如何将lambda代码块主体进行封装的.

```java
@FunctionalInterface
public interface Func {
    void ivk(int arg);
}

public class Test {

    private static void the_lambda_invoker(Func func) {
        func.ivk(2);  
        // Just normal object passing. 
        // the lambda seems been encapsulated inside the func.
    }

    public static void main(String[] args) {
        the_lambda_invoker(i -> System.out.printf("lambda arg: %s, captured: %s", i, Arrays.toString(args)));
    }

}
```
这是我们主要的检查代码，其中有一个简单的 函数接口(FunctionalInterface, 里面的`Func`)，
一个lambda调用者，和一个lambda"使用者".

## 检查.

我们将使用检查bytecode的方式，了解lambda实际上是如何运作的。
```
dreamtowards@localhost ldv % javap -p -v Test
...v...
```

<details> 
    <summary>Full Decompile Detail.</summary>

```
// jdk1.8.0_231-b11. for javac r javap. compiled by "javac Test.java"
...
Constant pool:
   #1 = Methodref          #7.#27         // java/lang/Object."<init>":()V
!  #2 = InterfaceMethodref #12.#28        // outskirts/lang/langdev/Test$Func.ivk:(I)V
!  #3 = InvokeDynamic      #0:#33         // #0:ivk:([Ljava/lang/String;)Loutskirts/lang/langdev/Test$Func;
!  #4 = Methodref          #11.#34        // outskirts/lang/langdev/Test.the_lambda_invoker:(Loutskirts/lang/langdev/Test$Func;)V
   #5 = Fieldref           #35.#36        // java/lang/System.out:Ljava/io/PrintStream;
   #6 = String             #37            // lambda arg: %s, captured: %s
   #7 = Class              #38            // java/lang/Object
   #8 = Methodref          #39.#40        // java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
   #9 = Methodref          #41.#42        // java/util/Arrays.toString:([Ljava/lang/Object;)Ljava/lang/String;
  #10 = Methodref          #43.#44        // java/io/PrintStream.printf:(Ljava/lang/String;[Ljava/lang/Object;)Ljava/io/PrintStream;
! #11 = Class              #45            // outskirts/lang/langdev/Test
! #12 = Class              #46            // outskirts/lang/langdev/Test$Func
  #13 = Utf8               Func
! #28 = NameAndType        #47:#48        // ivk:(I)V
  #29 = Utf8               BootstrapMethods
  #30 = MethodHandle       #6:#49         // invokestatic java/lang/invoke/LambdaMetafactory.metafactory:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
  #31 = MethodType         #48            //  (I)V
  #32 = MethodHandle       #6:#50         // invokestatic outskirts/lang/langdev/Test.lambda$main$0:([Ljava/lang/String;I)V
! #33 = NameAndType        #47:#51        // ivk:([Ljava/lang/String;)Loutskirts/lang/langdev/Test$Func;
! #34 = NameAndType        #19:#20        // the_lambda_invoker:(Loutskirts/lang/langdev/Test$Func;)V
  #44 = NameAndType        #62:#63        // printf:(Ljava/lang/String;[Ljava/lang/Object;)Ljava/io/PrintStream;
  #45 = Utf8               outskirts/lang/langdev/Test
  #46 = Utf8               outskirts/lang/langdev/Test$Func
! #47 = Utf8               ivk
  #48 = Utf8               (I)V
  #49 = Methodref          #64.#65        // java/lang/invoke/LambdaMetafactory.metafactory:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
  #50 = Methodref          #11.#66        // outskirts/lang/langdev/Test.lambda$main$0:([Ljava/lang/String;I)V
! #51 = Utf8               ([Ljava/lang/String;)Loutskirts/lang/langdev/Test$Func;
  #64 = Class              #67            // java/lang/invoke/LambdaMetafactory
  #65 = NameAndType        #68:#71        // metafactory:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
  #66 = NameAndType        #23:#24        // lambda$main$0:([Ljava/lang/String;I)V
  #67 = Utf8               java/lang/invoke/LambdaMetafactory
  #68 = Utf8               metafactory
  #69 = Class              #73            // java/lang/invoke/MethodHandles$Lookup
  #70 = Utf8               Lookup
  #71 = Utf8               (Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
  #72 = Class              #74            // java/lang/invoke/MethodHandles
  #73 = Utf8               java/lang/invoke/MethodHandles$Lookup
  #74 = Utf8               java/lang/invoke/MethodHandles
{
  public outskirts.lang.langdev.Test();
  ...

  !// func.ivk(2);  originally.
  private static void the_lambda_invoker(outskirts.lang.langdev.Test$Func);
    descriptor: (Loutskirts/lang/langdev/Test$Func;)V
    flags: ACC_PRIVATE, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: iconst_2
         2: invokeinterface #2,  2            // InterfaceMethod outskirts/lang/langdev/Test$Func.ivk:(I)V
         7: return
      LineNumberTable:
        line 12: 0
        line 13: 7
        
  !// i.e. the_lambda_invoker(args, *invokedynamic );
  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokedynamic #3,  0              // InvokeDynamic #0:ivk:([Ljava/lang/String;)Loutskirts/lang/langdev/Test$Func;
         6: invokestatic  #4                  // Method the_lambda_invoker:(Loutskirts/lang/langdev/Test$Func;)V
         9: return
      LineNumberTable:
        line 16: 0
        line 17: 9

  !// System.out.printf("...", String.valueOf(i2), Arrays.toString(s1));  almost originally. lambda body.
  private static void lambda$main$0(java.lang.String[], int);
    descriptor: ([Ljava/lang/String;I)V
    flags: ACC_PRIVATE, ACC_STATIC, ACC_SYNTHETIC
    Code:
      stack=6, locals=2, args_size=2
         0: getstatic     #5                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #6                  // String lambda arg: %s, captured: %s
         5: iconst_2
         6: anewarray     #7                  // class java/lang/Object
         9: dup
        10: iconst_0
        11: iload_1
        12: invokestatic  #8                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
        15: aastore
        16: dup
        17: iconst_1
        18: aload_0
        19: invokestatic  #9                  // Method java/util/Arrays.toString:([Ljava/lang/Object;)Ljava/lang/String;
        22: aastore
        23: invokevirtual #10                 // Method java/io/PrintStream.printf:(Ljava/lang/String;[Ljava/lang/Object;)Ljava/io/PrintStream;
        26: pop
        27: return
      LineNumberTable:
        line 16: 0
}
SourceFile: "Test.java"
InnerClasses:
     static #13= #12 of #11; //Func=class outskirts/lang/langdev/Test$Func of class outskirts/lang/langdev/Test
     public static final #70= #69 of #72; //Lookup=class java/lang/invoke/MethodHandles$Lookup of class java/lang/invoke/MethodHandles
BootstrapMethods:
  0: #30 invokestatic java/lang/invoke/LambdaMetafactory.metafactory:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
    Method arguments:
      #31 (I)V
      #32 invokestatic outskirts/lang/langdev/Test.lambda$main$0:([Ljava/lang/String;I)V
      #31 (I)V

```
</details>

经过检查bytecode, 整个类的成员大致为:
```java
public class Test {
    private static void the_lambda_invoker(Func func);  // original. func.ivk(2).
    public  static void main(String[] args);            // the lambda-part was differ.  
    // the_lambda_invoker((Func)invokedynamic<...>(args));  // pesudo code.
    
    private static void lambda$main$0(String[] a1, int a2);  // compiler gen. almost the original lambda-body.
    // System.out.printf("...", new Object[]{String.valueOf(a2), Arrays.toString(a1)});
}
```

其中: 
1. 主体: 静态私有方法 `lambda$main$0` 被生成了，他正是lambda表达式的主体!。其参数包括捕捉参数和lambda参数。
2. 转发: main方法中 原lambda表达式的位置 被一个invokedynamic "代替"了。

> 我认为这种设计很好，将lambda体分离出来 成为一个静态方法 包含对应的参数，十分纯粹。

## 通过 invokedynamic, 让函数接口 作为 lambda主体 的调用包装.

到此为止，我们已经知道
lambda代码块的实际主体(一个单独的静态函数), 接下来的问题是 如何将这个lambda主体函数 包装进`函数接口`中。(或者说 如何让函数接口的调用 转发到lambda主体函数上)。

这个'调用包装'，即是 invokedynamic 的用意。
实际上的实现可能有一些复杂，但是为了利于理解 请先看看这个例子: (示意代码: !实际细节不是这样的)
```
public static void main(String[] args) {
    the_lambda_invoker(new Func() {
        @Override
        public void ivk(int i) {
            Test.lambda$main$0(args, i);
        }
    })
}
```

### invokedynamic 指令 构成包装

该指令细节较为繁杂，需要的话可以单独搜寻。但是此处还是主要介绍大致执行过程。

```
public static CallSite metafactory(MethodHandles.Lookup caller, 
                                   String invokedName, 
                                   MethodType invokedType, 
                                   MethodType samMethodType, 
                                   MethodHandle implMethod, 
                                   MethodType instantiatedMethodType) throws LambdaConversionException {
    AbstractValidatingLambdaMetafactory mf = new InnerClassLambdaMetafactory(caller, invokedType, invokedName, samMethodType, implMethod, instantiatedMethodType, false, EMPTY_CLASS_ARRAY, EMPTY_MT_ARRAY);
    mf.validateMetafactoryArgs();
    return mf.buildCallSite();
}
```

查看被生成的 函数接口包装类:  
> 使用VM参数`-Djdk.internal.lambda.dumpProxyClasses` 可导出合成类于运行目录.
```
import java.lang.invoke.LambdaForm.Hidden;
import outskirts.lang.langdev.Test.Func;

// $FF: synthetic class
final class Test$$Lambda$14 implements Func {
    private final String[] arg$1;

    private Test$$Lambda$14(String[] var1) {
        this.arg$1 = var1;
    }

    private static Func get$Lambda(String[] var0) {
        return new Test$$Lambda$14(var0);
    }

    @Hidden
    public void ivk(int var1) {
        Test.lambda$main$0(this.arg$1, var1);
    }
}
```
其中的 arg$1 字段是 captured-args.


## Fun Tests

<details>

### 当lambda的参数没有被使用，那么 被生成的lambda主体静态函数 会包含lambda参数吗

这是一个细节，但是或许会体现出java语言开发者的心思 或许是很有趣的 他们是否行为很灵活?  
除此之外 我们打算("戏剧性地")使用一些captured参数，看看lambda主体静态函数签名到底会是怎样的.

```
public static void main(String[] args) {
    the_lambda_invoker(i -> System.out.println(Arrays.toString(args)));  
    // lambda-arg "i" is idle. and captured-arg "args" is been used.
    // let's see what lambda-body-function will be gen from java. shall it still includes idling lambda-args?
}
```
结果:
```
private static void lambda$main$0(java.lang.String[], int);
    descriptor: ([Ljava/lang/String;I)V
    flags: ACC_PRIVATE, ACC_STATIC, ACC_SYNTHETIC
    Code:
      stack=2, locals=2, args_size=2
         0: getstatic     #5                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: aload_0
         4: invokestatic  #6                  // Method java/util/Arrays.toString:([Ljava/lang/Object;)Ljava/lang/String;
         7: invokevirtual #7                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        10: return
      LineNumberTable:
        line 16: 0

```
嗯 有一些没有预想的有劲。即使lambda参数未使用 仍然会被包含入lambda主体静态方法签名。

</details>







# Cplusplus CPP11 Lambda

```cpp
[]() { } // barebone lambda

[capture_list] (param_list) mutable exception_list -> return_type {
    body
}
```

匿名函数对象

1. 创建lambda类，captured-args, 函数体 重载 operator(). 
2. 创建lambda实例
3. 调用 operator()
```cpp
class lambda_xx
{
private:
    int a;
    int b;
public:
    lambda_xx(int _a, int _b) : a(_a), b(_b) {}
    
    bool operator()(int x, int y) throw() {
        return a + b > x + y;
    }
};
void Test() {
    int a = 1;
    int b = 2;
    lambda_xx l = lambda_xx(a, b);
    bool ret = l.operator()(3, 4);
}
```







# V8 vs. JVM Hotspot Performance Test.
Pushed on 22 Aug, 2021. [jks]

Many people says that V8 is so effective, "Why V8 So Fast"..   
I actually dont believe so much, think, Javascript(EMCAS) even doesn't have a int or float, just all high-level objects, how can it been fast..?

So let's test.

#### Rules
*We will use In-Program-Timer instead of Operation-System-External-Timer etc, because we only want test for sample-code-execution, regardless of syntax-parse-process of js or compile-process of java.*

```
Runtime: (x86_64)
  Nodejs 11.15.0.
  JVM Hotsopt 11.
```
## String Concatenation Test

Since I can't search out a Performance Test article on English Community, however I found a Chinese man test([link](https://zhuanlan.zhihu.com/p/349922330)), in the test(String Contact Speed Test), the result is: v8 used 6.078s, jvm used 57.928s. I just laughed, how its possible.? so let we test out again:

[comment]: <> (we added one line to print the concatenated-string info, for prevents the possible of V8 optimization simply removed the string-concatenate-operation since we haven't use it.)
Javascript:
```
let s = Date.now();

let str = "*";
for (let i = 0;i < 1000000;i++) {
    // console.log(i);     // removed, the print isn't performace-accuracy, its dependents on buffer size or some random detail.
    str += "*";
}
console.log(str.length);   // added. for prevent optmization removed the str.

console.log((Date.now() - s) / 1000);
```
```
1000001
1.709
```

Java:
```
...main func...
long l = System.currentTimeMillis();

String s = "*";
for (int i = 0;i < 1_000_000;i++) {
    s += "*";
}
System.out.println(s.length());

System.out.println((System.currentTimeMillis() - l) / 1000f);
```
```
1000001
49.526
```

little fun.. now I can understand that why sometimes JS can faster than Cplusplus.. the random situation-related-optimization vs specified-detail-impl...

so let switch another way, an optimized way, use 'unlocked' StringBuilder yeah.

Java (StringBuilder):
```
1000001
0.042
```


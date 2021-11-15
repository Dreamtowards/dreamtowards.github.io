
## O.O.P. The static keyword for Members. 

In OOP supported languages, there are some way to access 'this' instance of that object in the member-function.

1. some GPPLs declares 'this' implicitly by not using 'static' modifier. e.g. Cpp, Java, MS-Csharp. 
    ```
    void do_sth(int i) {
        println(this);
    }
    ```
   + \+ Syntactical-Cleaner
   + \- Ambiguous. <small>*where 'this' comes from and stands for.?*</small>
   + \- Not-Flexible. <small>*member-functions always cannot been used as static-function.*</small>
   + \- Not-Function-Consistent. <small>*static-function and member-function. instead of just parameter-feature*</small>


2. while some GPPLs explicit declares 'self' in the function-parameters. and not need of 'static' modifier 'at all'. e.g. Rust, pythen. (all functions are static, just 1st-self-param functions can be used as instance-function)
    ```
    void do_sth_(MyKlass* self, int i) {
        println(self)
    }
    ```
    - \+ Logical-Cleaner
    - \+ Flexible. <small>*all functions are 'static'. some of them can used as instance-function*</small>
    - \+ Function-Consistent <small>*all functions same rules. not need think about whether static.*</small>
    - \- Syntactical little chaos. <small>*lots of duplicated 'self' declarations*</small>
    - \- Not-Consistent-with-Member-Variables little chaos. <small>*for static member-variables, still need 'static' keyword.*</small>

Which is 'Better'.?

The former is like Syntax-Cleaner but little ambiguous, not-flexible not-function-consistent.  
while the latter is like Logical-Cleaner, flexible and function-consistent but Syntax-LittleChaos and non-consistent-for-member-variables.


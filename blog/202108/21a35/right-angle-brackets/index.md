
## Ambiguity on `>>` for Generics/Template vs. Right-Shifts

```
CPP11

A<B<C>> 

a >> b 
```

In languages that like Cplusplus or Java, there have a right-shift operator `a >> b` and also a generics brackets `A<B<C>>`.

the `>>` should be done at Lexical-time, but seems simply can't. in two example above, the former operator token should be a single `>>`, while the latter should be two `>`. 

---

Stroustrup mentioned this thing on CPP11, not an exact solution, but a little summary. `to get that example "correct" the three stages has somehow to cooperate.`
https://www.stroustrup.com/C++11FAQ.html#:~:text=to%20get%20that%20example%20%22correct%22%20the%20three%20stages%20has%20somehow%20to%20cooperate  
https://www.stroustrup.com/C++11FAQ.html

---

in this paper, the author talked about 3 solutions
1. Match `<` on Lexical-time. per `<` matches a single `>`.  
   In addition: consider `(..)` as 'matching scope' for avoid this `A<B<( c>d>e )>>` problem. (lasting `>` has been wrongly parsed as right-shift since they had been pop-ed at first two `>` in the parentheses)
2. parsing all `>>` as `>` when parsing templates.
3. Lexical parse all `>>` as multiple `>`, and modify grammar `rsh := '>' '>'`, thus things like this `int i = 10000 >  > x;` for rsh are allowed..

http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1757.html

---

Javac solution: (jdk7)

javac's lexer, only hold one token at a time.  
when parse Generics, meet `>>=` consume and modify to `>=`, `>>` -> `>`. 

(but I just think this may quite a little 'risk'.

---

Overall, there seems like 4 solutions:


1. Every `<` matches a single `>` in Lexical time, and consider `{[(` as 'matching-scopes' for avoid situations like `A<B<( c>d>e )>>` or T<(x >> 1)>.
2. Lexical parsing all `>>` as multiple single `>`, modify rsh grammar as `rsh := '>' '>'`, and when parsing rsh, check text-position of that two `>` token, make sure they are 'connected'(no gap).
3. Reading tokens as Demand and In-time. e.g. when parsing template: `if (read(">")) closeTemplate();`, rsh: `shifts() { if (read("<<")) parselsh(); elif (read(">>")) parseRsh(); }`
4. Replace `>>=` to `>=` at runtime when parsing template. jdk7-javac-approach.

I think 2 is not that good. 

















https://stackoverflow.com/questions/50743644/how-c-compilers-differentiate-the-token-for-binary-operator-and-for-templa

https://www.google.com/search?q=clang+template+%3E%3E+lexer
https://www.google.com/search?q=template+and+binary+%3E%3E+lexer

`langdv2` `21c30w`
#  21c30w langd _updates*


`210728` langdv Impl Parser

basque catalan galician polish
dutch french german indonesian slovak
latin norwegian

### QrA Sections

Questions 1, `string[] args` in main function.?:
<details markdown="1">

```
main(array<string> args) { 
    ... 
}
```   
or   
```
main() { 
    array<string> args = runtime.cliargs; 
}
```
.?
</details>


Question 2: `class.memb` or `class::memb` for static access?

Question 3: A.{.m1, .m2.mm3} syntax.?

```
entity.{
    .position.set(0, 0, 0),
    .color.set(Colors.YELLOW),
    .name = TheName
}  // -> entity
```
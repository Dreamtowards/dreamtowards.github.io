
 Circular Dependency Prob.
### 循环依赖 以及Java的编译器及整体机制是如何避免他的
Posted on Aug 9, 2021 / [langdv]


```cpp
// sourcefile Car.hpp
#ifndef CAR_H
#define CAR_H
#include "Wheel.hpp"

class Car {
public:
    Wheel wheel;
    
    void initWheel() {
        wheel.ptrcar = this;
    }
}
#endif
```
```cpp
// Wheel.hpp
#ifndef WHEEL_H
#define WHEEL_H
#include "Car.hpp"

class Wheel {
public:
    Car* ptrcar;
    
    void check() {
        assert(ptrcar->wheel == this);
    }
}
#endif
```
这是一个涉及双方符号/类型和进一步成员信息的互相依赖情况。

此时编译 若入口依赖是`Car`，会出现的问题是 `Wheel.hpp: error: unknown type name 'Car'`.  
即Wheel无法解析Car。因为自入口第一次引入Car之后 Car不会再被定义, 此时Car中的`include wheel`中的`include car`将失效。因此Wheel 无法解析Car.




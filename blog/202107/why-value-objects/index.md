
# Why Java Introduced Valhalla
Posted on Jul 3, 2021.

### Why not uses Stack-Objects, for the Performance and Functionality.
the Heap Objects sometimes are Uncontrollable. for example in high-density vector computations.
instead, the stack-object are always memory well-controlled.

I always think that for small and high-density-allocated "Entity-Objects" (reference objects),
the stack-objects is performance well-enough, in space-aspect its just need an extra RTTI pointer, and in functionality its still perfectly supports inheritances thus also good for Language-Object-Consistence. 

but the java not had ever focused on Stack-Objects anyway, the Escape-Analytics is not-tough. 
the Java just directly introduced ValueObjects/InlineObjects now...

so why not stack-objects but value-objects.? the ValueObjects had no more functionality, then 
its more possibility for the Performance.

> Sergey Kuksenko, Oracle. Java Performance Department.
> https://www.infoq.com/presentations/java-valhalla-inline-types-2020/

# Why C# have Value Objects
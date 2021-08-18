
# The `using` statement. program Referring and Organization.

we had always to refer other programs. 

## Look Around

let's see the designs from some programming languages.

### Dynam Languages

#### Nodejs
```js
let exlib = requires("path/to.js");   // <- exports = ...
```
just simply use a function. little bit cool for small-ranged script-languages    
but certainly, its doesn't fit static languages, and the design in lot aspects might not good.  

<details>

The "Import"/Program-Referring is a hi-weighted design part of a program-language, it's "Entrance"/Bridge of Programs, 
thus, the Program-Referring should be designed as OneBody with the Language, the Co-Core-Part.  
instead of some random extension, or the program been imported might be seen as some random casual carbage. the system just loosing.

nodejs uses a "normal" function to import external programs, in some script-languages it maybe seen as a little bit "flexibly", but it might cause chaos, the Level is confused, Import a program is a big thing, don't confuse with a normal function.

However, this is correct decision for Nodejs. its a runtime, not language spec.

</details>

#### Javascript EMCA ES5

```js
import singlelib from "path/to/the.js";
import {part, of, lib} from "path/to.js";
```
not bad. little bit good for script languages.  
just the multiple-part-import is a considerable special-case.

#### Pythen
```py
import math;   // print math.pi
from math import pi as PI;   // print PI
```
the syntax little bit casual. unnecessary miscellaneous and special-cases.

### Static Languages

#### Java
```java
package my.pkg; 

import java.util.List; 
import static java.util.Collections.emptyList;
```
Good:
1. `import` can only refers Classes. its Clear.
2. statics can also been import by `import static`, isolated with `import`. Clear.
3. `package` used as like a "label", doesn't 'waste' top-level indent/space/weight e.g. Cpp Namespaces.  
  And it's reasonable, packages just prefix of Classes, not anything wired struct.
   
Mid:
1. the "import" keyword, actually its not really import but just referring. however the keyword is kind of cool.
2. the package syntax, doesn't support multi-packages in a single compile-unit as a innate feature in java. but commonly its still should be working.


#### C#
```csharp
using System.Collection;

namespace The.Nmspc { ... }
```
Little-Good:
1. the `using` keyword. is actually referring mean.

Not-Good:
1. `using` can referring a Namespace, which is confusable.


#### Cplusplus
```cpp
#include <string>
// ... std::string
```
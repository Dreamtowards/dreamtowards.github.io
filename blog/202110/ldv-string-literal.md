
## ldv. string literal.
Oct 6, 2021. langdesg.

string literal is essential.  
it provides string in such basic and convenient way.

but there still have several problems.
1. which kind of 'object' should it represents.?
   1. `C, const char*` - a pointer to a pure CharSequence with terminal of '0'.
   2. `Java, const String*` - a 'reference' to an OOP object which have a CharSequence pointer without terminal '0', a length int, and a hash int.

there firstly choose the former kind.

### Impl to char* string-literal.

we have not pushed an immediate pointer as return val yet.  
instead, we used `ldc` instruction which might kind of high-leveled.

the VM will get the constant by index and check its type,  
since it's a utf8 string, the VM just checks is it loaded in the runtime string_constant_pool, if loaded, just push the ptr, else just load it and push the ptr.

this impl is kind of lo-effective, every ldc of string needs a is-loaded check.

but however, it's still correct.
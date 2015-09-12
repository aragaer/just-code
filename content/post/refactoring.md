+++
date = "2015-09-13T00:20:32+03:00"
title = "Refactoring in a C preprocessor"

+++
Assume we have a set of headers. These are used in a large project written in C and maybe some C++. Now assume that the project can be built with a lot of configurations. Each configuration is defined by some flag and there's a number of parameters which have different values in different configurations. There is also some common code for all configurations:

*config1*: FLAG=1 include header1
*config2*: FLAG=2 include header2

#### header1.h:

    #include "common.h"


#### header2.h:

    #include "common.h"


#### common.h:

    #if FLAG == 1
    #define VALUE1 1
    #elif FLAG == 2
    #define VALUE1 2
    #else
    #error Unknown configuration
    #endif

    <repeat several times for other VALUEs>

    <some common code that uses all VALUEs>

That's classic! Replace conditional with polymorphism! But what is polymorphic here?

I don't actually know. But I'd rewrite it like this:


#### header1.h:

    #define VALUE1 1
    #define VALUE2 2
    #include "common.h"


#### header2.h:

    #define VALUE1 2
    #define VALUE2 42
    #include "common.h"


#### common.h:

    #ifndef VALUE1
    #error Unknown configuration
    #endif
    <repeat several times for other VALUEs>

    <some common code that uses all VALUEs>

Definitely much more clear.

> But we're using this "common.h" somewhere else as well. It could break there, so we don't make this change now. Instead we'll create a new ticket in our issue tracking system and make the change later.

Oops. There's no automated test system. Which means we don't have courage to change the code....
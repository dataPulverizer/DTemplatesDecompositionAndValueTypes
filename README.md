# D templates: decomposition and value types

## Introduction

So you've started writing D code and are happy writing simple templates like the only below to calculate dot product.

```d
//value.d
import std.stdio: writeln;
import std.traits: isFloatingPoint;

/*
  To compile:
  dmd value.d && ./value
*/

T dot(T)(T[] x, T[] y)
if(isFloatingPoint!T)
{
  T ret = 0;
  foreach(i; 0..x.length)
    ret += x[i]*y[i];
  return ret;
}

void main()
{
  auto x = [1.0, 2.0, 3.0];
  writeln("dot: ", x.dot(x));
}
```
## Decomposition
Now you need write something a bit more complicated for example to get the number of elements in a static array. You can do this by decomposing the types using templates/template functions to obtain the number of elements in your static array. The example below does this using a template function.

```
//decomp.d
import std.stdio: writeln;

/*
  //To compile and run:
  rdmd decomp.d
*/

auto getStaticArraySize(A: T[N], T, alias N)(A arr)
{
  return N;
}

void main()
{
  long[4] arr = [1L, 2L, 3L, 4L];
  writeln("Static Array Size: ", getStaticArraySize(arr));
}
```

You can also do this with a template:

```
//decompT.d
import std.stdio: writeln;

/*
  //To compile and run:
  rdmd decompT.d
*/

template getStaticArraySize(A: T[N], T, alias N)
{
  enum getStaticArraySize = N;
}

void main()
{
  long[4] arr = [1L, 2L, 3L, 4L];
  writeln("Static Array Size using template: ", getStaticArraySize!(typeof(arr)));
}
```
## Value types

The terminology here is a bit confusing, a common meaning of "value type" differentiates value and reference variables. That's not what this is about, here we are talking about when a value is actually part of a type signature rather than being contained in an object ... ooo very interesting!  Consider the following:

```
struct Value(alias val){}
```
That is what I'm referring to as a "value type". Here the value `val` is part of the type signature of the `Value` template. So if we create on of these, the value in `val` will be available to us at compile time, but how do we get hold of `val`? We use a decomposition pattern as before:

```
import std.stdio: writeln;
import std.traits: isFloatingPoint;

/*
  //To compile and run:
  rdmd value.d
*/

struct Value(alias val){}

auto getValue(A: V!N, alias V, alias N)(A x)
{
  return N;
}


void main()
{
  alias myValueType = Value!(4.2); // This is the type
  auto val = myValueType();
  writeln("The type signature: ", myValueType.stringof);
  writeln("Instance of the value type: ", val);
  writeln("Get the value from the type signature: ", getValue(val));
}
```

That's it.


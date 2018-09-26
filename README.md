# Nukepayload2.CompilerServices.InteriorPointer
Provides functionality for manipulating interior pointers with VB.

[Get on nuget](https://www.nuget.org/packages/Nukepayload2.CompilerServices.InteriorPointer)

- Convert ByRef to interior pointer, or convert interior pointer back to ByRef.
- Load or store unmanaged values indirectly.
- No extra dependencies.
- Supports addition, subtraction, increment, decrement, equality, inequality, greater than, greater than or equal, less than, and less than or equal operator.
- Provides helper functions for easier code conversion from C# or C++ to VB.

## Scenarios
- Don't want to see small c# projects among your huge VB solution.
- Prefer VB syntax and want to implement pointer algorithms.
- Want to convert c# codes that contains `Span(Of T)` without marking everything `<Obsolete>`.

## Guides
### C-style operators vs InteriorPointer members

|C# sample code|Remarks|InteriorPointer with VB|
|-|-|-|
|`var value = *p;`|p is a pointer of an unmanaged type|`Dim value = p.UnmanagedItem`|
|`var value = *p++;`|p is a pointer of an unmanaged type|`Dim value = p.GetAndIncrement.UnmanagedItem`|
|`var value = *++p;`|p is a pointer of an unmanaged type|`Dim value = p.IncrementAndGet.UnmanagedItem`|
|`var value = *p--;`|p is a pointer of an unmanaged type|`Dim value = p.GetAndDecrement.UnmanagedItem`|
|`var value = *--p;`|p is a pointer of an unmanaged type|`Dim value = p.DecrementAndGet.UnmanagedItem`|
|`*p = value;`|p is a pointer of an unmanaged type|`p.UnmanagedItem = value`|
|`*p++ = value;`|p is a pointer of an unmanaged type|`p.GetAndIncrement.SetUnmanagedItem(value)`|
|`var p = (T*)Unsafe.AsPointer(ref r);`|Converts ref to raw pointer|`Dim p = r.UnsafeByRefToTypedPtr`|
|`ref T r = Unsafe.AsRef<T>((void*)p);`|p is IntPtr|`Dim r = CType(p, InteriorPointer(Of T))`|
|`T r = Unsafe.AsRef<T>((void*)p);`|p is IntPtr|`Dim r = p.UnsafePtrToByRef(Of T)`|
|`*p1 = *p2;`|p1 is a pointer of an unmanaged type|`p1.CopyUnmanagedFrom(p2)`|
|`p+=2`|p is void*|`p+=2`|
|`p-=2`|p is void*|`p-=2`|
|`p+=2`|p is T*|`p+=2`|
|`p-=2`|p is T*|`p-=2`|
|`p1 < p2`|p1 is pointer|`p1 < p2`|
|`p1 > p2`|p1 is pointer|`p1 > p2`|
|`p1 <= p2`|p1 is pointer|`p1 <= p2`|
|`p1 >= p2`|p1 is pointer|`p1 >= p2`|
|`p1 = (void*)p2`|p1 is pointer|`p1 = CType(p2, InteriorPointer)`|
|`p1 = (T*)p2`|p1 is pointer|`p1 = CType(p2, InteriorPointer(Of T))`|

### C# pointer keywords to VB with InteriorPointer
#### stackalloc
__C#__
```C#
char* chrBuf = stackalloc char[50];
```
__VB__
```VB
<StructLayout(LayoutKind.Sequential, Size:=50 * 2)>
Structure StackAllocChar50
End Structure

Dim chrBuf = CType(resultBufValue.UnsafeByRefToPtr, InteriorPointer(Of Char))
```

#### fixed
__C#__
```C#
char[] values = new char[4096];
fixed(char* chrBuf = &values[0])
{
    // use chrBuf
}
```
__VB__
```VB
Dim values(4095) As Char
Using pinPtr = Fixed(values)
    Dim chrBuf = arr(0).UnsafeByRefToTypedPtr
    ' use chrBuf
End Using
```
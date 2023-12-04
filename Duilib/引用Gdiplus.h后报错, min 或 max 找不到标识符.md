## 增加`Gdiplus`相应的头文件`Gdiplus.h`后大概率会出现如下报错：

```bash
c:\program files (x86)\windows kits\8.1\include\um\GdiplusTypes.h(475): error C3861: “min”: 找不到标识符
c:\program files (x86)\windows kits\8.1\include\um\GdiplusTypes.h(477): error C3861: “max”: 找不到标识符

C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\GdiplusTypes.h(479,22): error C3861: “min”: 找不到标识符
C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um\GdiplusTypes.h(481,21): error C3861: “max”: 找不到标识符
```
升级或安装`SDK 10.0.22000.0` 可以修复此问题

或者用以下几个方法修复

### 解决方法一，

> From the C++ team in Windows:
>
> The header itself cannot be changed to use `std::min, std::max`. GDI+ supports C client code, and even with a `__cplusplus` check, `std::min/std::max`require C++ 14 or later. The SDK has a number of headers that still use unqualified min/max, and only the C++/WinRT headers use `std::min/std::max`, as usage there is already constrained to C++ 17 or later. The workaround is to add `std::min/std::max` explicitly before including `gdiplustypes.h` (or any other Windows header that uses min/max):
>
> ```c++
> #define NOMINMAX
> #include "windows.h"
> using std::min;
> using std::max;
> #include “GdiplusTypes.h”
> ```

```cpp
#include <algorithm>
namespace Gdiplus
{
	using std::min;
	using std::max;
};
#include <Gdiplus.h>
#pragma comment( lib, "gdiplus.lib" )
```

### 解决方法二：

```cpp
// 没定义min、max则定义
//  Define min max macros required by GDI+ headers.
#ifndef max
#define max(a,b)            (((a) > (b)) ? (a) : (b))
#define _MinDefTmp_
#endif

#ifndef min
#define min(a,b)            (((a) < (b)) ? (a) : (b))
#define _MaxDefTmp_
#endif
 
#include <Gdiplus.h>
#pragma comment( lib, "gdiplus.lib" )
 
// 临时定义的则取消定义，避免其他地方错误
#ifdef _MinDefTmp_
#undef min
#undef _MinDefTmp_
#endif

#ifdef _MaxDefTmp_
#undef max
#undef _MaxDefTmp_
#endif
```

## stackoverflow的方案

> min and max in gdiplus are **really annoying** in modern c++ codebase.
> I have done the trick around gdiplus.h so many times in so many project it should be fixed.

[stackoverflow response that saved me](https://stackoverflow.com/a/45958334/2054398)

```c++
//  global compilation flag configuring windows sdk headers
//  preventing inclusion of min and max macros clashing with <limits>
#define NOMINMAX 1

//  override byte to prevent clashes with <cstddef>
#define byte win_byte_override

#include <Windows.h> // gdi plus requires Windows.h
// ...includes for other windows header that may use byte...

//  Define min max macros required by GDI+ headers.
#ifndef max
#define max(a,b) (((a) > (b)) ? (a) : (b))
#else
#error max macro is already defined
#endif
#ifndef min
#define min(a,b) (((a) < (b)) ? (a) : (b))
#else
#error min macro is already defined
#endif

#include <gdiplus.h>

//  Undefine min max macros so they won't collide with <limits> header content.
#undef min
#undef max

//  Undefine byte macros so it won't collide with <cstddef> header content.
#undef byte
```

附带 windows 中 `minwindef.h` 的 `min、max` 宏定义

```c++
#ifndef NOMINMAX
 
#ifndef max
#define max(a,b)            (((a) > (b)) ? (a) : (b))
#endif
 
#ifndef min
#define min(a,b)            (((a) < (b)) ? (a) : (b))
#endif
 
#endif  /* NOMINMAX */
```


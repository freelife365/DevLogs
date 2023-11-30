## DPI 是什么

DPI 全名是 `Dots Per Inch`, 表示显示设备在每英寸上有多少个像素点。Windows 中默认为 96。

Windows 采用 DWM(Desktop Window Manager)来负责窗口显示，当 DPI 较高时，而软件又没有自己处理，这时系统会帮你适配，如前面设置的 125%的缩放，效果类似放大图片，所以你的程序看起来会模糊，这就是界面库要支持 DPI 的重要性。

所谓界面库支持 DPI，就是根据当前的缩放比例，使用相对应分辨率的资源（图片和文字等）。

## DPI 在 Duilib 中使用

有两种方法，一种是函数实现，一种是清单文件设置

### 一、函数实现

直接在你的 InitWindows()里调用,

`m_pm.GetDPIObj()->SetDPIAwareness(PROCESS_PER_MONITOR_DPI_AWARE);`
内部是调用 WindowsAPI

```cpp
 HRESULT WINAPI SetProcessDpiAwareness(_In_ PROCESS_DPI_AWARENESSvalue);
typedef enum _PROCESS_DPI_AWARENESS {
  PROCESS_DPI_UNAWARE            = 0,
  PROCESS_SYSTEM_DPI_AWARE       = 1,
  PROCESS_PER_MONITOR_DPI_AWARE  = 2
} PROCESS_DPI_AWARENESS;
```

该函数告诉系统，要不要你帮我适配，就是前面讲的给我强行拉伸。

- PROCESS_DPI_UNAWARE 在软件启动时，操作系统会自动将软件进行缩放拉伸；在系统 dpi 缩放改变时，系统也会将软件自动进行缩放拉伸，但软件**不会收到** `WM_DPICHANGED` 消息
- PROCESS_SYSTEM_DPI_AWARE 在软件启动时，系统不会将软件进行缩放拉伸；在系统 dpi 缩放改变时，系统会将软件进行缩放拉伸，但软件仍然**不会收到** `WM_DPICHANGED`消息。
- PROCESS_PER_MONITOR_DPI_AWARE 在软件启动时，系统不会将软件进行缩放拉伸；
  同样，在系统 dpi 缩放改变时，系统也不会将软件进行缩放拉伸，但软件**会收到** `WM_DPICHANGED` 消息。如果你的程序支持 PROCESS_PER_MONITOR_DPI_AWARE，当你的窗口移动到 DPI 不同的显示器上时，会收到 WM_DPICHANGED 消息。

主要相关 API：

- SetProcessDpiAwareness ：设置当前进程的 DPI 感知等级。

- GetDpiForMonitor ：查询显示的 DPI。

- MonitorFromPoint ：获取指定点的显示器的句柄。

- MonitorFromRect ：获取与指定矩形相交面积最大的显示器句柄。

### 二、清单文件配置

声明 DPI 感知级别

```xml
    <asmv3:application xmlns:asmv3="urn:schemas-microsoft-com:asm.v3">
        <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
            <dpiAware>true</dpiAware>
        </asmv3:windowsSettings>
    </asmv3:application>
```

通过设置 dpiAware 为 true 就声明应用支持 DPI 缩放。
如果想要支持 Per Monitor 模式的 DPI 缩放，则需要设置 dpiAware 值为 true/PM。这种方式是官方推荐的方式。
需要注意的是，如果程序是一个 DLL，则 Manifest 中的设置会被忽略。

PS: 刚发现清单文件配置的 duilib 不能动态设置 DPI，why? 暂时先使用第一种方法吧！-\_-

## 动态设置 DPI 显示：

```cpp
//m_pm为窗口管理类，有的维护库为m_pManager
m_pm.SetDPI(120);  //当前窗口
m_pm.SetAllDPI(120);//所有窗口
```

注意, 120 是 DPI，96x125%=120，duilib 默认是 96,就是不缩放。

一般跟随系统的缩放比例可以写成：

```cpp
int sysDPI =  GetDeviceCaps(m_pm.GetPaintDC(), LOGPIXELSX);//获取系统DPI
m_pm.SetAllDPI(sysDPI);
//LOGPIXELSX: Logical pixels/inch in X
//LOGPIXELSY: Logical pixels/inch in Y
```

### 确定 DPI 缩放因子

在控件和窗口创建之前需要先确定 DPI 缩放因子。

#### GetDeviceCaps

通过`GetDeviceCaps`可以获取到水平和垂直方向的 DPI 值，得到了 DPI 值，还需要明确支持的 DPI 缩放范围。
Windows 中，目前有 96、120、144、192 四种标准的 DPI，当然用户也可以设置为其它 DPI 大小。
实际适配时，支持 96、120、144、192 四种标准 DPI 即可，其它 DPI 则可以取相近的标准 DPI。
最后根据 DPI 就可以确定水平和垂直方向的缩放因子。

#### GetSystemMetrics

通过 `GetSystemMetrics` 可以得到显示器的分辨率，再根据前面得到的 DPI 值，就可以确定当前的有效分辨率。
如果有效分辨率过低（Windows 推荐的有效分辨率不低于 `1024*720`），可能会导致界面显示不全，这个时候就需要适当的缩小缩放因子或者提示用户。

#### DPI 和缩放对应关系

- 96 DPI = 100% scaling
- 120 DPI = 125% scaling
- 144 DPI = 150% scaling
- 168 DPI = 175% scaling
- 192 DPI = 200% scaling

#### 代码适配

对于代码中构建的窗口和布局，适配的工作量就要大一些了。这一部分主要工作就是将原先代码中动态设计算的尺寸与缩放因子相乘得到缩放后的尺寸。

对于代码中使用的常量尺寸，在定义的时候可以考虑`#define` 而不是 `const` 常量。例如对于一个窗口宽度 `WND_WIDTH`
`#define WND_WIDTH 100 * dpi_x`
而不是用
`const int WND_WIDTH = 100 * dpi_x;`

这样做的优点是如果应用考虑做到 Per-Monitor 级别的适配或者需要动态切换缩放比例时，这部分的代码可以直接使用而不需要再做修改了。

#### 字体适配

Windows 使用 `CreateFontIndirect` 创建字体，在创建的时候，只需要对 `lfHeight` 进行缩放就可以了。

## 图片资源

```xml
<Button name="maxbtn" width="27" height="21" normalimage="file='sys_btn_max.png' source='0,0,26,21'" hotimage="file='sys_btn_max.png' source='27,0,53,21'" pushedimage="file='sys_btn_max.png' source='54,0,80,21'"/>
```

例如这一个最大化按钮，需要准备 5 套大小的图片,对应不同的 DPI.在 xml 里面只需要指明 100% 的图片名称，其他的 DPI 的图片会自动获取.

- sys_btn_max.png  
  81 x 22 像素
- sys_btn_max@125.png  
  99 x 27 像素
- sys_btn_max@150.png  
  122 x 33 像素
- sys_btn_max@175.png  
  142 x 39 像素
- sys_btn_max@200.png  
  162 x 44 像素
  
## Duilib 修改 

有些控件或者属性可能还没有提供 DPI 适配,需要自己修改，修改时需要注意下面几点

- `m_cXY m_rcPadding m_cxyFixed m_cxyMin`  
  类似这些保存 xml 里面的声明的值的变量，里面放的是 100% 的值。

- `m_rcItem` 保存的是缩放后的值

- 调用 `GetManager()->GetDPIObj()->Scale` 进行缩放的时候需要判断 `if(GetManager()==NULL)`

- `m_sImageModify.SmallFormat(_T("dest='%d,%d,%d,%d'"), d1, d2, d3,d4);  `  
  d1 d2 d3 d4 需要是 100% 的值
  
## 资源适配

一般来说，DPI 不同，界面的大小不同，需要的资源也就不同。

### 资源目录

每一种 DPI 都有一个对应的资源目录，资源在这些目录下采用相同的相对路径。
例如一张 test.png 图片分辨适配 1x(96 DPI)和 1.5x(144 DPI)时，其对应的目录可以是 `res/xxxx/test.png` 和`res@1.5x/xxxx/test.png`。

### 图片资源

对于图片资源，优先使用对应目录下的资源。如果在对应目录下没有找到资源，则可以使用最其他 DPI 目录下的资源。
使用其他 DPI 目录的图片资源时，在使用之前还需要先进行缩放处理，缩放的比例由相关的两个 DPI 的比值决定，以查找 120 DPI 下 test.png 为例。

具体流程如下：

1. 查找 DPI=120,test.png
2. DPI=120 目录下没有
3. 在其它目录下查找
4. DPI=192 目录下找到
5. 图片缩小到原尺寸的 120/192
6. 缓存图片供 UI 使用



### 适配步骤

    了解了适配相关的基础概念之后，接下来开始对应用进行适配了。

在未对 DPI 进行适配之前，窗口、控件构建的大致过程如下：

1. 加载资源
2. 创建窗口、控件
3. 代码中动态调整

适配的主要工作包括资源的适配和尺寸调整两个方面，调整之后的流程如下：

1. 确定 DPI 缩放因子
2. 加载对应的资源
3. 转换 XML 中的尺寸
4. 创建窗口、控件
5. 代码中转换单位运态调整

### xml 等构建 UI 的资源

一般来说，除非想在不同的 DPI、有效分辨率下采用不同的布局方式，建议只使用一套 xml 资源。这样可以减少适配的工作量，同时避免维护多套 xml 带来的麻烦。

## 窗口和控件缩放

窗口和控件的缩放是 DPI 适配的主要工作。

### xml 适配

窗口和控件构建支持使用 xml 进行配置，对于 xml 构建的窗口和布局适配起来比较简单，工作量也比较小，只需要在读取 xml 的时候直接与缩放因子相乘就行了。为了使 xml 更灵活，xml 中最好支持使用不同的单位进行配置。类似 px,pt,dp 这样的单位。

## 兼容 win10

发现，duilib 并不支持 win10 的 DPI。方法一里的设置一直不成功，看了会源码发现，是`SetDPIAwareness`里调用`IsWindows8Point1OrGreater()`一直失败，该函数顾名思义就是判断系统版是否 8.1 及以上版本。
注意`win8.1`及以上才支持 DPI Aware，所以这里要判断。
发现函数是拷贝了`win10SDK`的 VersionHelpers.h 里的源码。
内部调用

```cpp
VerifyVersionInfo(...)
```

### MSDN 关于这个解释了一波

Targeting your application for Windows
`GetVersion`, `GetVersionEx`, `VerifyVersionInfo`这些函数最高只能识别出 Win8，`IsWindowsVersionOrGreater`系列函数只是`VerifyVersionInfo`的一个封装，所以也存在同样的问题。
MSDN 里说需要在配置清单里说明程序兼容 win10

```xml
<?xml version="1.0" encoding="UTF-8"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <!-- OSVersion -->
    <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
        <application>
            <!-- Windows 10 -->
            <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}"/>
            <!-- Windows 8.1 -->
            <supportedOS Id="{1f676c76-80e1-4239-95bb-83d0f6d0da78}"/>
            <!-- Windows Vista -->
            <supportedOS Id="{e2011457-1546-43c5-a5fe-008deee3d3f0}"/>
            <!-- Windows 7 -->
            <supportedOS Id="{35138b9a-5d96-4fbd-8e2d-a2440225f93a}"/>
            <!-- Windows 8 -->
            <supportedOS Id="{4a2f28e3-53b9-4441-ba9c-d69d4a4a6e38}"/>
        </application>
    </compatibility>
</assembly>
```

在 visual studio 里配置清单文件，将上述清单代码拷贝进 xml 文件，建议`软件名.xml`

项目属性-清单工具-输入输出-附加清单文件，加入$(TargetName).xml。

可以选择是否嵌入清单选项，否的话，程序 exe 目录下还会生成响应的 xxxx.manifest，软件运行需要带上它，是的话就不用。

## 应用声明支持 HiDPI

Project proptery -> Manifest Tool -> Input and Output -> DPI Awareness 设置为下面的值之一

- High DPI Aware
- Per Monitor High DPI Aware

### 清单文件

> 支持 Windows 6.0 界面库、支持管理员权限、兼容 WIN8/WIN10 下取系统版本、兼容 DPI Aware

```xml
<?xml version="1.0" encoding="UTF-8"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <!-- Windows 6.0 Style -->
    <dependency>
        <dependentAssembly>
            <assemblyIdentity type="win32" name="Microsoft.Windows.Common-Controls" version="6.0.0.0" processorArchitecture="x86" publicKeyToken="6595b64144ccf1df" language="*"></assemblyIdentity>
        </dependentAssembly>
    </dependency>
    <!-- Administrator -->
    <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
        <security>
            <requestedPrivileges>
                <requestedExecutionLevel level="requireAdministrator"></requestedExecutionLevel>
            </requestedPrivileges>
        </security>
    </trustInfo>
    <!-- DPI Aware -->
    <asmv3:application xmlns:asmv3="urn:schemas-microsoft-com:asm.v3">
        <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
            <dpiAware>true</dpiAware>
        </asmv3:windowsSettings>
    </asmv3:application>
    <!-- OSVersion -->
    <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
        <application>
            <!-- Windows 10 -->
            <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}"/>
            <!-- Windows 8.1/Windows Blue/Server 2012 R2 -->
            <supportedOS Id="{1f676c76-80e1-4239-95bb-83d0f6d0da78}"/>
            <!-- Windows Vista/Server 2008 -->
            <supportedOS Id="{e2011457-1546-43c5-a5fe-008deee3d3f0}"/>
            <!-- Windows 7/Server 2008 R2 -->
            <supportedOS Id="{35138b9a-5d96-4fbd-8e2d-a2440225f93a}"/>
            <!-- Windows 8/Server 2012 -->
            <supportedOS Id="{4a2f28e3-53b9-4441-ba9c-d69d4a4a6e38}"/>
        </application>
    </compatibility>
</assembly>
```

# 测试

在开发的时候，一般都需要及时观察到适配的效果，这里简单的做法就是强制设定缩放因子为我们的需要测试的值就可以了。但是对于最终的显示效果还需要通过更改系统设置的缩放比例后观察的效果为准。

# 总结

对于 Windows 平台而言，未来高清设备一定会越来越多，所以适配 DPI 变得越来越重要。
通过上面的步骤，能够满足大多数应用对于 DPI 适配的需求，但对于需要做到 Per-Monitor 级别的适配的应用，就还需要更多的工作以完成适配。

# 参考文档

`https://msdn.microsoft.com/en-us/library/dn469266(v=vs.85).aspx#pixelated_text`

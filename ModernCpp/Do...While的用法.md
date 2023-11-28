## 使用`do...while`替换`goto`语句
```cpp
//最佳方案：do {if() break; if() break;} while(0)
do {
    // 步骤1
    ...
    if (步骤1失败) {
        break;
    }
    // 步骤2
    ...
    if (步骤2失败) {
        break;
    }
    // 步骤3
    ...
    if (步骤3失败) {
        break;
    }
} while(0);

// 步骤4
...
// 步骤5
...
//这个其实也适用于其他用do while的语言，不止C++
```


函数可直接返回STL容器或对象。不要返回指针，也不需要给函数加出参，相信我，没问题。

```c++
std::vector<std::string> split(std::string str, std:string del) {
    std::vector<std::string> str_list;
    // ...
    return str_list;
}
```


其实`C++11`之前也有这么用的。因为编译器自己做的`RVO`，`NRVO`优化，这当然是非标的。改一下编译选项可能就没啦。`gcc`不显式关闭RVO的话，默认就开启的。但我在`C++98`的环境下工作时，还是很少见到这种直接返回对象的写法。其实不是所有返回对象函数定义都能触发RVO，如果不清楚，`C++98`还是谨慎使用。但是`C++11`开始，你不用担心了。
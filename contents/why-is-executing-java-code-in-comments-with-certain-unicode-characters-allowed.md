##为什么Java中允许执行含有某些Unicode字符的注释？
下面的代码将产生一个输出“Hello World”（尝试一下）
```
public static void main(String... args) {

   // The comment below is not a typo.
   // \u000d System.out.println("Hello World!");
}
```
产生这种情况的原因是：Java编译器将Unicode字符\u000d解析为一个换行，于是，上述程序就变为如下：
```
public static void main(String... args) {

   // The comment below is not a typo.
   //
   System.out.println("Hello World!");
}
```
因此，这导致注释被“执行”。
自此，这种特性可被用作“隐藏”恶意的代码或一个坏心眼的程序员能想到的任何东西。
为什么在注释中会允许这种特性？
为什么Java规范对其视而不见？

##最佳答案
Unicode解码在其他语句翻译前发生。这样做核心的好处在于：ASCII码和其他编码方式间的抉择变得不重要了。你甚至不需要指出注释的起始和结束。  
正如JLS Sectin3.3指出：这允许基于ASCII码的工具能处理源文件：
```
[...] The Java programming language specifies a standard way of transforming a program  
written in Unicode into ASCII that changes a program into a form that can be processed by  
ASCII-based tools. [...]
```
这给予了平台独立性（支持字符集的独立性）基本的保证，而这种独立性恰好是Java平台一个核心的目标。  

当用非拉丁语言编制代码时，拥有在文件中随处编写Unicode字符的能力是一项优雅的特性，且在注释中尤为重要。事实上，这种微妙的干涉语义  
的方式只是一种（不幸的）副作用。  

在这个问题上有很多的陷阱且Java Puzzlers总结了如下变体：
```
    Is this a legal Java program? If so, what does it print?

    \u0070\u0075\u0062\u006c\u0069\u0063\u0020\u0020\u0020\u0020
    \u0063\u006c\u0061\u0073\u0073\u0020\u0055\u0067\u006c\u0079
    \u007b\u0070\u0075\u0062\u006c\u0069\u0063\u0020\u0020\u0020
    \u0020\u0020\u0020\u0020\u0073\u0074\u0061\u0074\u0069\u0063
    \u0076\u006f\u0069\u0064\u0020\u006d\u0061\u0069\u006e\u0028
    \u0053\u0074\u0072\u0069\u006e\u0067\u005b\u005d\u0020\u0020
    \u0020\u0020\u0020\u0020\u0061\u0072\u0067\u0073\u0029\u007b
    \u0053\u0079\u0073\u0074\u0065\u006d\u002e\u006f\u0075\u0074
    \u002e\u0070\u0072\u0069\u006e\u0074\u006c\u006e\u0028\u0020
    \u0022\u0048\u0065\u006c\u006c\u006f\u0020\u0077\u0022\u002b
    \u0022\u006f\u0072\u006c\u0064\u0022\u0029\u003b\u007d\u007d
```
这个程序结果是一个简单的“Hello World！”  
在这个难题的解决方案中，他们指出如下观点：  
```
More seriously, this puzzle serves to reinforce the lessons of the previous three: Unicode  
escapes are essential when you need to insert characters that can’t be represented in any other way into your program.  
Avoid them in all other cases.
```

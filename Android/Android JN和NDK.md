# 环境搭建
- 在 SDK Manager中勾选NDK和CMake。
![[Pasted image 20220105200217.png]]
- 在CMakeLists中找到刚刚勾选的NDK版本名，并在想调用Native方法的类中添加动态库
![[Pasted image 20220105201211.png]]
- 创建工程
![[Pasted image 20220105201349.png]]
- 项目创建完成后，运行看到 hello world就成功了
# 概念
### JNI
- JNI，全名 Java Native Interface，是Java本地接口，JNI是Java调用Native 语言的一种特性，通过JNI可以使得Java与C/C++机型交互。简单点说就是JNI是Java中调用C/C++的统称。
### NDK
- NDK 全名Native Develop Kit，官方说法：Android NDK 是一套允许您使用 C 和 C++ 等语言，以原生代码实现部分应用的工具集。在开发某些类型的应用时，这有助于您重复使用以这些语言编写的代码库。

- **JNI可以在Java和Android中同时使用，NDK只能在Android里面使用。**

# 相关函数

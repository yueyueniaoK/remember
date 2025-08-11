## CMake基础

### CMakeLists.txt 文件

CMakeLists.txt 是 CMake 的配置文件，**用于定义项目的构建规则、依赖关系、编译选项等**。每个 CMake 项目通常都有一个或多个 CMakeLists.txt 文件。

### 文件结构和基本语法

1.指定CMake的最低版本要求

![image-20250730093249380](D:\kaper\image\image-20250730093249380.png)

2.定义项目的名称和使用的编程语言

![image-20250730093331064](D:\kaper\image\image-20250730093331064.png)

3.指定要生成的可执行文件和其源文件：

![image-20250730093346054](D:\kaper\image\image-20250730093346054.png)

4、创建一个库（静态库或动态库）及其源文件：

![image-20250730093435423](D:\kaper\image\image-20250730093435423.png)

5、链接目标文件与其他库：

![image-20250730093504157](D:\kaper\image\image-20250730093504157.png)

6、添加头文件搜索路径：

![image-20250730093518520](D:\kaper\image\image-20250730093518520.png)

7、设置变量的值：

![image-20250730093602200](D:\kaper\image\image-20250730093602200.png)

8、设置目标属性：

![image-20250730093620855](D:\kaper\image\image-20250730093620855.png)

9、安装规则：

![image-20250730093813545](D:\kaper\image\image-20250730093813545.png)

10、条件语句 (if, elseif, else, endif 命令)

![image-20250730093841531](D:\kaper\image\image-20250730093841531.png)

11、自定义命令 (add_custom_command 命令)：

![image-20250730093910411](D:\kaper\image\image-20250730093910411.png)

### 变量和缓存

CMake 使用变量来存储和传递信息，这些变量可以在 CMakeLists.txt 文件中定义和使用。变量可以分为普通变量和缓存变量。

#### 变量定义与使用

![image-20250730094104660](D:\kaper\image\image-20250730094104660.png)

#### 缓存变量

缓存变量存储在 CMake 的缓存文件中，用户可以在 CMake 配置时修改这些值。**缓存变量通常用于用户输入的设置，例如编译选项和路径**。

![image-20250730094200900](D:\kaper\image\image-20250730094200900.png)

### 查找库和包

CMake 可以通过 **find_package()** 指令自动检测和配置外部库和包。常用于查找系统安装的库或第三方库。

![image-20250730094311878](D:\kaper\image\image-20250730094311878.png)
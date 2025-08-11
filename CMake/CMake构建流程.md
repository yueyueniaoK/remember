### CMake构建实例

#### CMake构建步骤：

1. **创建 `CMakeLists.txt` 文件**：定义项目、目标和依赖。
2. **创建构建目录**：保持源代码目录整洁。
3. **配置项目**：使用 CMake 生成构建系统文件。
4. **编译项目**：使用构建系统文件编译项目。
5. **运行可执行文件**：执行生成的程序。
6. **清理构建文件**：删除中间文件和目标文件。

#### 项目实例：

```cmake
MyProject/
├── CMakeLists.txt
├── src/
│   ├── main.cpp
│   └── mylib.cpp
└── include/
    └── mylib.h
```

##### 创建CMakeLists.txt文件

在 MyProject 目录下创建 CMakeLists.txt 文件。

```cmake
cmake_minimum_required(VERSION 3.10)   # 指定最低 CMake 版本

project(MyProject VERSION 1.0)          # 定义项目名称和版本

# 设置 C++ 标准为 C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 添加头文件搜索路径
include_directories(${PROJECT_SOURCE_DIR}/include)

# 添加源文件
add_library(MyLib src/mylib.cpp)        # 创建一个库目标 MyLib
add_executable(MyExecutable src/main.cpp)  # 创建一个可执行文件目标 MyExecutable

# 链接库到可执行文件
target_link_libraries(MyExecutable MyLib)
```

##### 创建构建目录

为了保持源代码目录的整洁，我们将在**项目根目录下创建一个单独的构建目录。

![image-20250730092051078](D:\kaper\笔记\图片\image-20250730092051078.png)

##### 配置项目

在构建目录中使用 CMake 配置项目。这将生成适合平台的构建系统文件（如 Makefile）。

![image-20250730092151382](D:\kaper\笔记\图片\image-20250730092151382.png)

**`cmake ..`**：`..` 指向源代码目录，即包含 `CMakeLists.txt` 文件的目录。CMake 将读取 `CMakeLists.txt` 文件并生成构建系统文件。

##### 编译项目

使用生成的构建系统文件编译项目。根据生成的构建系统文件类型，使用相应的构建命令。

![image-20250730092524264](D:\kaper\笔记\图片\image-20250730092524264.png)

##### 运行可执行文件

![image-20250730092544049](D:\kaper\笔记\图片\image-20250730092544049.png)

##### 清理构建文件

![image-20250730092609521](D:\kaper\笔记\图片\image-20250730092609521.png)
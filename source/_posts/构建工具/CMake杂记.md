---
title: CMake杂记 - 一些零零散散的使用技巧
date: 2023‎-05‎-‎16‎ ‏‎13:54:56
id: 86
tags: 构建工具
categories: 构建工具
copyright: true
---

# CMake杂记

## 1. 在编译过程中打印编印信息

- 执行命令cmake时追加：`-DCMAKE_VERBOSE_MAKEFILE=ON`
- 在CMakeLists.txt中添加：`set(CMAKE_VERBOSE_MAKEFILEON ON)`



## 2. 打印自定义变量及其值

```cmake
get_cmake_property(_variableNames VARIABLES)
foreach (_variableName ${_variableNames})
    message(STATUS "${_variableName}=${${_variableName}}")
endforeach()
```



## 3. 打印环境变量及其值

```cmake
execute_process(COMMAND "${CMAKE_COMMAND}" "-E" "environment")
```



## 4. 多系统区分

```xml
if(WIN32)
    # todo: windows action
elseif(APPLE)
    # todo: mac action
elseif(UNIX)
    # todo: linux action
endif()
```



## 5. windows下x64和x86区分

```xml
if(WINDOWS)
    if(CMAKE_CL_64)
        target_link_libraries (BarcodeReader "DBRx64")
    else()
        target_link_libraries (BarcodeReader "DBRx86")
    endif()
else()
    target_link_libraries (BarcodeReader "DynamsoftBarcodeReader")
endif()
```



## 6. 将pthread作为默认线程库

```xml
# add pthread library
find_package (Threads)
target_link_libraries(smt-logger ${CMAKE_THREAD_LIBS_INIT})
```

> 由于pthread不是linux的默认线程库，需要手动制定。



## 7. 设置构建类型

```xml
#set(CMAKE_BUILD_TYPE "Release")
#set(CMAKE_BUILD_TYPE "Debug")
#set(CMAKE_BUILD_TYPE "MinSizeRel")
#set(CMAKE_BUILD_TYPE "RelWithDebInfo")
```



## 8. 编译选项

```xml
 add_definitions(-Wall)
```

可以通过`add_definitions(xxx)`设置编译选项, 常见编译选项列表:

1. -w: 关闭编译时的警告，也就是编译后不显示任何warning，因为有时在编译之后编译器会显示一些例如数据转换之类的警告，这些警告是我们平时可以忽略的。
2. -Wall: 编译后显示所有警告。
3. -W: 类似-Wall，会显示警告，但是只显示编译器认为会出现错误的警告。



## 9. TARGET的库属性设置

- 仅仅有静态
  -  IMPORT_LOCATION=lib路径

- 有动态库且静态库作为链接：
  -  IMPORTED_IMPLIB=lib路径
  -  IMPORTED_LOCATION=dll路径

```cmake
// 区分操作系统cpu架构， include 得在project下面
project(smt-logger-test VERSION 0.1.0)
include(CMakeDetermineSystem)
```



## 10. 用于寻找依赖库

```cmake
include(CMakeFindDependencyMacro)
find_dependency(Threads)
```



## 11. 给Window的Debug版本加后缀

```cmake
if(MSVC)
	set(CMAKE_DEBUG_POSTFIX "d")
endif()
```



## 12. 判断系统版本

```cmake
project(app)
include(CMakeDetermineSystem)
```



## 13. 生成带版本的so文件

```cmake
SET_TARGET_PROPERTIES(taos PROPERTIES VERSION 1.6.0.0 SOVERSION 1)
```

 此时会生成如下文件：

```cmake
libtaos.so -> libtaos.so.1*
libtaos.so.1 -> libtaos.so.1.6.0.0*
libtaos.so.1.6.0.0
```



## 14. Copy Qt DLL

```cmake
COMMAND ${CMAKE_COMMAND} -E copy_if_different
    $<TARGET_FILE:Qt5::Core>
    $<TARGET_FILE:Qt5::Widgets>
    ... etc ...
```



## 15. 打印CMake表达式的值

```cmake
add_custom_command(TARGET Test POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E echo "Runtime Dlls: $<TARGET_RUNTIME_DLLS:Test>")

add_custom_command(TARGET Test POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E echo "Target File: $<TARGET_FILE:${PROJECT_NAME}>")
```

> 调试查看CMake的表达式值



## 16. 拷贝当前项目中别的模块生成的DLL库到example模块里

```cmake
target_link_libraries(${PROJECT_NAME} PRIVATE
    library1
    library2)

# Copy the output dll files  from library1 and library2 to current module's exe path.
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
    $<TARGET_RUNTIME_DLLS:${PROJECT_NAME}>
    $<TARGET_FILE_DIR:${PROJECT_NAME}>)
```

> 这个功能必须cmake版本高于3.21以上才支持



## 17. QT部署（windeployqt）

```cmake
if (WIN32)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
            COMMAND "$ENV{Qt5_DIR}/bin/windeployqt.exe" "$<TARGET_FILE:${PROJECT_NAME}>")
endif ()
```

需要定义`Qt5_DIR`环境变量



## 18. CMake使用MSVC时支持UTF-8

```cmake
if (WIN32)
    add_compile_options("$<$<C_COMPILER_ID:MSVC>:/utf-8>")
    add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/utf-8>")
endif ()
```



## 19. CMake 静态库与共享库生成

1. 新建XXX_global.h文件，定义XXX_EXPORT

   ```c++
   #ifndef XXX_GLOBAL_H
   #define XXX_GLOBAL_H
   
   #include <QtGlobal>
   
   // Define XXX_SHARED_LIB in you application if you want to link against the
   // sharted version of AppLog
   
   #if defined(_WIN32) && defined(XXX_SHARED_LIB)
   #  if defined(XXX_LIBRARY)
   #    define XXX_EXPORT Q_DECL_EXPORT
   #  else
   #    define XXX_EXPORT Q_DECL_IMPORT
   #  endif
   #else
   #   define XXX_EXPORT
   #endif
   
   #endif //XXX_GLOBAL_H
   ```

2. 使用XXX_EXPORT

   ```c++
   class XXX_EXPORT XXX
   {
   };
   ```

3. CMakeLists.txt，add_library的库类型由`BUILD_SHARED_LIBS`指定

   ```cmake
   // BUILD_SHARED_LIBS默认为OFF
   option(BUILD_SHARED_LIBS "Specifies the type of libraries (SHARED or STATIC) to build" OFF)
   
   add_library(${PROJECT_NAME} ${XXX_HEADERS} ${XXX_SOURCES})
   
   if (BUILD_SHARED_LIBS)
       target_compile_definitions(${PROJECT_NAME} PUBLIC -DXXX_SHARED_LIB)
       target_compile_definitions(${PROJECT_NAME} PRIVATE -DXXX_LIBRARY)
   endif()
   ```

4. 如果使用了Conan，则在conanfile.py中加入

   ```python
   def generate(self):    
       tc = CMakeToolchain(self)
       // ......
       if self.options.shared == True:
           tc.variables["BUILD_SHARED_LIBS"] = "ON"
       else:
           tc.variables["BUILD_SHARED_LIBS"] = "OFF"
   
       // ......
       tc.generate()
       tc = CMakeDeps(self)
       tc.generate()
           
   def package_info(self):
       // ......
       if self.options.shared:
           self.cpp_info.defines = ["XXX_SHARED_LIB"]
   ```


## 20. CMake设置程序图标

1. 在CMakeLists.txt同级目录下新建文件“`XXX.rc`”，并放入准备好的ico图标文件；在rc文件中添加以下内容

```cpp
IDI_ICON1 ICON "product.ico"
```

2. 在cmake文件中添加

```cpp
add_executable(PROJECT_NAME
        main.cpp
        resource.rc
)
```

3. 重新编译

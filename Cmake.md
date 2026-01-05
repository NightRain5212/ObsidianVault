---
title: Cmake/Make 极简入门
description: 提供简单的格式化模板，初学即可用
categories:
  - study
tags:
  - study
  - cmake
---
## Makefile格式

```Makefile
#version 1

# 目标文件：依赖文件
	# 编译器 -o 目标 依赖
test: main.cpp complex.cpp 
	g++ -o test main.cpp complex.cpp
```

```Makefile
# version 2，使用变量表示
# 设置变量
CXX = g++
TARGET = test
OBJ = main.o complex.o
# 编译链接的过程规则
$(TARGET) : $(OBJ)
	$(CXX) -o $(TARGET) $(OBJ)

# 对于OBJ下的每个文件分别编译
main.o: main.cpp
	$(CXX) -c main.cpp
 	
complex.o: complex.cpp
	$(CXX) -c complex.cpp
```

```Makefile
# version 3
CXX = g++ # 编译器
TARGET = test # 目标
OBJ = main.o complex.o # 依赖

CXXFLAGS = -c -Wall # 编译选项，显示所有的warning

# $@表示Target(目标)，而@^表示OBJ (依赖)
$(TARGET): $(OBJ)
	$(CXX) -o $@ $^   

# 表示对所有的.o文件满足的统一规则，$<表示依赖的第一个文件
%.o: %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@

# clean命令，强制删除所有的.o文件，和TARGET文件
.PHONY: clean # 表示防止同名文件clean
clean:
	rm -f *.o $(TARGET)
```

```Makefile
# version 4
CXX = g++
TARGET = test
SRC = $(wildcard *.cpp) # 所有当前目录下的.cpp文件
OBJ = $(patsubst %.cpp,%.o,$(SRC)) # 路径替换，把SRC里的.cpp替换成.o

CXXFLAGS = -c -Wall

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^

%.o: %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@

.PHONY: clean
clean: 
	rm -f *.o $(TARGET)
```

## CMake格式

 - 文件名`CMakeLists.txt`,最简格式：
```CMake
# cmake的最低需求版本
cmake_minimum_required(VERSION 3.10)

# 项目名
project(test)

# 生成可执行文件 目标 依赖...
add_executable(test main.cpp complex.cpp)
```

---
layout: post
title: CMake built-in 변수 확인하기
author: Park Donghyeon
date: 2018-03-04 18:00:00 +0900
categories: [tool]
tags: [cmake, build]
toc: false
comments: true
---

CMake 내장 변수를 활용하면 소스코드의 파일 경로를 바꿀 때마다 파일 경로를 수정할 필요가 없다. 
 그래서 내장 변수를 써야한다. 무슨 내장 변수가 있는지 알아야하지 않겠는가?  
일단 무슨 변수가 있는지 전부 출력해보고 확인해보자.


```cmake
# CMakeLists.txt
foreach (_variableName VARIABLES)
    message(STATUS "${_variableName}=${ ${_variableName} }")
endforeach()
```

CMakeLists.txt 파일을 만들고 `cmake .` 명령을 실행하면 CMake의 변수들이 전부 출력된다.



```cmake
CMAKE_HOME_DIRECTORY
CMAKE_SOURCE_DIR
CMAKE_BINARY_DIR
PROJECT_SOURCE_DIR
PROJECT_BINARY_DIR
```

얘네들은 값이 전부 똑같은데 무슨 차이가 있는지 모르겠다. 알게되면 업데이트 함.
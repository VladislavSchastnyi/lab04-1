## Laboratory work IV

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab04** на сервисе **GitHub**
- [ ] 2. Ознакомиться со ссылками учебного материала
- [ ] 3. Выполнить инструкцию учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab03.git projects/lab04
Cloning into 'projects/lab04'...
remote: Counting objects: 19, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 19 (delta 1), reused 13 (delta 0), pack-reused 0
Unpacking objects: 100% (19/19), done.
$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04.git
```

```ShellSession
$ g++ -I./include -std=c++11 -c sources/print.cpp
$ ls print.o
print.o
$ nm print.o | grep print
0000000000000087 t _GLOBAL__sub_I__Z5printRKSsRSo
0000000000000000 T _Z5printRKSsRSo
0000000000000025 T _Z5printRKSsRSt14basic_ofstreamIcSt11char_traitsIcEE
$ ar rvs print.a print.o
ar: creating print.a
a - print.o
$ file print.a
print.a: current ar archive
$ g++ -I./include -std=c++11 -c examples/example1.cpp
$ ls example1.o
example1.o
$ g++ example1.o print.a -o example1
$ ./example1 && echo
hello
```

```ShellSession
$ g++ -I./include -std=c++11 -c examples/example2.cpp
$ nm example2.o
000000000000015f t _GLOBAL__sub_I_main
                 U _Unwind_Resume
0000000000000122 t _Z41__static_initialization_and_destruction_0ii
                 U _Z5printRKSsRSt14basic_ofstreamIcSt11char_traitsIcEE
                 U _ZNSaIcEC1Ev
                 U _ZNSaIcED1Ev
                 U _ZNSsC1EPKcRKSaIcE
                 U _ZNSsD1Ev
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
                 U _ZNSt8ios_base4InitC1Ev
                 U _ZNSt8ios_base4InitD1Ev
0000000000000000 r _ZStL19piecewise_construct
0000000000000000 b _ZStL8__ioinit
0000000000000000 W _ZStorSt13_Ios_OpenmodeS_
0000000000000010 r _ZZL18__gthread_active_pvE20__gthread_active_ptr
                 U __cxa_atexit
                 U __dso_handle
                 U __gxx_personality_v0
                 w __pthread_key_create
                 U __stack_chk_fail
0000000000000000 T main
$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
hello
```

```ShellSession
$ rm -rf example1.o example2.o print.o 
$ rm -rf print.a 
$ rm -rf example1 example2
$ rm -rf log.txt
```

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 2.8)
project(print)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

```ShellSession
$ cmake -H. -B_build
-- The C compiler identification is Clang 3.6.0
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler: /usr/bin/clang
-- Check for working C compiler: /usr/bin/clang -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_build
$ cmake --build _build
Scanning dependencies of target print
[100%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
Linking CXX static library libprint.a
[100%] Built target print
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

```ShellSession
$ cmake --build _build
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_build
[ 33%] Built target print
Scanning dependencies of target example1
[ 66%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
Linking CXX executable example1
[ 66%] Built target example1
Scanning dependencies of target example2
[100%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
Linking CXX executable example2
[100%] Built target example2
$ cmake --build _build --target print
[100%] Built target print
$ cmake --build _build --target example1
[ 50%] Built target print
[100%] Built target example1
$ cmake --build _build --target example2
[ 50%] Built target print
[100%] Built target example2
```

```ShellSession
$ ls -la _build/libprint.a
-rw------- 1 ubuntu ubuntu 2746 Jun  7 22:09 _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

```ShellSession
$ git clone https://github.com/tp-labs/lab04 tmp
Cloning into 'tmp'...
remote: Counting objects: 37, done.
remote: Total 37 (delta 0), reused 0 (delta 0), pack-reused 37
Unpacking objects: 100% (37/37), done.
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```ShellSession
$ cat CMakeLists.txt
cmake_minimum_required(VERSION 2.8)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_build
$ cmake --build _build --target install
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_install/lib/libprint.a
-- Installing: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_install/include
-- Installing: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_install/include/print.hpp
-- Installing: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_install/cmake/print-config.cmake
-- Installing: /home/ubuntu/workspace/VladislavSchastnyi/workspace/projects/lab04/_install/cmake/print-config-noconfig.cmake
$ tree _install
_install
├── cmake
│   ├── print-config-noconfig.cmake
│   └── print-config.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a

3 directories, 4 files
```

```ShellSession
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
[master 571270a] added CMakeLists.txt
 1 file changed, 36 insertions(+)
 create mode 100644 CMakeLists.txt
$ git push origin master
Counting objects: 25, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (19/19), done.
Writing objects: 100% (25/25), 3.94 KiB | 0 bytes/s, done.
Total 25 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), done.
To https://github.com/VladislavSchastnyi/lab04.git
 * [new branch]      master -> master
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2017 Братья Вершинины
```

# /usr/bin/ld: cannot find -lstdc++: No such file or directory

## An Error

```bash
cmake -S . -B build

-- The CXX compiler identification is Clang 14.0.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - failed
-- Check for working CXX compiler: /usr/bin/clang++
-- Check for working CXX compiler: /usr/bin/clang++ - broken
CMake Error at /usr/share/cmake-3.22/Modules/CMakeTestCXXCompiler.cmake:62 (message):
  The C++ compiler

    "/usr/bin/clang++"

  is not able to compile a simple test program.

  It fails with the following output:

    Change Dir: ./myproject/build/CMakeFiles/CMakeTmp

    Run Build Command(s):/usr/bin/gmake -f Makefile cmTC_bce1d/fast && /usr/bin/gmake  -f CMakeFiles/cmTC_bce1d.dir/build.make CMakeFiles/cmTC_bce1d.dir/build
    gmake[1]: Entering directory './myproject/build/CMakeFiles/CMakeTmp'
    Building CXX object CMakeFiles/cmTC_bce1d.dir/testCXXCompiler.cxx.o
    /usr/bin/clang++    -MD -MT CMakeFiles/cmTC_bce1d.dir/testCXXCompiler.cxx.o -MF CMakeFiles/cmTC_bce1d.dir/testCXXCompiler.cxx.o.d -o CMakeFiles/cmTC_bce1d.dir/testCXXCompiler.cxx.o -c ./myproject/build/CMakeFiles/CMakeTmp/testCXXCompiler.cxx
    Linking CXX executable cmTC_bce1d
    /usr/bin/cmake -E cmake_link_script CMakeFiles/cmTC_bce1d.dir/link.txt --verbose=1
    /usr/bin/clang++ CMakeFiles/cmTC_bce1d.dir/testCXXCompiler.cxx.o -o cmTC_bce1d
    /usr/bin/ld: cannot find -lstdc++: No such file or directory
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    gmake[1]: *** [CMakeFiles/cmTC_bce1d.dir/build.make:100: cmTC_bce1d] Error 1
    gmake[1]: Leaving directory './myproject/build/CMakeFiles/CMakeTmp'
    gmake: *** [Makefile:127: cmTC_bce1d/fast] Error 2
```

## Investigation

### Compile with verbose option

```bash
clang++ -v main.cpp -o main
```

```log
Ubuntu clang version 18.1.8 (++20240731024944+3b5b5c1ec4a3-1~exp1~20240731145000.144)
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/11
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
Candidate multilib: .;@m64
Selected multilib: .;@m64
 "/usr/lib/llvm-18/bin/clang" -cc1 -triple x86_64-pc-linux-gnu -emit-obj -mrelax-all -dumpdir main- -disable-free -clear-ast-before-backend -disable-llvm-verifier -discard-value-names -main-file-name main.cpp -mrelocation-model pic -pic-level 2 -pic-is-pie -mframe-pointer=all -fmath-errno -ffp-contract=on -fno-rounding-math -mconstructor-aliases -funwind-tables=2 -target-cpu x86-64 -tune-cpu generic -debugger-tuning=gdb -fdebug-compilation-dir=myproject -v -fcoverage-compilation-dir=myproject -resource-dir /usr/lib/llvm-18/lib/clang/18 -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++ -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/x86_64-linux-gnu -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/backward -internal-isystem /usr/lib/llvm-18/lib/clang/18/include -internal-isystem /usr/local/include -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../x86_64-linux-gnu/include -internal-externc-isystem /usr/include/x86_64-linux-gnu -internal-externc-isystem /include -internal-externc-isystem /usr/include -fdeprecated-macro -ferror-limit 19 -fgnuc-version=4.2.1 -fskip-odr-check-in-gmf -fcxx-exceptions -fexceptions -fcolor-diagnostics -dwarf-debug-flags "/usr/lib/llvm-18/bin/clang --driver-mode=g++ -v main.cpp -o main -dumpdir main-" -faddrsig -D__GCC_HAVE_DWARF2_CFI_ASM=1 -o /tmp/main-66c883.o -x c++ main.cpp
clang -cc1 version 18.1.8 based upon LLVM 18.1.8 default target x86_64-pc-linux-gnu
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/backward"
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../x86_64-linux-gnu/include"
ignoring nonexistent directory "/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++
 /usr/lib/llvm-18/lib/clang/18/include
 /usr/local/include
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
main.cpp:1:10: fatal error: 'iostream' file not found
    1 | #include <iostream>
      |          ^~~~~~~~~~
1 error generated.
```

### A trap

```log
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/11
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
```

check the path `/usr/bin/../lib/gcc/x86_64-linux-gnu/`

```bash
$ ls -al /usr/bin/../lib/gcc/x86_64-linux-gnu/11 | grep libstd
-rw-r--r-- 1 root root  6018836  5월 13  2023 libstdc++.a
-rw-r--r-- 1 root root   683830  5월 13  2023 libstdc++fs.a
lrwxrwxrwx 1 root root       40  5월 13  2023 libstdc++.so -> ../../../x86_64-linux-gnu/libstdc++.so.6
```

looks good in `11` directory

```bash
$ ls -al /usr/bin/../lib/gcc/x86_64-linux-gnu/12 | grep libstd
```

nothing

## Solution

```bash
sudo apt install libstdc++-12-dev
```

```bash
ls -al /usr/bin/../lib/gcc/x86_64-linux-gnu/12 | grep libstd

-rw-r--r-- 1 root root  6118606  5월 13  2023 libstdc++.a
-rw-r--r-- 1 root root   719116  5월 13  2023 libstdc++fs.a
lrwxrwxrwx 1 root root       40  5월 13  2023 libstdc++.so -> ../../../x86_64-linux-gnu/libstdc++.so.6
```

### Try again

```bash
clang++ -v main.cpp -o main

Ubuntu clang version 18.1.8 (++20240731024944+3b5b5c1ec4a3-1~exp1~20240731145000.144)
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/11
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
Candidate multilib: .;@m64
Selected multilib: .;@m64
 "/usr/lib/llvm-18/bin/clang" -cc1 -triple x86_64-pc-linux-gnu -emit-obj -mrelax-all -dumpdir main- -disable-free -clear-ast-before-backend -disable-llvm-verifier -discard-value-names -main-file-name main.cpp -mrelocation-model pic -pic-level 2 -pic-is-pie -mframe-pointer=all -fmath-errno -ffp-contract=on -fno-rounding-math -mconstructor-aliases -funwind-tables=2 -target-cpu x86-64 -tune-cpu generic -debugger-tuning=gdb -fdebug-compilation-dir=myproject -v -fcoverage-compilation-dir=myproject -resource-dir /usr/lib/llvm-18/lib/clang/18 -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12 -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/x86_64-linux-gnu/c++/12 -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/backward -internal-isystem /usr/lib/llvm-18/lib/clang/18/include -internal-isystem /usr/local/include -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../x86_64-linux-gnu/include -internal-externc-isystem /usr/include/x86_64-linux-gnu -internal-externc-isystem /include -internal-externc-isystem /usr/include -fdeprecated-macro -ferror-limit 19 -fgnuc-version=4.2.1 -fskip-odr-check-in-gmf -fcxx-exceptions -fexceptions -fcolor-diagnostics -dwarf-debug-flags "/usr/lib/llvm-18/bin/clang --driver-mode=g++ -v main.cpp -o main -dumpdir main-" -faddrsig -D__GCC_HAVE_DWARF2_CFI_ASM=1 -o /tmp/main-505553.o -x c++ main.cpp
clang -cc1 version 18.1.8 based upon LLVM 18.1.8 default target x86_64-pc-linux-gnu
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../x86_64-linux-gnu/include"
ignoring nonexistent directory "/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12
 /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/x86_64-linux-gnu/c++/12
 /usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../include/c++/12/backward
 /usr/lib/llvm-18/lib/clang/18/include
 /usr/local/include
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
 "/usr/bin/ld" -z relro --hash-style=gnu --build-id --eh-frame-hdr -m elf_x86_64 -pie -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o main /lib/x86_64-linux-gnu/Scrt1.o /lib/x86_64-linux-gnu/crti.o /usr/bin/../lib/gcc/x86_64-linux-gnu/12/crtbeginS.o -L/usr/bin/../lib/gcc/x86_64-linux-gnu/12 -L/usr/bin/../lib/gcc/x86_64-linux-gnu/12/../../../../lib64 -L/lib/x86_64-linux-gnu -L/lib/../lib64 -L/usr/lib/x86_64-linux-gnu -L/usr/lib/../lib64 -L/lib -L/usr/lib /tmp/main-505553.o -lstdc++ -lm -lgcc_s -lgcc -lc -lgcc_s -lgcc /usr/bin/../lib/gcc/x86_64-linux-gnu/12/crtendS.o /lib/x86_64-linux-gnu/crtn.o
```


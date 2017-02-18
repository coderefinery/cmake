---
layout: episode
title: "Hello-world example"
teaching: 30
exercises: 0
questions:
  - "How do make work?"
  - "What is implicit rules, suffixes, different types of variables?"
  - "What is an out of source compilation?"
objectives:
  - "Learn how to configure and build simple Make files."
keypoints:
  - "We start by compiling in source directory" 
  - "The object files can be built in a separate subdirectory."
  - "Make dependencies can be regenerated"
  - "A simple but usable Makefile which can be used for smaller projects"
---

## Hello-world example

We start with a C++ program:

```cpp
#include <iostream>

int main()
{
    std::cout << "Hello World!" << std::endl;
}
```

Create a fresh directory and save the C++ code to a file called
`hello.cpp`.

We wish to compile this code to `hello`.

We start out with a very simple Makefile contains:

```make
hello.x: hello.cpp
	g++ hello.cpp -o hello
```
The contains of your newly created subdirectory should look like this:
```shell
$ ls
Makefile	hello.cpp
```

The Makefile states that the target "hello.x" depends on the file "hello.cpp".
The statement 'g++ hello.cpp -o hello' is the (shell)-command that produce (we
often say 'build') the target from the dependents(or prerequisites).

A Makefile contains a set of rules on the form:

```make
#rule 1
target1: prereq1 prereq2
	 commands

target2: prereq21 prereq22
	 commands

target3: prereq31 target1
	 commands
	 commands

# Important: 'commands' is preceded by a <tab>, not white spaces.

```
The first rule is the default rule and the one executed when just write 'make'.

Let us develop our simple example further. We should be able to remove the
targets without to much fuzz. 'rm hello' seems efficient enough in the example,
but as our code base grow things will be more complicated. 

The establish practice is to use the target 'clean' for removing targets. Here:
```make
hello.x: hello.cpp
	g++ hello.cpp -o hello

clean:
	rm hello.x
```

'clean' is a phony target (phony = not real or true, trying to trick people
:-). We see that 'clean' have no prerequisites. Consequently it will always be
executed, unless.... try this:
```shell
make 
touch clean
make clean
make
```
 If the targets exists, like here where made an empty file named 'clean', the
command will be never run because it is always up to date. To remedy this, we
state the target 'clean' as phony:
```make
hello.x: hello.cpp
	g++ hello.cpp -o hello

.PHONY: clean           # make is case-sensitive, .phony will not do the trick
clean:
	rm hello.x
```

When we do 'make clean', our target 'hello.x', is removed despite that there is
a file named 'clean' in the directory. By stating targets as phony, we can get
shell commands executed, either as mean in it self, or as part of a
prerequisite to other targets.

---

## Implicit rules
There is a range of implicit rules in make. In fact our simple Makefile is over-specified, we can make use of make's implicit rule about C++ files:
```make
hello: hello.cpp  #note that we changed the name of the target from hello.x to hello

.PHONY: clean
clean:
	rm hello
```

The magic going on here is shown by 'make -p' or 'make --print-data-base'. If
you redirect this to a file 'make -p > make-dbase.txt', you can take a look at
the implicit rules governing the build process. Open the file 'make-dbase.txt'
in a editor and search for %.cpp

You should find something like:
```make
%.cpp:

%: %.cpp
#  commands to execute (built-in):
	$(LINK.cpp) $^ $(LOADLIBES) $(LDLIBS) -o $@
```

This implicit rule show how a target which is the stem of a .cpp file, our case
the target 'hello', will be built. The build process expand the three variables
$(LINK).cpp, $(LOADLIBES) $(LDLIBS) as well as the two automatic variables $^,
$@. For now, let us look at $(LINK.cpp), by searching for LINK.cc  ( Searching
for LINK.cpp, will show that it is equal LINK.cc).

```make

LINK.cc = $(CXX) $(CXXFLAGS) $(CPPFLAGS) $(LDFLAGS) $(TARGET_ARCH)


```

These variables should be recognizable. These are often inputs to make as part
of a build process. You can see what their defaults are by searching in the
'make -p' output (CXX is set to c++, CXXFLAGS is unset)

Let us say we want to keep our naming of "whatever".x as the target executable.
We can copy the implicit rule to making new rule for this naming convention. In
addition we use a variable for our target:

```make
TARGET = hello.x

%.x: %.cpp
	$(LINK.cpp) $^ $(LOADLIBES) $(LDLIBS) -o $@

$(TARGET): hello.cpp

.PHONY: clean
clean:
	rm $(TARGET)

```
What are these automatic variables? There is six automatic variable which make expand in the following way:
+ $@ - expands to the filename representing target, our case hello.x
+ $^ - the filenames of all prerequisites separated by white spaces, our case only hello.cpp
+ $< - the filename of the  first prerequisite
+ $* - The stem of the target filename ( a filename without is suffix)
+ $? - The names of all prerequisites that are newer than the target, separated by spaces
+ $% - The finale element of an archive member specification
+ $+ - Similar to $^, except that $+ includes duplicates

We are building our executable in the source directory, in the directory where
hello.cpp resides. It is better to separate the source files from the
executables and the object files, and to a out of source compilation . How do
we accomplish that?

```make
TARGET = hello.x
BUILD_DIR = ./build

%.x: %.o
	$(LINK.cpp) $^ $(LOADLIBES) $(LDLIBS) -o $@

$(BUILD_DIR)/$(TARGET): $(BUILD_DIR)/hello.o


$(BUILD_DIR)/%.o : %.cpp
	mkdir -p $(@D)
	$(COMPILE.cc) $(OUTPUT_OPTION) $<

.PHONY: clean
clean:
	rm -rf $(BUILD_DIR)
```
 To achieve a separation of executables/object files from source file, we
introduce a two-phase compilation. First we produce the object files, or object
file in our case, and then we link to an executable. The target
'./build/hello.x' depends on './build/hello.o'. We change the prerequisite for
'%.x' from '%.cpp' to '%.o'. We add a commands for how to produce
'./build/*.o'. The build command is copied from the 'make -p' output and is
equal to the build command for '%.o : %.cpp'.

Was this really an improvement? For build a target only depending on one source
file, probably not. If our target depends on several source files, this is a
step in the right direction, but we can do further improvements. 

```make
TARGET = hello.x

SRC = src
BUILD_DIR = build

SRCS= $(wildcard *.cpp)
OBJ = $(SRCS:%.cpp=$(BUILD_DIR)/%.o)
DEPS = $(OBJ:%.o=%.d)

$(TARGET) : $(BUILD_DIR)/$(TARGET)

$(BUILD_DIR)/$(TARGET): $(OBJ)
	$(CXX) $(CXXFLAGS) $^ -o $@ $(LDFLAGS) $(LDLIBS)

$(BUILD_DIR)/%.o : %.cpp
	mkdir -p $(@D)
	$(CXX) $(CXXFLAGS) -MMD -c $< -o $@ $(INC)

.PHONY: clean
clean:
	rm -rf $(BUILD_DIR)



```

Now is the build statements general, and we can keep all changes in the upper part of the Makefile
```make
# -- variable part in the start
CXX = clang++

GTEST_HOME = $(HOME)/local/googletest
INC = -I $(GTEST_HOME) -I $(GTEST_HOME)/include -I . -I ../src -I$(HOME)/src/c++/include
LDLIBS = $(GTEST_HOME)/lib/libgtest.a
LDFLAGS = 
CXXFLAGS = -std=c++11 -pthread

TARGET = vnorm_p_test


# ---- static part below here ---
BUILD_DIR = ./build
SRCS = $(wildcard *.cpp)
OBJ = $(SRCS:%.cpp=$(BUILD_DIR)/%.o)
DEPS = $(OBJ:%.o=%.d)

$(TARGET) : $(BUILD_DIR)/$(TARGET)

$(BUILD_DIR)/$(TARGET) : $(OBJ)
	mkdir -p $(@D)
	$(CXX) $(CXXFLAGS) $^ -o $@ $(LDFLAGS) $(LDLIBS)

$(BUILD_DIR)/%.o : %.cpp
	mkdir -p $(@D)
	$(CXX) $(CXXFLAGS) -c $< -o $@ $(INC)
```


Here is an example from the 'trenches':
```make
CXX = `libmesh-config --cxx`

CXXFLAGS = `libmesh-config --cxxflags` -fpermissive `python2.7-config --cflags`

LIBS = -L/sw/sdev/Modules/intelcomp/psxe_2015/composer_xe_2015.1.133/compiler/lib/intel64/ -L/sw/sdev/Modules/gcc/gcc-4.8.2/lib64 -larmadillo `python2.7-config --ldflags`  `libmesh-config --libs`
#LIBS = -L/sw/sdev/Modules/gcc/gcc-6.2.0/lib64 -larmadillo `python2.7-config --ldflags`  `libmesh-config --libs`

#INC = `libmesh-config --include` -I/home/ntnu/gordona/installdir/armadillo/usr/include -I/sw/sdev/Modules/python/python-2.7.12/include/python2.7 -I/sw/sdev/Modules/python/python-2.7.12/lib/python2.7/site-packages/numpy-1.11.1-py2.7-linux-x86_64.egg/numpy/core/include/numpy/
INC = `libmesh-config --include` -I/home/ntnu/gordona/installdir/armadillo/usr/include -I/sw/sdev/Modules/python/python-2.7.9/include/python2.7 -I/sw/sdev/Modules/python/python-2.7.9/lib/python2.7/site-packages/numpy/core/include/numpy/

SRCS = main.cpp nonlinearsolver.cpp jacobian2.cpp MVConverter.cpp BCS.cpp spincurrent.cpp BChandler.cpp externalflux.cpp residual.cpp compute_RJ.cpp wrap.cpp Connection.cpp compute_output.cpp Exchange.cpp
OBJS = $(SRCS:.cpp=.o)
NAME = Executables/Usadel_0512

all: $(NAME)

$(NAME): $(OBJS)
		$(CXX) -o $@ $(CXXFLAGS) $(LIBS) $(INC) $(OBJS)

.cpp.o: $(SRCS)
		$(CXX) -c $< $(CXXFLAGS) $(LIBS) $(INC)

build: $(SRCS)
		$(CXX) -o $(NAME) $(FILES) $(CXXFLAGS) $(LIBS) $(INC)

```
## Task
+ Discuss the problems with this Makefile. Identify the two most severe problems here.

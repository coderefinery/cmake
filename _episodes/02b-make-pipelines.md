---
layout: episode
title: "Data processing pipeline using Make"
teaching: 0
exercises: 0
questions:
  - How does the make command work?
  - What are Makefiles composed of?
  - Why are Makefiles useful for Python and R developers?
#objectives:
#  - Learn how to configure and build simple Makefiles.
#  - Develop a simple Makefile, which can be used in small projects.
#  - "See what creates the 'magic' in Make."
#keypoints:
#  - We start by compiling in source.
#  - The object files can be built in a separate subdirectory.
#  - Make dependencies can be regenerated.
#  - A simple but usable Makefile which can be used for smaller projects.
#  - Make expresses dependencies.
#  - Make structures the build process.
---

## Motivation

- Get a feel for Makefiles.
- Demonstrate that Makefiles are not only for compiling C, C++, and Fortran code.
- Motivate Makefiles for data processing.
- "Parallelize" data processing pipelines using Make.

---

## Data processing pipeline example

We clone an [example repository](https://github.com/bast/make-pipeline).

This example contains two scripts that should in a very simplified way
represent a real data processing pipeline.

The script `count.py` counts the 10 most frequent characters in a text
read from standard input:

```shell
$ cat data/lorem.in | ./count.py
```

This produces:

```
i 42
e 38
t 32
o 29
u 29
a 29
n 24
l 22
r 22
d 19
```

The output of this script can be piped into a second script called
`plot.py` which "plots" the result in a bar "chart":

```shell
$ cat data/lorem.in | ./count.py | ./plot.py
```

Which produces:

```
i #####################
e ###################
t ################
o ##############
u ##############
a ##############
n ############
l ###########
r ###########
d #########
```

The scripts are extremely simplified versions of real life scripts.

---

## Goal

- Imagine you need to process tens or hundreds or thousands of such texts.
- Try to use Make to simplify and parallelize this task.

---

## Structure of Makefiles

```makefile
# rule
target: dependencies
	command(s)

# another rule
target: dependencies
	command(s)

# ...
```

**There are tabs in front of the commands, not spaces!**

---

## Makefile to process one data file

Create a file called `Makefile` with the following content (**mind the tab**):

```makefile
all: data/lorem.tmp data/lorem.out

data/lorem.tmp: data/lorem.in
        cat data/lorem.in | ./count.py > data/lorem.tmp

data/lorem.out: data/lorem.tmp
        cat data/lorem.tmp | ./plot.py > data/lorem.out
```

And test it:

```shell
$ make
```

- Try modifying the input text and hit `make`.
- Try `make` again without modifying the input text.

How does Make know that the outputs need to be rebuilt or not?

Try also "updating" the input file with `touch`:

```shell
$ touch data/*.in
$ make
```

---

## Makefile to process all data files

Now we will try a more sophisticated example:

```makefile
SRCS = $(wildcard data/*.in)
OBJS = $(patsubst %.in,%.out,$(SRCS))

all: $(OBJS)

# otherwise intermediate tmp files would be deleted
.PRECIOUS: %.tmp

%.tmp: %.in
        cat $< | ./count.py > $@

%.out: %.tmp
        cat $< | ./plot.py > $@
```

Discuss the changes and the motivations behind these changes.

---

## Parallel processing

Now we have a Makefile which can process thousands of files.
It will also discover which files need to be rebuilt if we modify inputs.

In this simple example the dependencies are not branched but in a real
life example we can imagine complex dependency trees.


### Let us slow things down

Now let us simulate the situation that the processing takes a lot of time.

For this edit `count.py` and `plot.py` and insert an artificial pause (maybe 2
or 3 seconds) by changing `num_seconds_sleep`.

Then time the entire processing:

```shell
$ touch data/*.in
$ time make

cat data/lorem.in | ./count.py > data/lorem.tmp
cat data/lorem.tmp | ./plot.py > data/lorem.out
cat data/shakespeare.in | ./count.py > data/shakespeare.tmp
cat data/shakespeare.tmp | ./plot.py > data/shakespeare.out
cat data/faust.in | ./count.py > data/faust.tmp
cat data/faust.tmp | ./plot.py > data/faust.out
make  0.22s user 0.04s system 2% cpu 12.289 total
```

### Let us speed things up

Now if you have 4 cores in your laptop (adapt accordingly), try:

```shell
$ touch data/*.in
$ time make -j4

cat data/lorem.in | ./count.py > data/lorem.tmp
cat data/lorem.tmp | ./plot.py > data/lorem.out
cat data/shakespeare.in | ./count.py > data/shakespeare.tmp
cat data/shakespeare.tmp | ./plot.py > data/shakespeare.out
cat data/faust.in | ./count.py > data/faust.tmp
cat data/faust.tmp | ./plot.py > data/faust.out
make -j4  0.27s user 0.03s system 7% cpu 4.131 total
```

Discuss what just happened.

---

## Resources

- [GNU Make Book](https://www.nostarch.com/gnumake)
- [Managing Projects with GNU Make, third edition](http://www.oreilly.com/openbook/make3/book/index.csp)

# [A Tutorial on Portable Makefiles](https://nullprogram.com/blog/2017/08/20/)

## What's in a Makefile

* At make's core, are one or more dependency trees, constructed from rules
  * Each vertex in the dependency tree, is called a target
  * The dependency trees' roots are the final products of the build (executable, document, etc)
* Makefiles specify the dependency trees and supply the shell commands to produce a target from its prerequisites
* The syntax for specifying an edge(s) in the dependency tree is `target [target name(s)...]: [prerequisite(s)...]
* In the example, `.c` files are the tree leaf nodes, which are the prerequisities for intermediate nodes, which are prerequisites for the root (`game`) node

## Adding commands to rules

* Each command starts with a tab character
* Each line is run in its own shell

## Invoking make and choosing targets

* `make` can build the `game` binary (default target)
* `make intermediate1 intermediate2` will build only the intermediate targets (and all of their prerequisites)

## Customize the build with macros

* Effectively variables with default values that can be overridden via command line arguments
  * `make CC=clang` where `CC` is defined in the `.SUFFIXES` section

## Inference rules so that you can stop repeating yourself

* A target with a certain extension, with a prerequisite with another certain extension is built a certain way

```make
# "Teaches" amke how to build an object file from a C source file
# Must be added to SUFFIXES section
.c.o:
    $(CC) $(CFLAGS) -c $<
```

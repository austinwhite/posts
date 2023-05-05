---
title: The Anatomy of a Compiler
tags:
  - tag
date: 05-02-2023
summary: How is human readable source code translated into machine executable code?
---

![language structure](../posts/images/The-Anatomy-of-a-Compiler/language-structure.svg)
  
## Scanning
The first job of the interpreter is scanning, also known as lexing (or 
lexical analysis). The **scanner** injests source code as a stream of 
raw characters and chunks them together to form tokens. A **token** is a 
group of characters (of size n >= 1) that has a collective meaning.

Some tokens are a single character (i.e. \{ and ,) while others can be several
characters long, such as numbers (123), string literals ("hi"), and
identifiers (min). 

Some characters from the source code don't have any significance to the
scanner such as whitespace and comments. These things are only useful to 
the programmer, the interpreter will typically ignore them.

```c
float avg = (min + max) / 2;
```

The statement above would result in the following tokens:
- float
- avg
- =
- (
- min
- \+
- max
- )
- 2
- ;

Scanning is a ubiquitous task done for every language. Open source tools have 
been created that take a predefined grammar and output a fully 
functioning scanner. See [flex](https://github.com/westes/flex)

## Parsing
The parser provides the language with it's syntax by utilizing rules outlined 
in the language's **grammar**. Much like in linguistic languages, the grammar
dictats how words (tokens) will link together to form coherent expressions 
and statements.

The parser takes a flat sequence of tokens, and constructs them into an
**Abstract Syntax Tree (AST)**.

![language structure](../posts/images/The-Anatomy-of-a-Compiler/AST.svg)

During the construction of this AST, the interpreter will find and report
any syntax errors.

## Static analysis
At this point we know the syntactic structure of the code but that's it.

For example, given the expression *a + b* we know that we're adding *a* and 
*b*, but what do *a* and *b* refer to? How are they defined, what scope do 
they belong to?

The first part of analysis is called **binding**. For each identifier, we find
out where it's defined, then we can connect the name to it's definition.
This is also where we resolve the scope of each identifier and ensure that
it's being respected, if it isn't we'll throw an error.

In the case of a statically typed language, we'll also determine if the types
of the values being added support it. If not, we'll throw an error.

The defininitions and types are often stored back on the tree in the form 
of attributes or in a separate lookup table using the identifiers as keys. 
If an even more powerful form of bookkeeping is required, one may transform 
the tree into an entirely new data structure called an Intermediate 
Representation (IR). This will be covered in the next section.

Everything up to this point is referred to as the language's **front end**.
This is where all of the language specifics occur. From this terminology you 
probably deduced that the language also has a back end. The language's 
**back end** is concerned with the final architecture where the code will run.
This terminology was coined long ago, in the time since then modern 
techniques have been developed to make connecting this pipeline easier and 
more flexible. These techniques exist in the **middle end** (try not to 
think too hard about that name).

## Intermediate representations
A compiler is essentially a pipeline where each stage's primary job is to 
transform the data in such a way that the next stage is easier to implement.

The **Intermediate representation (IR)** is part of the middle end. Essentially, 
it's a standardized representation of the data that enables you to mix and match
different front ends and back ends. This way you could reduce your workload 
significantly by reusing parts of the pipeline, or you can eliminate work 
entirely by utilizing pre-written backends for a large swath of architectures.
Many standards exist, see "control flow graph", "static single-assignment",
"continuation-passing style", and "three-address code".

Utilizing IR to mix and match front ends and back ends is how compilers
like GCC are able to support so many different languages and architectures.

## Optimization
Another component of the middle end, optimizations can be applied to the IR 
in order to make the code more efficient once it's compiled down to its 
final execution form.

For example, we could execute the following during compilation and replace
each instance of *pennyArea* with a discrete value making the program more 
efficient at runtime.

```c
float pennyArea = 3.14159 * (0.75 / 2) * (0.75 / 2);
```
would be replaced with:
```c
float pennyArea = 0.4417860938
```

## Code generation
In this step we generate the instruction primitives that will be run by the
CPU. This is officially where the back end begins.

There are ultimately two paths one can take, do you want to generate code 
that will run nativly on a real CPU, or code that will run within a container
on a virtual CPU? Compiling down to a real CPU results in the fastest execution
, but can be a highly involved task due to the complex nature of 
modern CPU architectures. 

There's another option, compiling to **bytecode**. This is virtual machine code,
instead of instructions that will run on a real chip, this code is for a 
hypothetical (idealized) machine. It's called bytecode because each instruction 
is usually a single byte long. This code will then run on a virtual machine.

## Virtual machine
There's no real chip that speaks in bytecode, we'll need to translate it.
There are again two paths to choose from, we can write a mini compiler for 
each architecture we'd like to support that converts the bytecode to native
code, this will have to be done for every chip that needs to be supported but 
we'll get to reuse the rest of the compiler pipeline across all the machines 
we support. Here, the bytecode is essentially being used an an IR.

The other option is to write a **virtual machine (VM)**. This is a program which 
emulates the aforementioned hypothetical chip. This option is significantly
slower than running native code, however it offers the upsides of being 
much more simple and portable.

## Runtime
It's time to run the compiled program. If we compiled down to native machine 
code, we can just tell your OS to run the program, if we went the VM route 
we'll need to start up the VM and load the program into it.

In both cases there will be services that run alongside the executing program 
providing assistance where needed, i.e. garbage collector.

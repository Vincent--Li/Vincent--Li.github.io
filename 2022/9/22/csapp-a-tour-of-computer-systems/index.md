# CSAPP: A Tour of Computer Systems



## 1.1 Information is Bits + Contexts


The same bits may have different meaning under different contexts

```C
// hello.c
#include <stdio.h>

int main() {
    printf("hello, world\n");
    return 0;
}
```

![图 3](/images/be1a33a3e4634fa4e621a2ed91f42f44e8c2f9665fcbe660c14691fc5c02825f.png) 


## 1.2 Programs Are Traslated by Other Programs into Different Forms


![图 1](/images/5c818ebc36e28ba95814e2d1891ac1ad1a391c63a556fc09be2b6451cff31d99.png)  

The programs that perform four phases(*preprocessor, compiler, assembler, and linker*) are known colletively as the *compilation system*

- *Preprocessing phase*. The preprocessor(cpp) modifies the original C program according to directives that begin with the '#' character. And insert it direcly into another program text(another C program, end with .i suffix)
- *Compilation phase*. The compiler(cc1) translate the text file hello.i into text file hello.s (contains an assembly-language program.)
- *Assembly phase*. The assembler(as) traslates hello.s into machine-language instructions, packages them in a form known as ***relocatable object program*** and store the result the object file hello.o. This file is a binary file containing 17 bytes to encode the instructions for function main. 
- *Lingking Phase*. linker(ld) handles merging of code. The result is hello file, which is an executable object file. 

## 1.3 It pays to Understand Compi!
[图 7](/images/6b251f20a7f65dc53b7b53ca7b96c4454b0b8806e7a6a90d25f004c7d7d76a10.png)  
lation Systems Work
- *To optimize programm performance*
- *To Understand link-time errors*
- *To avoid security holes*

## 1.4 Processor Read and Interpret Instructions Stored in Memory

### 1.4.1 Hardware organization of a System

![图 5](/images/b49bcbc5bbdccdee598dce8e685a8d2181839fea23e0bc9c319fe34d3b0a59c0.png)  

- Buses
- I/O Devices
- Main Memory
- Processor


### 1.4.2 Running the hello Program
![图 9](/images/14db4ca2480ca3261c241a2880f378aaec99e92c28eb045ade11024a1de2476b.png)  


## 1.5 Caches Matter

![图 10](/images/e5437896172f14eafcaaee0b332e0639287c85466d15933ed91655067971ea51.png) 


## 1.6 Storage Devices Form a Hierarchy

![图 11](/images/22ee5b3c801e188f877664d932a5f0a438408fce9cce3bc6e81b476981bddf3e.png)  


## 1.7 The Operating System Manages the Hardware

![图 13](/images/81a6d7758d8e04866c00478fe2c197982ae7eb3fbc0508f93554ec35ca6427ea.png)  

As figure suggests:
- Files are abstractions for I/O devices
- Virtual memory is a abstractions for both the main memory and disk I/O devices
- Processes are abstrations for the processor, main memory, and I/O devices


### 1.7.1 Processes

The operation system keeps track of all the state information that the process needs in order to run. 

This state, **context** includes infromation such as :
- the current value of the PC
- the register file
- the contents of main memory

![图 14](/images/9db19e81638a1bb799fbacb70851fe8d7e01cd0791bf3f176590f54c22ded286.png)  

As Figure above indicates, the transition from one process to another process is managed by the operation system **kernel**

Kernel is not a separate process. It is a collection of code and data structures that the system uses to manage all the process.

### 1.7.2 Threads

A process can consist of multiple execution units, called threads, each running in the context of the process ans sharing the same code and global data. 

### 1.7.3 Virtual Memory 

![图 15](/images/d3c943aacbe69f3135cbc340f16d57d37ed223e0a327ae3975c681e4a89d85aa.png)  


Virtual memory is an abstractio that provides each process with the **illusion** that is has exclusive use of the main memory. 
Each process has the same uniform view of memory, which is known as its **virtual address space**.

- *Program code and data*. Code and data areas are initialized directly from the contents of an executable object file. 
- *Heap*. The code and data areas are followed immediately by the run-time *heap*, the heap expands and contracts dynamically at run time as a result of calls to C standard library routines such as malloc and free. 
- *Shared libraries*. Near the middle of the address space is the shared libraries. It is powerful but somewhat difficult.
- *Stack*. At the top of the user's virtual address space is the user stack that the compiler uses to implement function calls. 
- *Kernel virtual memory*. The top region of the address space. This is reservied for the kernel. 

### 1.7.4 Files
Introduce later

## 1.8 Systems Communicatie with Other Systems Using Networks
Network can be viewed as another I/O device. Examples cloude view below. 
![图 16](/images/14b18a714f79ad3329422c2532402d547a1ebc6e8f6673c0e8d8970d1e21afa1.png)  

![图 17](/images/0cd4521b20979a6aa734b35a9f35e4a57f8d6048e4866fe362c28047603bca50.png)  

## 1.9 Important Themes
### 1.9.1 Amdahl's Law
let's say:
- $T_{new}$: the time after optimization
- $T_{old}$: The tiem before optimization
- $\alpha$: The fraction that optimized time can be optimized
- k: improve performance by a factor of k

$$
T_{new}=(1-\alpha)T_{old}+(\alpha T_{old})/k
$$
$$
=T_{old}[(1 - \alpha ) + \alpha / k]
$$
The speedup S is : 
$S = T_{old}/T_{new}$
$=$$1 \over (1 - \alpha ) + \alpha / k$

Amdahl's law describes a general principle for improving any process. In addition to reduce the cost of manufacturing razor blades, or a student trying to improve his or her grade point average. 

### 1.9.2 Concurrency and Parallelism

- **Concurrency**: refer to the general concept of a system with multiple, simultaneous activities
- **Parallelism**: refer to the use of concurrency to make a system run faster. Parallelism can be exploited at multiple levels of abstraction in a computer system. We highlight three levels here: 

1. Thread level Parallelism

![图 18](/images/d97360fb908efd3cd35553cf7ea2a9244692259a2626ca4a88a08b4643ae7a2d.png)  


![图 19](/images/0736ba9748506309ea9610bd45104b8d2267279690b95654aec7a105b8dace3f.png)  

2. Instruction-Level Parallelism

Modern processors can execute multiple instructions at one time. For example, early microprcessors (Intel 8086) required multiple clock cycles to execute a single instruction(IPC). More recent processors can rate 2-4 instructions per clocl cycle. 

Processors that cal sustain execution rates faster than 1 instruction per cycle are called **superscalar processors**. 

3. Single-Instruction, Multiple-Data(SIMD) Parallelism
See Web Aside: OPT:SIMD

### 1.9.3 The importance of Abstractions in Computer Systems

![图 20](/images/5b610004bf06e6bebd0994e8f69d76b3f07bb205c7a4c3fe89bb5a153f6a87cb.png)  



























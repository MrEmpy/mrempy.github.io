![](https://cdn-images-1.medium.com/max/800/0*hFHxet6fJJ-gyHH9.jpg)

Buffer Overflow is one of the oldest faults in the history of computing, and to this day, it's been a problem, but not as often as before. These buffer overflow vulnerabilities can occur in many types of software, from operating systems, to client/server applications, and in desktop software.

In this article, you will learn how to understand buffer overflow and also practice it.

## What is Buffer Overflow?

In computer security and programming, a data overflow or buffer overflow is an anomaly where a program, when writing data to a buffer, overflows the limits of the buffer and overwrites adjacent memory. This is a special case of a memory security breach.

They are more common in C/C++ programming languages, as arrays are not automatically checked as in other languages (C#, Python, Ruby, Perl). Therefore, this makes it possible to write data outside the allowed limit, and can be used to change variable values, redirect to another function, call functionalities and among others.

![](https://miro.medium.com/max/640/0*LSpKV081ZjkXHglF.png)

In the image, the fields marked in blue are the buffers, which only store 8 bytes. If the value passed is greater than eight, the last bytes will replace the other bytes in memory. See the following example:

Code:

```
#include <stdio.h>

int main() {
    char message[] = "Very good";
    char buffer[8];
    puts("Write your message here: ");
    gets(buffer);
    printf("Overflow: %s\n", message);
    printf("Your message is: %s\n", buffer);
    return 0;
}
```

Execution:

![](https://miro.medium.com/max/640/1*hta69sb-oU5bAy3psjnyfA.png)

On the first run, the letter A was repeated seven times, and everything went smoothly. Now look at the second run, the letter A was repeated 10 times, in “Overflow: “, the letter A appeared only twice, which means it was possible to replace the message “Very good”.

## Memory Layout of a Process

![](https://miro.medium.com/max/358/0*f3lqlI5e7kdqsJTV.png)

This is the memory layout of a process, let’s understand each part of it:

* Command line arguments and environment variables are stored at the top of the process’s memory layout at the top addresses.
* Next comes the stack segment. This is the area of memory that is used by the process to store the function’s local variables and other information that is saved every time a function is called. This other information includes the return address i.e. the address where the function was called from, some information about the caller’s environment such as machine registers etc is stored on the stack. It’s also worth mentioning here that each time a recursive function is called, a new stack frame is generated so that each set of local variables doesn’t interfere with any other set.
* The heap segment is the one that is used for dynamic memory allocation. This segment is not limited to a single process, but is shared among all processes running on the system. Any process can dynamically allocate memory from that thread. As this segment is shared between processes, the memory of this segment should be used with caution and should be deallocated as soon as the process completes using this memory.
* As it looks in the picture above, the stack grows downwards while the stack grows upwards.
* All global variables that are not initialized in the program are stored in the BSS segment. Upon execution, all uninitialized global variables are initialized to the value zero. Note that BSS stands for ‘Block Initiated by Symbol’.
* All initialized global variables are stored in the data segment.
* Finally, the text segment is the memory area that contains the machine instructions that the CPU executes. Typically, this segment is shared between different instances of the same program that is running. Since there is no reason to change the CPU instructions, this thread has read-only privileges.

## Replacing the Value of a Variable

```
#include <stdio.h>

int main() {
    char buffer[32];
    int confirm = 0;
    puts("Write your message here: ");
    gets(buffer);
    if (confirm == 0) {
        printf("Your message is: %s\n", buffer);
        return -1;
    }
    puts("You managed to change the value of the confirm variable!\n");
    printf("Value: %d", confirm);
    return 0;
}
```

There is a variable called comfirm in which its value is 0, after a few lines, there is a check that is made, if the confirm variable is equal to 0, it will print a message saying what you typed, and later, it will return -1 , exiting its execution. Analyzing the code, it is not going to the lines below as there is a lock. But it is possible to change the value of the confirm variable by overflowing the buffer.

See this example below:

![](https://miro.medium.com/max/640/1*CDmWCm2T6cDgj0eKionvyA.png)

The buffer was 32 bytes, and on the first run only 7 bytes were added, which resulted in the message saying what was typed. After adding 51 bytes in the second run and sending them, it was possible to change the value of the confirm variable, which is now 1094795585.

## Conclusion

Today we learned how the buffer overflow failure occurs and how the stack works. Try running the [example codes](https://github.com/MrEmpy/BufferOverflow/tree/main/01-introduction) on your machine and see the result. In the next articles we will cover ways to get shell from a machine.

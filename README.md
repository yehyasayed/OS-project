# OS - Project

Add System Call (Final term project)



## Team Member Names

* Yeyha Sayed Abd Elgwad Ali.

* Ngeu Malok Majok Bol.

  


## Virtual Machine Settings

Number of cores  **2**
The capacity of memory  **4G**
The kernel version  **5.8.1**



## Steps of how adding the system call

### **Section 1** - Preparation

*In this section, we will download all necessary tools to add a basic system call to the Linux kernel and run it.*



**1.1** - Fully update operating system.

```
$ sudo apt update && sudo apt upgrade -y
```



**1.2** - Download and install the essential packages to compile kernels.

```
$ sudo apt install build-essential libncurses-dev libssl-dev libelf-dev bison flex -y
```



**1.3** - Clean up installed packages.

```
$ sudo apt clean && sudo apt autoremove -y
```



**1.4** - Download the source code of the latest stable version of the Linux kernel `version 5.8.1` .

```
$ wget -P ~/ https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.8.1.tar.xz
```



**1.5** - Unpack the source code. 

```
$ tar -xvf ~/linux-5.8.1.tar.xz -C ~/
```



**1.6** - Reboot computer.



------



### **Section 2** - Creation

*In this section, we will write a basic system call in C and integrate it into the new kernel.*



**2.1** - Change working directory to the root directory.

```
$ cd ~/linux-5.8.1/
```



**2.2** - Create the home directory of system call.

```
$ mkdir identity
```



**2.3** - Create a C file for system call.

```
$ nano identity/identity.c
```

Then, we will write the following code in it.

```c
#include <linux/kernel.h>
#include <linux/syscalls.h>

SYSCALL_DEFINE0(identity)
{
    printk("yehya sayed\n");
    return 0;
}
```



**2.4** - Create a Makefile for system call.

```
$ nano identity/Makefile
```

Then, we will write the following code in it.

```
obj-y := identity.o
```



**2.5** - Add the home directory of system call to the main Makefile of the kernel.

Open the Makefile.

```
$ nano Makefile
```

Add the home directory of system call.

```
kernel/ certs/ mm/ fs/ ipc/ security/ crypto/ block/ identity/
```



**2.6** - Add a function prototype for system call to the header file of system calls.

Open the header file.

```
$ nano include/linux/syscalls.h
```

write the code above `#endif`.

```
$ asmlinkage long sys_identity(void);
```

Then, save it.



**2.7** - Add system call to the kernel's system call table.

Open the table.

```
$ nano arch/x86/entry/syscalls/syscall_64.tbl
```

Add the following code at the end of this section.

```
440     common  identity                sys_identity
```

Then, save it.



------



### **Section 3** - Installation

*In this section, we will install the new kernel.*



**3.1** - Configure the kernel.

```
$ make menuconfig
```

Make no changes to keep it in default settings.

Then, save it.



**3.2** - Find out how many logical cores.

```
$ nproc
```



**3.3** - Compile the kernel's source code with 2 core.

```
$ make -j2
```



**3.4** - Prepare the installer.

```
$ sudo make modules_install -j2
```



**3.5** - Install the kernel.

```
$ sudo make install -j2
```



**3.6** - Update the bootloader of the operating system with the new kernel.

```
$ sudo update-grub
```



**3.7** - Reboot computer.



------



### **Section 4** - Result

*In this section, we will write a C program to check whether system call works or not.*



**4.1** - Check the version now.

```
$ uname -r
```

```
5.8.1
```



**4.2** - Change working directory to home directory.

```
$ cd ~
```



**4.3** - Create a C file to generate a report of the success or failure of system call.

```
$ nano report.c
```

Write the following code in it.

```c
#include <linux/kernel.h>
#include <sys/syscall.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <errno.h>

#define __NR_identity 440

long identity_syscall(void)
{
    return syscall(__NR_identity);
}

int main(int argc, char *argv[])
{
    long activity;
    activity = identity_syscall();

    if(activity < 0)
    {
        perror("Your system call faild.");
    }
    else
    {
        printf("Congratulations, yehya system call is added\n");
    }

    return 0;
}
```

Then, save.



**4.4** - Compile the C file you just created.

```
$ gcc -o report report.c
```

Then, run the C file compiled.

```
$ ./report
```

If it displays the following, everything is working as intended.

```
yehya system call is added
```

Then, check the last line of the `dmesg` output.

```
$ dmesg
```

At the bottom, we see.

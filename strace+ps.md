<div align = "center">

# Understanding `ps` Command
</div>

## Introduction
-  "used to get the more and detailed information about a specific process or all processes"
- "used to know whether a particular process is running or not, who is running what process in system, which process is using higher memory or CPU, how long a process is running, etc."

### Testing the command
- First ran some processes in BG from different users: `su - ansible` and ran connected with VMs using `ssh ansible@192.168.122.142`
- The outputs of running the various `ps` related commands along with their explanation has been provided below.
- We use ps aux to display the processes of all users. But, we notice here that we don't use the - sign with options that we were using till now. So, using options without (-) is known as BSD syntax to write commands. The other one (one with -), which we use regularly, is known as Standard syntax to write commands. [[Ref!]](https://cloudxlab.com/assessment/displayslide/7359/bsd-syntax-of-commands#:~:text=We%20use%20ps%20aux%20to,Standard%20syntax%20to%20write%20commands.)
- BSD = Berkeley Software Distribution & BSD Syntax is the old UNIX syntax - Really great story about the shift from BSD Syntax to the Standard Syntax https://askubuntu.com/a/485092/1763786

#### `ps`: shows current user's processes:


<div align = "center">

# Understanding Strace
</div>

## Clearing Pre-Requisites

### Kernel
- Inside /boot
- Bridge betwen the applications & hardware
- When system starts, only a specific portion of OS called Kernel is loaded.
- Stuff like CPU, devies and memory managed by Kernel 
- Example:
> Camera app asks Kernel, "Let me use the Camera Hardware, Flash Hardware, Storage, CPU, RAM, etc?" and the Kernel accepts the request by giving access to these hardwares.

>GoogleServices wants to run in the background, use CPU, RAM, etc. It will request Kernel to allow permission to use them.

> Whem system starts all boot animations, startup apps, login screen are dsplayed thanks to Kernel

### SystemCalls
- Interface used by processes to access hardware/resource
- "I want to access XYZ hardware, Kernel"
- How a program requests a service from Kernel

- From `man syscalls`:
    > The system call is the fundamental interface between an application and the Linux kernel.
- Here interface: made of inter (between) and faces (fronts/surfaces) ---> between faces ---> where 2 or more faces meet
- This means that SysCall comes between softwares and Linux Kernel just like how Kernel is the interface between Software & Hardware
- `strace ls` to show systemcalls being made when ls command is run contains the `write` SysCall:
    ```
    write(1, "ansible  Desktop       Downloads"..., 100ansible  Desktop       Downloads  filestash		   go	    go.sum  minio   Pictures   Screencasts  snap
    ``` 
- Write is a systemcall https://en.wikipedia.org/wiki/Write_(system_call) which outputs data from a program (ls is also c language program)
> write thus takes three arguments:
        - The file code (file descriptor or fd). (Type of output; 1=stdout for me) 
        - The pointer to a buffer where the data is stored (buf). ("My Name = Yash\n")
        - The number of bytes to write from the buffer (nbytes). (length of string = 15)

- Writing own program like ls that utilises the write syscall for outputting text of to screen:
    ```
    void main() {
	    write(1, "My Name = Yash\n",15);i
    }
    ```

- I built this code using `gcc syscallsSelf.c -o syscallsSelf`, where gcc is the compiler and -o is for defining the name.

- Running `/syscallsSelf` gave the correct output of `My Name = Yash` but running it with strace gave:

```
strace ./sysCallsSelf
    execve("./syscallsSelf", ["./syscallsSelf"], 0x7ffd55607780 /* 52 vars */) = 0
    brk(NULL)                               = 0x559277940000
    arch_prctl(0x3001 /* ARCH_??? */, 0x7ffdf89f8e70) = -1 EINVAL (Invalid argument)
    mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3ca3ca7000
    access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
    openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
    newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=88679, ...}, AT_EMPTY_PATH) = 0
    mmap(NULL, 88679, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f3ca3c91000
    close(3)                                = 0
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
    read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P<\2\0\0\0\0\0"..., 832) = 832
    pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
    newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=2072888, ...}, AT_EMPTY_PATH) = 0
    pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
    mmap(NULL, 2117488, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f3ca3a00000
    mmap(0x7f3ca3a22000, 1540096, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x22000) = 0x7f3ca3a22000
    mmap(0x7f3ca3b9a000, 360448, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x19a000) = 0x7f3ca3b9a000
    mmap(0x7f3ca3bf2000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1f1000) = 0x7f3ca3bf2000
    mmap(0x7f3ca3bf8000, 53104, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f3ca3bf8000
    close(3)                                = 0
    mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3ca3c8e000
    arch_prctl(ARCH_SET_FS, 0x7f3ca3c8e740) = 0
    set_tid_address(0x7f3ca3c8ea10)         = 48185
    set_robust_list(0x7f3ca3c8ea20, 24)     = 0
    rseq(0x7f3ca3c8f060, 0x20, 0, 0x53053053) = 0
    mprotect(0x7f3ca3bf2000, 16384, PROT_READ) = 0
    mprotect(0x5592763e1000, 4096, PROT_READ) = 0
    mprotect(0x7f3ca3cdc000, 8192, PROT_READ) = 0
    prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
    munmap(0x7f3ca3c91000, 88679)           = 0
    write(1, "My Name = Yash\n", 15My Name = Yash
    )        = 15
    exit_group(15)                          = ?
    +++ exited with 15 +++
```

- `write(1, "My Name = Yash\n", 15My Name = Yash) = 15` is where the output is being printed on the terminal screen

- Basic work-flow of this output is:
    1. syscallsSelf = waiter
    2. runs on or enters System = restaurant
    3. allocates memory in RAM = gets ready
    4. libraries are searched = shelves are searched 
    5. library files fetched = specific tools like plates grabbed
    6. working directory entered = goes to kitchen
    7. learns listing is to be done = learns about work plan: need to take food to customers
    7. sees all files & directories = looks around the kitchen for orders to be taken to customer
    8. Identifies lists/directories to be displayed = identifies the orders to be taken to customer
    9. Console-output using Write SysCall = Takes and gives order to customer
    10. Opened libraries closed & exits = Cleans the tools and exits 

#### References
- [Understanding SystemCalls](https://opensource.com/article/19/10/strace#:~:text=A%20system%20call%20is%20a,understand%20how%20system%20calls%20work.)
- [System Calls | Read | Write | Open | Close | Linux](https://www.youtube.com/watch?v=CHs9WwvEKdg&pp=ygUbc3lzdGVtY2FsbHMgZXhwbGFpbmVkIGxpbnV4)
- [Linux System Calls Explained](https://www.youtube.com/watch?v=JdVn3RMLU_Q&ab_channel=NirLichtman)
- [Linux Tutorial: How a Linux System Call Works](https://www.youtube.com/watch?v=FkIWDAtVIUM&pp=ygUXd2hhdCBhcmUgc3lzY2FsbHMgbGludXg%3D)
- [Lets understand Linux - syscall for noobs](https://www.youtube.com/watch?v=gYpWkbm6K98&t=18s&ab_channel=BugsWriter) [Great]
- [eli5 What's the difference between os and an kernel](https://www.reddit.com/r/explainlikeimfive/comments/12j9b4o/eli5_whats_the_difference_between_os_and_an_kernel/)
- [Which linux system call is used by ls command in linux to display the folder/file name?](https://stackoverflow.com/questions/12920976/which-linux-system-call-is-used-by-ls-command-in-linux-to-display-the-folder-fil)
- [SysCalls man page](https://man7.org/linux/man-pages/man2/syscalls.2.html)
- [Are system call part of the kernel or are they part of the OS?](https://unix.stackexchange.com/questions/504288/are-system-calls-part-of-the-kernel-or-are-they-part-of-the-os#:~:text=So%20a%20system%20call%20is,a%20library%20(e.g.%20libc%20).)
- [Syscalls, Kernel vs. User Mode and Linux Kernel Source Code - bin 0x09](https://www.youtube.com/watch?v=fLS99zJDHOc&ab_channel=LiveOverflow) [Great]
- [write man page](https://www.manpagez.com/man/2/write/#:~:text=Write()%20attempts%20to%20write,iov%5Biovcnt%2D1%5D.) 

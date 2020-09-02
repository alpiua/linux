memory
==
# Memory Consumption in Linux (c) Emil Norisky, 2012-04-10
This article explains a reasonable method to measure memory consumption of a process on Linux. Linux is equipped with virtual memory management and, therefore, measuring the memory consumption of a single process is not as simple as most users think. This article explains what information you can get from each indicator related to memory consumption. 

## An Allegory to Get the Idea
This article explains three indicators that can possibly be used to measure the memory consumption of a single process on Linux. VSZ (Virtual Memory Size), RSS (Resident Set Size), and PSS (Proportional Set Size).

Although this will lack accuracy, let us consider an allegory to get the idea. There are three people sharing a room. We will consider each person to represent a process, and living expenses to represent memory consumption. Measuring the memory consumption of a single process will be represented as calculating the total living expense for one person in this allegory.

Each person has their own cell phone line that is not being shared. All three indicators, VSZ, RSS, and PSS, will count the cell phone bills as each persons living expense individually, and there is no problem with this.

The shared room comes with a garage space that can be used if they pay for it, but they all don't drive and they are not using it. However, VSZ will count the entire garage space cost as each persons living expense even though they are not using it. VSZ, therefore, represents the total living expense when they spend on every possible thing regardless of the actual usage. RSS and PSS only count expenses that are actually being used, and therefore, they will not count the garage space cost because it is not being used.

Since they are sharing the internet connection and the cable TV, they split up those bills. However, RSS will count the entire amount of the internet connection and cable TV as each persons living expense, even though they are sharing it and splitting up the bill. The idea of RSS is to calculate living expenses as it was not shared with anybody else.

PSS will only count one third of the internet connection and cable TV bill as each persons living expense, because they are sharing it. This is more reasonable than RSS since it is considers the fact that they are sharing it.

Let's make the allegory a little bit more complicated in order to understand the limitations of PSS. One person is always on the internet and doesn't watch TV that much. Hence, that person pays 50% of the internet connection bill and 20% of the cable TV bill upon agreement. PSS, however, cannot handle situations like this. It will simply calculate one third of the internet connection bill and cable TV bill as each persons living expense.

Using PSS is what I consider most reasonable. However, there are limitations and there are also situations where RSS may work better. RSS is reasonable when you want to know the total living expense when you move out and live on your own. 

## Virtual Memory Management
Measure the memory consumption of a single process on operating systems that were used in the good old days such as MS-DOS or µITRON was simple. It is not, however, that simple in modern operating systems since they are equipped with virtual memory management, which has many benefits, yet, it makes it difficult to measure the memory consumption of a single process.

Linux is equipped with virtual memory management and has some important functions that are related to memory consumptions measurement. Before explaining them, we will start by checking the result of the ps command that shows information of processes. Figure 2 shows how the result of the ps command looks like.
```sh
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
norisky  29065  0.1  0.2   8132  5604 pts/8    Ss   19:03   0:00 zsh
norisky  29075  0.0  0.1  10228  3772 pts/8    S+   19:03   0:00 vi
norisky  29526  8.0  0.5  27708 10676 pts/4    S+   19:05   0:00 emacs -nw
norisky  29527  0.0  0.0   2976  1080 pts/6    R+   19:05   0:00 ps aux
```
Figure 2. ps aux
In the man page, we can see that VSZ and RSS are related to memory consumption. The man page says:

**VSZ** 
    virtual memory size of the process in KiB (1024-byte units). Device mappings are currently excluded; this is subject to change. (alias vsize). 
**RSS**
    resident set size, the non-swapped physical memory that a task has used (in kiloBytes). (alias rssize, rsz). 

Which should we use, VSZ or RSS? The following sections will explain both indicators and also another indicator, PSS (Proportional Set Size), which is relatively new. PSS is not shown in the ps command but you can see it from the /proc file system.

## Technical Terms

**page**

This is a block of memory that is used in memory management on Linux. One page is 4096 bytes in typical Linux systems. 
 
**physical memory**

This is the actual memory, typically the RAM, that is on the computer.

**virtual memory**

This is a memory space given to a process that lets the process think it has its own continuous memory that is isolated from other processes regardless of the actual memory amount on the computer or the situation of other processes memory consumption. A virtual memory page can be mapped to a physical memory page, and hence, processes only need to think about the virtual memory. 

## VSZ (Virtual Memory Size) and Demand Paging

Considering the VSZ (virtual memory size) to measure memory consumption of a process does not make much sense. This is due to the feature called demand paging, which suppresses unnecessary memory consumption.

For example, a text editor named emacs has functions that can handle XML files. These function, however, are not used all the time. Loading these functions on to the physical memory is not necessary when the user just wants to edit a plain text. The demand paging feature does not load pages unless they are used by the process.

This is how it works. First, when the program starts, Linux gives a virtual memory space to the process but does not actually load pages that have functions on to the physical memory. When the program actually calls a function in the virtual memory, the MMU in the CPU tells Linux that the page is not loaded. Then Linux pauses the process, loads the page on to the physical memory, maps the page to the virtual memory of the process, then lets the process run again from where it got paused. The process, therefore, does not need to know that it got paused, and just simply assume the function was loaded on the virtual memory and use it.

VSZ (virtual memory size) describes the entire virtual memory size of the process regardless of pages being loaded on the actual memory or not. This is, therefore, not a realistic indicator to measure memory consumption since it includes pages that are not actually consumed.

## RSS (Resident Set Size) and Shared Libraries

RSS (Resident Set Size) describes the total amount of the pages for a process that are actually loaded on the physical memory. This may sound like the real amount of memory being consumed by the process, and is better than VSZ (virtual memory size), but it is not that simple due to the feature called shared libraries or dynamic linking libraries.

A library is a module that can be used by programs to handle a certain feature. For example, libpng.so takes care of compressing and decompressing PNG image files, and libxml2.so takes care of handling XML files. Instead of making each programmer write these functions, they can use libraries developed by others and achieve the result they want.

A shared object is a library that can be shared by multiple programs or processes. For example, let's say there are two processes running at the same time that want to use XML handling functions that are in the shared library libxml2.so. Instead of loading the pages that have the exact same functions multiple times, Linux loads it once on to the physical memory and maps it to both processes virtual memory. Both processes do not need to care if they are sharing the functions with somebody else because they can access the functions and use them inside their own virtual memory. Due to this feature, Linux suppresses unnecessary duplication of memory pages.

Now, let us go back to the same example above. Emacs, a text editor, has functions that can handle XML files. This is taken care of by the shared library libxml2.so. This time, the user that is running emacs is actually working with XML files and emacs is using the functions in libxml2.so. Meanwhile, there are two more process running in the background that are using libxml2.so too. Since libxml2.so is a shared library, Linux only loads it once on the physical memory and maps it to all three processes virtual memory.

When you see the RSS (Resident Set Size) of emacs, it will include the pages of libxml2.so. This is not wrong because emacs is actually using it. But what about the other two processes? It is not just emacs that is using those functions. If you sum the RSS (Resident Set Size) of all three processes, libxml2.so will be counted three times even though it is only loaded on the physical memory once.

RSS (Resident Set Size), therefore, is an indicator that will show the memory consumption when the process is running by it self without sharing anything with other processes. For practical situations where libraries are being shared, RSS (Resident Set Size) will over estimate the amount of memory being consumed by the process. Using to measure memory consumption of a process is not wrong but you may want to keep in mind of this behaviour. 

## PSS (Proportional Set Size)

PSS (Proportional Set Size) is a relatively new indicator that can be used to measure memory consumption of a single process. It is not be available on all Linux systems yet but if it is available, it may come in handy. The concept is to split the memory amount of shared pages evenly among the processes that are using them.

This is how PSS (Proportional Set Size) calculates memory consumption: If there are N processes that are using a shared library, each process is consuming one N-th of the shared libraries pages.

For the example above, emacs and two other processes were sharing the pages of libxml2.so. Since there are three processes, PSS will consider each process is consuming one third of libxml2.so's pages.

I consider PSS (Proportional Set Size) as a more realistic indicator compared to RSS (Resident Set Size). It works well especially when you want to consider the memory consumption of an entire system all together, and not each process individually. For example, when you are developing a system that has multiple processes and daemons and you want to estimate how much memory you should install on the device, PSS (Proportional Set Size) works better than RSS (Resident Set Size).


## Programs

### ps
`ps -ylC <procname>`  
`ps aux`

### pmap
report memory map of a process
`pidof php-fpm | xargs pmap -d | grep '^mapped' `

### ps_mem
Can determine how much RAM is used per program (not per process).

`for i in $(pgrep php-fpm); do echo "child $i"; ps_mem -s -p $i; done;`

### smem
Reports physical memory usage, taking shared  memory  pages into  account. Unshared  memory is reported as the USS (Unique Set Size).  Shared memory is divided evenly among the  processes sharing that memory. 

The unshared memory (USS) plus a process's proportion of shared memory is reported as the PSS (Proportional Set  Size). The USS and PSS only include physical memory usage. 

They do not include memory that has been swapped out to disk.

Memory can be reported by process, by user, by mapping, or  sys‐temwide. Both text mode and graphical output are available.

### htop

There are two modes for CPU metrics reporting: the default one, and a "detailed CPU time" which can be enabled from the Setup screen (Display Options / Detailed CPU time). All of them show the percentage of time spent in different processes:

Default mode

    Blue: low priority processes (nice > 0)
    Green: normal (user) processes
    Red: kernel time (kernel, iowait, irqs...)
    Orange: virt time (steal time + guest time)

Detailed mode

    Blue: low priority threads (nice > 0)
    Green: normal (user) processes
    Red: system processes
    Orange: IRQ time
    Magenta: Soft IRQ time
    Grey: IO Wait time
    Cyan: Steal time
    Cyan: Guest time

Memory meters are more straightforward:

    Green: Used memory pages
    Blue: Buffer pages
    Orange: Cache pages


### pss.py

```python
#! /usr/bin/python
# coding: utf-8
##-----------------------------------------------------------------------------
## pss.py --- Print the PSS (Proportional Set Size) of accessable processes
##                        copyright (C) 2012 Emil Norisky, all rights reserved.
##-----------------------------------------------------------------------------
import os, sys, re, pwd


##-----------------------------------------------------------------------------
def pss_main():
    '''
    Print the user name, pid, pss, and the command line for all accessable
    processes in pss descending order.
    '''
    # Get the user name, pid, pss, and the command line information for all
    # processes that are accessable. Ignore processes where the permission is
    # denied.
    ls = []   # [(user, pid, pss, cmd)]
    for pid in filter(lambda x: x.isdigit(), os.listdir('/proc')):
        try:
            ls.append((owner_of_process(pid), pid, pss_of_process(pid), cmdline_of_process(pid)))
        except IOError:
            pass

    # Calculate the max length of the user name, pid, and pss in order to
    # print them in aligned columns.
    userlen = 0
    pidlen = 0
    psslen = 0
    for (user, pid, pss, cmd) in ls:
        userlen = max(userlen, len(user))
        pidlen = max(pidlen, len(pid))
        psslen = max(psslen, len(str(pss)))
    
    # Get the width of the terminal.
    with os.popen('tput cols') as fp:
        term_width = int(fp.read().strip())
    
    # Print the information. Ignore kernel modules since they allocate memory
    # from the kernel land, not the user land, and PSS is the memory
    # consumption of processes in user land.
    fmt = '%%-%ds  %%%ds  %%%ds  %%s' % (userlen, pidlen, psslen)
    print(fmt % ('USER', 'PID', 'PSS', 'COMMAND'))
    for (user, pid, pss, cmd) in sorted(ls, lambda x, y: cmp(y[2], x[2])):
        if cmd != '':
            print((fmt % (user, pid, pss, cmd))[:term_width - 1])


##-----------------------------------------------------------------------------
def pss_of_process(pid):
    '''
    Return the PSS of the process specified by pid in KiB (1024 bytes unit)
    
    @param pid  process ID
    @return     PSS value
    '''
    with file('/proc/%s/smaps' % pid) as fp:
        return sum([int(x) for x in re.findall('^Pss:\s+(\d+)', fp.read(), re.M)])


##-----------------------------------------------------------------------------
def cmdline_of_process(pid):
    '''
    Return the command line of the process specified by pid.

    @param pid  process ID
    @return     command line
    '''
    with file('/proc/%s/cmdline' % pid) as fp:
        return fp.read().replace('\0', ' ').strip()


##-----------------------------------------------------------------------------
def owner_of_process(pid):
    '''
    Return the owner of the process specified by pid.

    @param pid  process ID
    @return     owner
    '''
    return pwd.getpwuid(os.stat('/proc/%s' % pid).st_uid).pw_name


##-----------------------------------------------------------------------------
if __name__ == '__main__':
    pss_main()

```

## Swap


## Links
* <https://stackoverflow.com/questions/131303/how-to-measure-actual-memory-usage-of-an-application-or-process?rq=1v>
* <https://web.archive.org/web/20120520221529/http://emilics.com/blog/article/mconsumption.html>
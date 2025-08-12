+++
date = '2025-08-01T15:15:07-04:00'
draft = false
title = 'Bug Hunt - OOM Killer'
+++

Not a bug but a feature! The Out-of-Memory killer is an important Linux mechanism.

<!--more-->

### Date Encountered  
Wednesday, July 9, 2025, 6:59:08 PM

### Overview of Bug  
I was trying to run `dnf install` on a CentOS machine but kept encountering a message saying simply: Killed. In fact, any dnf operation was being killed, and I had no idea why.

### Steps I Took  
After scratching my head for a while, I consulted Perplexity for some guidance, and it led me to two places to check:

* the system logs at `/var/log/messages`
* the kernel ring buffer via `dmesg`

Specifically, I found the following:

```bash
[prompt] dmesg | grep -i -m 10 "out of memory"  
[510226.449642] Out of memory: Killed process 114815 (yum) total-vm:770112kB, anon-rss:549608kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:1256kB oom_score_adj:0 
```

```bash
[prompt] grep -m 5 "killed" /var/log/messages  
Jul  1 16:36:26 alejandro-villate systemd[1]: session-14.scope: A process of this unit has been killed by the OOM killer. 
```

I had my suspect--the OOM killer--but still didn't know what that meant. After reading through a few Stack Exchange and Stack Overflow posts, I reached out to my Meta PE mentors for some guidance, and one of them suggested looking into **swap space**. He also provided a link with which I followed along: [How To Add Swap on CentOS 7 — DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7). Following the guide solved my problem and taught me a lot in the process. TLDR; enabling swap space fixed the issue.

### What I learned  
The **Out-of-Memory Killer** is a kernel mechanism that is triggered whenever the system runs out of physical memory resources. It kills whatever process has the highest **oom_score**. You can see the oom_score of any process with:

```bash
cat /proc/<pid>/oom_score
```

**Swap space** is nothing but disk space used as an extension of RAM. It allows the system to handle more memory usage but is ultimately orders of magnitude slower than regular old RAM, since disk I/O is much, much slower.

Another cool thing I learned was the `dd` command, which I like to think of as `cp` if it was bitten by a radioactive spider: it's very versatile and very powerful. But with great power comes great responsibility (don't accidentally overwrite your entire drive doing something like `dd if=/dev/zero of=/dev/sda`). Use-cases include copying low-level data, creating disk backups, and \*drumroll\* initializing swap files to be used as swap space.

## Original Bug Hunt

I asked perplexity how I could investigate the issue and it led me to /var/log/messages

I did:  
dmesg | grep -i -m 10 "out of memory"  
[510226.449642] Out of memory: Killed process 114815 (yum) total-vm:770112kB, anon-rss:549608kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:1256kB oom_score_adj:0  

grep -m 5 "killed" /var/log/messages  
Jul  1 16:36:26 alejandro-villate systemd[1]: session-14.scope: A process of this unit has been killed by the OOM killer.  

\#\# PE mentor and Copilot suggested adding swap space  
Following along with: https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7  
swap is an area on a storage drive where the OS can temporarily store data that doesn't fit in memory  
pros:  
    safety net when server is low on memory  
cons:  
    reading and writing from swap is slower than memory  

free and swapon -s can be used to see if there is available swap space  
/dev/zero is like the opposite of /dev/null, it provides a null space to read from  

\`sudo dd if=/dev/zero of=/swapfile count=4096 bs=1MiB\`  
Now this is interesting. dd is kind of like a cp on super steroids. It lets you pick a source and destination, along with lots of other options to create a new file. In this case, \`if\` is the source (/dev/zero is like the reverse of /dev/null, giving us as many null characters as requested), \`of\` is the destination, \`count\` is the number of blocks we want to copy, and \`bs\` is the amount of BYTES in each block  
\*practical uses of dd\*  
creating a backup of a disk partition  
initializing swap space  

After following the article, I wanted to disable swap space  
\`swapoff /swapfile\`  
\`rm /swapfile\`  

Note: swap memory is slow because it reads and writes from a storage device which is orders of magnitude slower than memory  
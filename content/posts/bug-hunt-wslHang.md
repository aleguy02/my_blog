+++
date = '2025-07-17T19:07:16-04:00'
draft = false
title = 'Bug Hunt - WSL Hang'
+++

This is the first post in a series of candid bug hunt posts where I will describe an interesting bug I ran into, and how I eventually fixed it. As this is the first of these posts, I will explain the general structure that I will try to follow in future posts.  

**Date Encountered** - the official date that the bug went from a minor annoyance to something I needed to actually fix  
**Overview of Bug** - general description of what was happening and why it was an issue  
**Steps I Took** - pretty self explanatory. Steps I took while trying to address the issue  
**What I Learned** - any technical or practical takeaways from the whole process  
**Original Bug Hunt** - my original notes while troubleshooting the bug

### Date Encountered  
Monday, June 9, 2025, 4:55:12 PM

### Overview of Bug  
I was running into an issue with WSL (Windows Subsystem for Linux) where I was unable to start it at random times. Specifically, I do most of my development on WSL, and I would frequently try to open it and get  
```  
Catastrophic failure  
Error code: Wsl/Service/E_UNEXPECTED  
```  
or  
```
The operation timed out because a response was not received from the virtual machine or container.  
Error code: Wsl/Service/HCS_E_CONNECTION_TIMEOUT  
```  

### Steps I Took
When I first ran into the issue, I tried restarting WSL with `wsl --shutdown`, but this was a flaky fix. This was a bandaid--acceptable for how frequently I was using WSL at the time. Once my [fellowship](https://blog.alejandrovillate.com/posts/mlh-fellowship-journey/) started ramping up (and I would have to use WSL more frequently) I started searching for a cure. Skimming some GitHub issues led me to believe that Docker Desktop was the root issue though I wasn't sure exactly why. I lazily removed and reinstalled DD on my computer, hoping this would be the fix I was looking for. After this failed, I tried messing around with some virtualization settings through the Windows interface, but this was fruitless too. 

After a little more careful reading of [this GitHub issue](https://github.com/docker/for-win/issues/14827) and testing on my end, I confirmed Docker Desktop's Resource Saver Mode was the root issue. Restarting DD or disabling Resource Saver Mode resolved the issue.

### What I learned  
I learned that Docker Desktop runs on the WSL2 engine, meaning it runs on the same virtual machine that powers all the different Linux distributions I have installed. Resource Saver Mode pauses this machine, thus freezing all the other distributions running via WSL.  It was also cool to see GitHub Issues in action while hunting for a fix—I’d mostly seen them used for feature requests, and hadn’t realized they were also active spaces for discussions and troubleshooting, which makes sense in hindsight.

## Original Bug Hunt
\#\# Message:  
\`\`\`  
Catastrophic failure  
Error code: Wsl/Service/E_UNEXPECTED  
\`\`\`

\#\# What I Tried  
Uninstalling then reinstalling Docker Desktop (with restart)

\`\`\`  
wsl --shutdown  
wsl  
\`\`\`  
^ this is a temporary fix but it just keeps happening

\#\# My Thoughts  
I'm not sure why this is happening. I skimmed a few issues on the wsl GitHub page and was led to believe Docker Desktop could have something to do with it but honestly I don't know

\#\# Things to try  
remove Docker Desktop from startup apps

\# But this also happens (seemingly at random)...

\`\`\`  
PS C:\Users\darth> wsl  
The operation timed out because a response was not received from the virtual machine or container.  
Error code: Wsl/Service/HCS_E_CONNECTION_TIMEOUT  
\`\`\`  
It takes like a whole... fucking... minute. And sometimes it just stops working completely. It freezes on the wsl command. wsl --shutdown also stopped working. Heh

Update from one day later:  
wsl started working. I didn't run any command or restart my computer (as far as I know)

I tried turning WSL off then on again through Windows features. It seems to have worked so far, wsl is still a little slow to start up though but this is fine  
Unfortunately, the problem came back after some time

\*virtualization\*  
In Task Manager, vmicrdv (Hyper-V Remote Desktop Virtualization Service) is Stopped, but wsl is still working so this is unlikely to be the root issue

\#\# GitHub Issues  
https://github.com/microsoft/WSL/issues/10546  (distribution disk was corrupted)  
https://github.com/docker/for-win/issues/14827  (docker resource saver mode causes WSL to lock)  <--- winner winner chicken dinner

\#\# Docker Resource Saver

Trying to open WSL while DD is in resource saver mode leads to this message  
\`\`\`  
PS C:\Users\darth> wsl  
The operation timed out because a response was not received from the virtual machine or container.  
Error code: Wsl/Service/HCS_E_CONNECTION_TIMEOUT  
\`\`\`

Taking DD out of resource saver mode and trying to open WSL leads to this message  
\`\`\`  
Catastrophic failure  
Error code: Wsl/Service/E_UNEXPECTED  
\`\`\`

until WSL is restarted with \`wsl --shutdown\`. The issue is still open but the current workaround is "to restart Docker engine, WSL will continue to work, or to disable "Resource Saver" option in Docker."
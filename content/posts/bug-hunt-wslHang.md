+++
date = '2025-07-17T19:07:16-04:00'
draft = true
title = 'Bug Hunt 1 - WSL Hang'
+++

This is the first post in a series of candid bug hunt posts where I will describe an interesting bug I ran into, and how I eventually fixed it. As this is the first of these posts, I will explain the general structure that I will try to follow in future posts.  
**Overview of Bug** - general description of what was happening and why it was an issue  
**Steps I Took** - pretty self explanatory. Steps I took while trying to address the issue  
**What I Learned** - any technical or practical takeaways from the whole process

### Overview of Bug  
I was running into an issue with WSL (Windows Subsystem for Linux) where I was unable to start it at random times. Specifically, I do most of my development on WSL.
### Steps I Took

### What I learned

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
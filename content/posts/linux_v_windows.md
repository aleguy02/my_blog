+++
date = '2025-03-31T22:00:53-04:00'
draft = false
title = 'My take on Linux and Windows'
+++

Originally drafted 3/14

When I participated in MLH's Cloud Week, I found myself interacting with the Windows and Linux command lines more than ever before. When I say Windows, I mean a combination of Windows PowerShell and Command Prompt, and when I say Linux, I mean Ubuntu via WSL. I noticed many differences in the learning curve that I wanted to talk about. This isn't my answer to "Why do some people prefer Linux over Windows?". It is my answer to "Why do I prefer Linux over Windows?" Anyway, here are my thoughts.

### Learning Resources

My journey with Linux began when I tried to set up [aleguy02.dev](https://aleguy02.dev), my VPS. I followed a series of tutorials and guides from DigitalOcean (my VPS provider) and quickly learned that Linux is the operating system of servers. Nearly every tutorial was Ubuntu-based. Whenever I Googled anything about SSH, Nginx, or whatever else, most of the answers were for Ubuntu. Since I'm interested in working with servers, I naturally gravitated toward Linux.

I also participated in a bunch of live streams for MLH's Cloud Week and those were very much Linux-based, so I got to listen to real human beings talk about it LIVE and interact with them. In other words, I've had a lot of help learning Linux. Windows... not so much. My experience with Windows involved a lot of head-scratching, excessive Googling, and thinking how much faster everything would be if I were just using Ubuntu.

Now let's talk about `man`. Man oh man, do I love `man`. Whenever I'm confused on a command, I can use `man [command]` to read about it without leaving the terminal, and boy is it detailed. Each flag is carefully documented and a synopsis of the command is given. Amazing. Windows, on the other hand, does not have `man` but it does have `HELP`. I wanted to make a direct comparison, so I did `man mkdir` and `HELP mkdir` in a WSL and Windows PowerShell, respectively. If you do this yourself and observe the output, my point should be illustrated pretty clearly.

![Output of `man mkdir`](/man_mkdir.png '`man mkdir` opens an extensive manual on how to use mkdir')
![Output of `HELP mkdir`](/HELP_mkdir.png '`HELP mkdir` opens a less-than-impressive manual on how to use mkdir')

### Installing Packages

It used to be the case that installing packages with Windows was just a sucky experience: you had to click through a bunch of menus to install one thing, and it was a pain. This paragraph was originally going to be me complaining about that, but while doing research for this post I found out that Windows has its own competent package managers, like Chocolatey or WinGet. I haven't used them yet, and I'm a little embarrassed to admit that I didn't know about them until now--but that's part of the learning process.

### Skill Issue?

The more I challenge myself and try to use Windows, the more I learn to appreciate its differences. My biggest gripe now is the lack of good resources (outside of Stack Overflow posts), but I know more practice and experience will get me there. My final takeaway: Ultimately, both are tools and it's up to their wielder to experiment and learn what they like and when to use what. Sure, Vim is awesome, but sometimes, I just want to use my mouse and use Notepad instead.

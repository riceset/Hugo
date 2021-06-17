---
title:  "How to set up the CS50 IDE locally (Mac)"
date:   2021-03-06
tags: [CS50]
---

In this article, I will explain how to install `check50`, `submit50` and `style50` on your Mac.
First, you will need to install pip if you don’t have it yet:
```bash
$ curl https://bootstrap.pypa.io/2.7/get-pip.py -o get-pip.py
$ python get-pip.py
```
Now, you can install all the tools with the following commands:
```bash
$ python3 -m pip install check50
$ python3 -m pip install submit50
$ python3 -m pip install style50
```
For `style50`, you will need to install libmagic and astyle to make it work properly:
```bash
$ brew install libmagic
$ brew install astyle
```
Now that you installed everything, restart your terminal and there you have it.

![CS50](https://user-images.githubusercontent.com/48802655/121961672-1772d480-cd3e-11eb-9b58-1d71c5a3ae70.png)

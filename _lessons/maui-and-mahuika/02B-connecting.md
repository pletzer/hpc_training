---
layout: post
title: Connecting to Maui and Mahuika
permalink: /lessons/maui-and-mahuika/connecting
chapter: maui-and-mahuika
---

## Objectives

You will learn:

* how to login to Maui and Mahuika

## Requirements

You will need a terminal program to login to Maui or Mahuika:

- Windows: [MobaXterm](https://mobaxterm.mobatek.net/), Windows 10 bash, or [Putty](https://www.putty.org/)
- MacOS X: Terminal app, iTerm2
- Linux: Terminal app, xterm

## Connecting to Maui and Mahuika

Connecting to Maui or Mahuika is a two step process. First, connect to NeSI's lander node by typing:
```
ssh -Y <myusername>@lander.nesi.org.nz
```
inside your terminal program, where `<myusername>` is your account user name. You will see the following prompt:
```
First Factor:
```
Enter your password, after which you will see:
```
Second Factor (optional):
```
Enter the 6-digit code from the mobile device. 

To compile code, submit jobs to the scheduler and access your data, you will need to connect to one of the Maui or Mahuika login nodes.

For Mahuika:
```
ssh -Y login.mahuika.nesi.org.nz
```

For Maui:
```
ssh -Y login.maui.nesi.org.nz
```

No password should be required to access the login nodes from the lander node.

## Jumping across the lander node (advanced)

On most Linux and MacOS machines the login process can be simplified to just a single SSH command, jumping across the lander node on the way to either the Maui or Mahuika login nodes. With the following lines in your `~/.ssh/config` file (replacing `myusername` with your username) you can run the command `ssh mahuika` on your machine and it will take you straight to Mahuika (`ssh maui` for Maui). 

```
Host mahuika
   User myusername
   Hostname login.mahuika.nesi.org.nz
   ProxyCommand ssh -W %h:%p lander
   ForwardX11 yes
   ForwardX11Trusted yes
   ServerAliveInterval 300
   ServerAliveCountMax 2

Host maui
   User myusername
   Hostname login.maui.nesi.org.nz
   ProxyCommand ssh -W %h:%p lander
   ForwardX11 yes
   ForwardX11Trusted yes
   ServerAliveInterval 300
   ServerAliveCountMax 2

Host lander
   User myusername
   HostName lander.nesi.org.nz
   ForwardX11 yes
   ForwardX11Trusted yes
   ServerAliveInterval 300
   ServerAliveCountMax 2
```

The `ForwardX11` directives will enable X11 forwarding and are optional. The `ServerAlive` directives will stop the connection from hanging when you don't type anything in for some time.

This can be combined with the `Control` directives to make subsequent SSH logins and SCP commands use the same connection (i.e. without needing to type a password in again). For example. to enable this for all connections add the following section to your `~/.ssh/config` file:
```
Host *
    ControlMaster auto
    ControlPath ~/.ssh/sockets/ssh_mux_%h_%p_%r
    ControlPersist 1
```
and be sure to run: `mkdir -p ~/.ssh/sockets`.
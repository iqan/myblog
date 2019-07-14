---
title: "Installing a .NET Core service on linux server"
date: "2019-05-01T00:00:00Z"
layout: post
draft: false
category: "Programming tutorial"
description: "A tutorial on how to install a backend service created using dotnet core in linux a server."
tags:
  - "linux"
  - "dotnet core"
  - "systemd"
  - "dotnet"
  - "dotnet core on linux"
---

In this article we will be creating a .NET Core service and host it in a linux machine. We will be using dotnet 2.2 and ubuntu as host machine. We will use systemd to install, run and manage service.

## Why should I care about .NET Core running on linux?

Since ages, companies have been making windows services for their backend jobs. The only problem is, it can be installed on windows server only, so if you are just starting or have a small company, you may not want to spend a fortune on windows servers. After .Net core was released, there are many good options to run backend services on variety of hosts and OS’s.

![Introduction](./1.png)

So let’s get started. If you already have a linux machine running you can ignore creating a host part and jump to creating a service.

## Setting up host machine

In this tutorial, we will be using an ubuntu 16.04 server hosted on Microsoft Azure. You can go through [https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm) on how to create a linux server on Azure OR you can run ubuntu server in local machine using Virtual Box.

First step is to install dotnet core on linux. So connect to your ubuntu server using ssh.

`ssh <username>@<ip-or-dns>`

Follow [https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-current](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-current) and install dotnet core.

Once done, you can verify installation:

`dotnet — version`

You should see your installed version.

## Create a demo service

There is another article I have written, you can refer it and create a simple .Net Core service.

[How to create a timely running service in .NET Core](/timely-running-dotnetcore-service)

Let’s assume you have created a service in home directory and this service can be run using

`dotnet /home/iqan/DummyService/bin/debug/DummyService.dll`

This path can be different so please use correct path to dll.

## Create a systemd service

We will use systemd to install/create a service and run our dotnet application. Systemd comes pre-installed in ubuntu but you may need to install it for different distros, like RHEL.

To create a service we need to create a configuration/service file which contains some information about service. It will have service name, base directory, pre-conditions, command to run, etc.

Let’s create a new service. Open terminal and type:

`sudo nano /lib/systemd/system/dummyservice.service`

Now write this:

```service

[Unit]
Description=Dummy Service in .NET
# Requires=xyz.service
# After=xyz.service

[Service]
Type=forking
WorkingDirectory=/user/iqan
ExecStart=dotnet DummyService/bin/debug/DummyService.dll

[Install]
WantedBy=multi-user.target

```

Once you are done with configuration. Click ctrl+x or cmd+x and save file. We have used minimum configurations here.

- **Description:** It sets description of service. This can be used to give more details about the service.

- **Requires:** If service has any dependency on other service like mongodb or sqlserver then we can specify here. In our dummy service we do not have any such dependency so it’s commented out.

- **After:** States that our service will only start after xyz service is up and running.

- **WorkingDirectory:** You can specify your own directory where application resides.

- **ExecStart:** Here, you specify the entrypoint or the command to run when service is started

You can learn more about syntax for systemd here: [https://www.freedesktop.org/software/systemd/man/systemd.syntax.html](https://www.freedesktop.org/software/systemd/man/systemd.syntax.html)

## Start service and verify

If above all steps are done then you will see a service created. To check if service is created, enter:

`sudo systemd status dymmyservice.service`

This will state service is inactive because we haven’t started it yet. So let’s start the service using:

`sudo systemd start dummyservice.service`

If you check status again then you will see service is loaded and running. Also you can see any logs if your dummy application is logging to console.

To stop service:

`sudo systemd stop dummyservice.service`

## Wrap up

In this article, we have seen how to install a dotnet core service in linux machine using systemd. You can try a ton of things to configure your service and also make interesting use-cases.

Thanks for reading! Have a great day ahead! :)

# Docker
Solves problems during Development and Deployment.  


problems solved :
1. Dependency version conflicts
2. Installation of dependency apps hardness across different OSes and ISAs (during development)
3. maintaining configs 

How :
1. ship the exact dependencies. No more dependency conflicts
2. ship the exact configs. No different configs across different systems.
3. maintaining configs is done in one place. And it comes pre-shipped. this reduces hardness in maintaining configs.
4. The image targets the docker runtime. The runtime abstracts away the OS. Meaning that the Apps you write need to call on general OS functions. If you use very OS-specific functions in your code, then you have to use custom tactics... eg tweaking the docker runtime that you are using OR using conditional OS compilation


Now A VM has its downsides :
1. The VM artifacts are huge. They contain an entire OS + a single App.
2. The VM artifacts have a slow startup_time. You have to boot the OS. And then start the APP within the OS.
3. The VM artifacts consume more computation power. You have to run 2 entire OSes + the target app.

But the VM has an advantage over Docker.    
VM images can run ontop of every host OS or bare-metal... provided that has a hypervisor is available. 
Docker images are not seamlessly universal across different operating systems. OR different ISAs when used on bare-metal scenarios.  
Docker only deals with the Application layer, it does not deal wit the kernel layer.

The thing is, Unlike the VMs, Docker artifacts do not package the target kernel of the App.  
So the Docker artifact is dependent on the target kernel.   
A docker image that was targeting the Linux kernel would not work atop the windows kernel directly.    

To solve this compatibility problem, people create adapters.    

A popular kind of adapter was introduced by Docker themselves : "Docker Desktop".   
THis adapter runs a lightweight linux kernel ontop of a hypervisor. So yea... you still import a version of the linux kernel.

The Container uses the hosts kernel. The container does not use the hosts userspace components. This is because the base kernel across different is the typically the same. The difference between the distros is in their user-space components. These components affect how an app gets installed, the default configs.

Some of the user-space components include :
1. The System libraries that the APP uses - Examples of these system libraries include : Graphics libraries, Database libraries, the C library. Even though system libraries are generic across linux distros, there are variations in versions. These variations can cause the app to not run as expected.  The graphics libraries of different versions may support different data formats, or they may have dropped a certain functionality that the App needs.

Distros use different system library versions. Sometimes changing the version of one may break an app that was dependent on the old version of the system library. To avoid this system version hell... just ship the specific system  libraries in the docker image

2. Command line utilities -  Linux distributions come with a wide range of command-line tools and utilities for managing and interacting with the system. THese utilities have version variations. To avoid hell, just package our specific utilities. Your app may need to run certain cmd scripts... your choice.


3. Configuration Files: Linux distributions have their own set of configuration files that define system-wide settings, services, and defaults. These files can be found in directories like /etc and include files such as /etc/hosts, /etc/fstab, /etc/resolv.conf, and many more.


4. File System Hierarchy : Linux distros try to follow an almost similar File System hireachy conventions. But there are slight variations across the distros. Some directories may have slightly different names or locations between distributions, but their purpose remains similar. This might be due to different package managers used or utilities. Eg if the init system is init, we have the directory : /etc/init.d instead of /etc/systemd/system.
The /lib and /lib64 directories store shared libraries, but some distributions may use /usr/lib or /usr/lib64 instead



## Docker image vs Docker Containers

A docker image is an executable umutable package containing :
1. Configuration files
2. Base image : Dependent OS-system libraries that are not part of the target base kernel.
3. Dependent Apps or crates (version specific)
4. The actual code.

A Docker container is a running instance of an image.   
From one image you can run mutiple containers


You can get an image from online or locally.    
You can specify the path of getting the image.  

## Port Binding
Processes need to communicate with other services.  
Two processes can communicate with each other by reading and writing on the same file.  
This file is called a port. 

A unix Port is a shared file between processes that are under the UNIX file system.  
A TCP port is a file that two servicesthat use the TCP/IP protocol use to communicate.  

There are well known ports that are reserved for specific well known services. Eg Port 80 is reserved for the HTTP service.  
The reserved ports range from (0-1023)

Now, the container is isolated. So when I run a container, by defaul it gets its own network domain. It gets its own ports that are isolated from the ports of the host OS.  

To be able to make the local browser access and communicate with the ports in the container, you need to bind the local ports (ie > 1023) to the ports of the container.
    port 1080 (local) ---> port 80 (in container)

You can know the port of a process using the "docker ps" command
You can bind using the publish command :
    docker run --detach --publish 1080:80 nginx:1.25.1\

It is better to use standard bindings eg bind port 80 (local) ---> port 80 (container)


## Naming
Each container has a unique ID.  
You use this ID in commands.    

You can give each container a unique name :  
- docker run --name web_server --detach --publish 80:80 nginx:1.25.1    

Now you can do a command like : docker stop web_server

## Dockerfile
THis is a file that contains the instructions of building a docker image. THink of it as a build script



## How is isolation achieved in Docker?

Containers need to get their own isolated resources : CPU, memory, IP address, file mount points, inner_processes

What is Linux Namespace mechanism? Why do we need it?   
What is PID namespace? Why do we need it?   
What is a mount Namespace? Why do we need it?   
Network manespace, UTS namespace, IPC Namespace, User Namespace:    

What are Linux Control Groups?  
Why do we need them? When do we need them?  

How do Linux namespaces and Linux control Groups associate with Docker?


Discuss the security of IOT in docker
How does a container work?



What are the disadantages of trying to use docker in IoT?
What are the security features of docker?





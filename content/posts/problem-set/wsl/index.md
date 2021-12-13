---
title: "WSL2(Windows Subsystem for Linux) Problem Set"
subtitle: ""
date: 2020-09-23T23:05:04+08:00
lastmod: 2020-09-23T23:05:04+08:00
draft: false
author: "Aya"
authorLink: ""

tags: ["Linux", "WSL"]
categories: ["Problem-Set"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "featured-image.webp"

toc:
  enable: true
  auto: false
math:
  enable: false
lightgallery: true
license: '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank">CC BY-NC 4.0</a>'
---

<!--more-->
## 1._make_ command execute correctly, but the site can't be reached for some reasons.（Solved）

Description: By using _telnet_ command, we can know certain port's status. As a result, the 1313 port of wsl is running by hugo, but that of windows is closed. Apparently, solve the disconnection between wsl and windows gonna handle this problem.

### ref：  
>[https://logi.im/script/achieving-access-to-files-and-resources-on-the-network-between-win10-and-wsl2.html](https://logi.im/script/achieving-access-to-files-and-resources-on-the-network-between-win10-and-wsl2.html)  
[https://www.cnblogs.com/hapjin/p/5367429.html](https://www.cnblogs.com/hapjin/p/5367429.html)

For programs listen to **0.0.0.0** in wsl2, win10 can be accessed directly through localhost:port. Therefore we have to set bind address equal to 0.0.0.0 on hugo

    hugo server --bind=0.0.0.0 --port=1313 --minify --theme book 

Reason: The default address of localhost is Loopback address **127.0.0.1**. It can only be accessed by local machine. So if we want to access wsl2 address from other consoles, we have to replace localhost in /etc/hosts.

By adding new rule to hugo Makefile, type `make win` to create local blog on wsl2 that can be accessed on other machines

    wsl win windows:  
    hugo server --bind=0.0.0.0 --port=1313 --minify --theme book  

## 2.Port depolyed at 0:0:0:0 can't be accessed from Windows. (Solved)

Description: Almost the same with **#problem 1**, but Spring is running at 0:0:0:0. Spring-boot's port can't be browsed through Windows browser. 

### ref:
>[https://github.com/microsoft/WSL/discussions/2471](https://github.com/microsoft/WSL/discussions/2471)

Using `wsl --shutdown` solved this problem, and we can also handle this by replacing localhost into the ip address of etho from **ip addr**(for some unknown reasons, the speed is way faster than using localhost)


## 3.The connection from Windows to MySQL in WSL2 failed(Solved)

Description: Connect to MySQL by setting ip as localhost(already set the bind_address=0.0.0.0), but it ended up failure.

### ref:
>[https://github.com/microsoft/WSL/issues/4150](https://github.com/microsoft/WSL/issues/4150)  
>[https://stackoverflow.com/questions/61002681/connecting-to-wsl2-server-via-local-network](https://stackoverflow.com/questions/61002681/connecting-to-wsl2-server-via-local-network)

It seems like Ubuntu host is virtually connected to windows host, and it's netted by the Windows computer. To change that, we gonna forward the wsl2 port to windows, by using

    netsh interface portproxy add v4tov4 listenport=<port-to-listen> listenaddress=0.0.0.0 connectport=<port-to-forward> connectaddress=<forward-to-this-IP-address>

Since then, there's another problem as the ip address of wsl2 is dynamic, it will change as you reboot. To improve this method, we should run a script every time we start our computer that does:
1. Get Ip Address of WSL2 machine
2. Remove previous port forwarding rules
3. Add port Forwarding rules
4. Remove previously added firewall rules
5. Add new Firewall Rules

```Script
        $remoteport = bash.exe -c "ifconfig eth0 | grep 'inet '"
        $found = $remoteport -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';

        if( $found ){
        $remoteport = $matches[0];
        } else{
        echo "The Script Exited, the ip address of WSL 2 cannot be found";
        exit;
        }

        #[Ports]

        #All the ports you want to forward separated by coma
        $ports=@(80,443,10000,3000,5000);


        #[Static ip]
        #You can change the addr to your ip config to listen to a specific address
        $addr='0.0.0.0';
        $ports_a = $ports -join ",";


        #Remove Firewall Exception Rules
        iex "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

        #adding Exception Rules for inbound and outbound Rules
        iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
        iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

        for( $i = 0; $i -lt $ports.length; $i++ ){
        $port = $ports[$i];
        iex "netsh interface portproxy delete v4tov4 listenport=$port listenaddress=$addr";
        iex "netsh interface portproxy add v4tov4 listenport=$port listenaddress=$addr connectport=$port connectaddress=$remoteport";
        }
```
The script must run under highest privilege. Use task scheduler to automatically start powershell and add argument as `-ExecutionPolicy Bypass c:\scripts\wslbridge.ps1`.

{{< admonition type=tip >}}
  Navicat should change the ip to 127.0.0.1 instead of localhost.
{{< /admonition>}}
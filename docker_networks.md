# Index
- [Intro](#intro)
- [Bridge Network](#bridge-network)
- [The User defined Bridge](#the-user-defined-bridge)
- [The Host Network](#the-host-network)
- [The MACVLAN Network](#the-macvlan-network)
- [The IPVLAN Network](#the-ipvlan-network)
- [The Overlay Network](#the-overlay-network)
- [The None Network](#the-none-network)

## Intro
Docker contains 7 type of networks

## Bridge Network
-> Default network is bridge
-> docker0 is default virtual bridge interface. It is default network for Bridge. If you have Docker installed you will see when you run ip address show

```docker network ls``` -> to list out networks
if you see driver in this command, it is network type

### Example
I have make three container 
```
docker run -itd --rm --name thor busybox
docker run -itd --rm --name mjolnir busybox
docker run -itd --rm --name stormbreaker nginx
```

Now, if you do ```ip address show``` you can find there ip addresses in list or <br/>
try ```bridge link``` for only show three ip addresses <br/>
For more detaile ```docker inspect bridge``` <br/>

Now, I will go in thor ```docker exec -it thor sh``` <br/>
and do ```ip addr show``` <br/>
and you see ip address. You can ping another container from here <br/>

You cannot access this things by it's ip address and port in browser. <br/>
You have to expose port for it. <br/>

So, default is bridge network.

## The User-defined Bridge
-> just means you are defining it or making it.
 
 ```docker network create asgard``` -> makes new network asgard. In ```ip addrress show``` you can see that <br/>
 ```docker nerwork ls``` -> you will see bridge name <br/>

 ### Example

1. let's create a network asgard
```docker network create asgard```

2. Let's add 2 containers in it
```docker run -itd --rm --network asgard --name loki busybox```
```docker run -itd --rm --network asgard --name loki_2 busybox```

3. You can ispect asgard
```docker inspect asgard```

#### Why create own network instead of default bridge
1. It provides isolation
2. Our first network is bridge and user define bridge network is asgard -> they cannot communicate with each other <br/>
So, if you go to the terminal of thor and try to ping loki through it's ip, it will not work (100% packet or data loss) <br/>
You can ping containers in that network. <br/>
From the shell of loki, you can ping loki_2 but not the container of default bridge network like thor, mjolnir and stormbreaker <br/>
<br/>
One more advantage of User defined bridge <br/>
1. In Default bridge you can ping other container in same network using ip but not name. <br />
2. In User define bridge you can ping other container in same network using name. <br/>

## The HOST Network
-> It is weird network. But pretty ossowmmmmm <br/>
When you deploy container in host network, it has not have it's own network.<br/>
You don't have to expose any port<br/>
You can access it directly from the host.<br/>

-> It runs on host as any other app, but downside is it has no isolation.

## The MACVLAN Network
-> simply connect docker container to our physical network. <br/>
-> It is like directly connecting switch of house. (Physical Router or Switch) <br/>
-> They have their own mac and ip , acting like vm<br/>

-> To create a network
```
docker network create -d macvlan \
--subnet home_network_subnet\
> --gateway router_home_network\
> -o parent=enp0s3 (tie macvlan to host interface)\ 
> newasgard
```
here, -o for option and newasgard is network name

### Example

1. Let's create containers
```
docker run -itd --rm --network newasgard --ip ip_addr --name new_thor busybox
```
Now, new_thor connected like vm ,br/>

Doing ```ip address show```, It will show macvlan but ping default gateway which will also be of container connected in macvlan but not ping anything. Downside<br/>

Docker container get own mac address. Network may not have multiple mac address. (port may not handle it. It says one to two only allowed) <br/>

```sudo up link set enp0s3(network interface) promisc on``` -> promiscuos on

see it work ? -> if not work, change down each network device.

use vm tool to set promiscuos Allow all. Now, it will work.

-> I have not practiced it, just watched a video

#### Two mode of macvlan
1. Bridge Mode
2. 802.1q Mode

## The IPVLAN Network
modes -> L2 (Default), L3 <br/>

container 1 -> mac is host mac but have different IP address<br/>
container 2 -> mac is host mac but have different IP address<br/>

Solves macvlan probleam <br/>

To create a network
```
docker network create -d ipvlan \ 
--subnet subnet\
--gateway gateway\
> -o parent=enp0s3 \
> newasgard 
```
Here, mode is ipvlan L2 <br/>

To create a container
```
docker run -itd --rm --network newasgard --ip ip_addr --name new_thor busybox
```

### L3
We are connecting containers to host, as host is router. <br/>
(It is good in networking) <br/>
No idea how to reach even cannot connect to host, internet, etc... <br/>
Crazy isolation <br/>

#### Note
```
Router IP -> 192.168.94.0
host ip ->10.7.1.232
```
You know how to reach, you tell router to go to host ip <br/>

To create a network
```
docker network create -d ipvlan \ 
--subnet subnet\
> -o parent=enp0s3 -o ipvlan_mode=l3\
> --subnet subnet_2\
> newasgard 
```
We don't have to specify gateway <br/>
container can talk to each other subnet container<br/>

## The Overlay Network
In production and cloud multiple host running multiple containers.<br/>
For communication, networking is crazy, their comes overlay<br/>
Something Docker swarm <br/>

## The None Network
none network is already there, if you ```docker network ls``` <br/>
-> Create a container
```docker run -itd --rm --network none --name gorr busybox``` <br/>
```ip address show``` give nothing to show.

## ref
video -> https://www.youtube.com/watch?v=bKFMS5C4CG0
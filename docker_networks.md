# Index
- [Intro](#intro)
- [Bridge Network](#bridge-network)

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

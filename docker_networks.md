# Index
- [Intro](#into)
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

So, default is bridge network.


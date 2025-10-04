```
docker swarm init --advertise-addr <mgr-ip>
docker swarm join-token worker
```

containers running in a service called "tasks"

```
docker service create --replicas=5 --name web --publish published=8080,target=80,mode=ingress nginx
docker service scale web=1
```

```
docker service create --name web --mode global -dt nginx
docker service create --name web --mode global --publish published=8080,target=80,mode=ingress -d nginx
```

```
docker node update --availability drain  swarm03
docker node update --availability active  swarm03
```
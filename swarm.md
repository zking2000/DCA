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

```
docker stack deploy -c uptime-kuma-stack.yml kuma

# uptime-kuma-stack.yml
version: "3.9"

volumes:
  kuma-data:

networks:
  kuma-net:
    driver: overlay

services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    networks: [kuma-net]
    volumes:
      - kuma-data:/app/data
    ports:
      - target: 3001
        published: 3001
        mode: ingress            # 从任意节点IP:3001 访问UI
    deploy:
      replicas: 1                # 建议单副本；数据在本地卷
      placement:
        constraints:
          - node.role == manager
      restart_policy:
        condition: on-failure
      update_config:
        order: start-first
```
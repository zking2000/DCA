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

```
docker swarm update --autolock=true

docker swarm unlock
```

```
docker service create --name myservice --mount type=volume,source=myvolume,target=/mypath nginx

docker volume ls
```

```
docker node update --label-add region=blr <node name>

```

```
Manager Node 
1. maintain cluster size
2. scheduling services
3. serving swarm mode HTTP API endpoints


经验法则：容错 = ⌊(N-1)/2⌋。超过 7 个 manager 通常得不偿失（开销、选举和心跳成本上升）。

⸻

在你当前 4 台的拓扑上怎么做
	•	选择 3 台提升为 manager，跨故障域分布（不同主机/机架/可用区）。
	•	保留 1 台作为 worker。

# 查看节点
docker node ls

# 将 2 台 worker 提升为 manager
docker node promote <node-2> <node-3>

# 可选：让 manager 不跑业务任务（只管控）
docker node update --availability drain <manager-1>
docker node update --availability drain <manager-2>
docker node update --availability drain <manager-3>

# 或者用约束让业务只跑在 worker
# 在部署 stack/service 时：
# --constraint 'node.role==worker'


docker swarm update --autolock=true
docker swarm unlock-key        # 查看/轮换密钥


# 1) 在任意一个仍为 manager 的节点上查看状态
docker node ls

# 2) 降级指定的 manager（在“另一个”manager 上执行）
docker node demote <manager-node-name>

# 3) 验证
docker node ls


# 这会让该节点离开 swarm（如果它还是 manager，需要 --force）
docker swarm leave --force
```


```
docker swarm join-token manager
docker swarm join-token worker
```
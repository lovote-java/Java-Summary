

* docker 安装 Redis

```shell
docker run --restart=always --log-opt max-size=100m --log-opt max-file=2 -p 6379:6379 --name myredis -v /home/redis/myredis/myredis.conf:/etc/redis/redis.conf -v /home/redis/myredis/data:/data -d redis redis-server /etc/redis/redis.conf  --appendonly yes  --requirepass 000415
```

* 容器内部连接进行测试

```shell
docker exec -it myredis redis-cli
```



## Redis 常用命令


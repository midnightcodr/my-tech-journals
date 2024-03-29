```
       +----+
       | M1 |
       | S1 |
       +----+
          |
+----+    |    +----+
| R2 |----+----| R3 |
| S2 |         | S3 |
+----+         +----+

Configuration: quorum = 2


M1: 192.168.1.196:6379
S1: 192.168.1.196:26379

R2: 192.168.1.197:6379
S2: 192.168.1.197:26379

R3: 192.168.1.198:6379
S3: 192.168.1.198:26379
```


##  Steps

1. on each node
```
sudo apt update && sudo apt upgrade
sudo apt install -y build-essential tcl pkg-config
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz
cd redis-stable
make
make test
sudo make install

sudo mkdir /etc/redis
sudo mkdir /var/lib/redis

```

2. on node 1
```
sudo sh -c 'cat << EOF > /etc/redis/redis.conf
protected-mode no
logfile "/var/lib/redis/redis.log"
EOF'

```

3. on node 2 & 3
```
sudo sh -c 'cat << EOF > /etc/redis/redis.conf
protected-mode no
slaveof 192.168.1.196 6379
logfile "/var/lib/redis/redis.log"
EOF'
```

4. on node 1 - 3
```
sudo sh -c 'cat << EOF > /etc/redis/sentinel.conf
protected-mode no
sentinel deny-scripts-reconfig yes
sentinel monitor mymaster 192.168.1.196 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
logfile "/var/lib/redis/sentinel.log"
EOF'
```

5. start redis servers on all nodes
```
sudo redis-server /etc/redis/redis.conf &

```
6. start sentinel servers on all nodes
```
sudo redis-sentinel /etc/redis/sentinel.conf &
```

7. write a test key in the master node (1)
```
redis-cli set hello world
```

8. verify failover
```
# on node 2 & 3, run
watch redis-cli info replication

# on node 1, run
redis-cli debug sleep 15
```
If failover is working, either node 2 or should should change from `slave` into `master` role after a few seconds.

9. use a nodejs program to verify failover is working
```javascript
const Redis = require('ioredis')
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

const redis = new Redis({
  sentinels: [
    { host: '192.168.1.196', port: 26379 },
    { host: '192.168.1.197', port: 26379 },
    { host: '192.168.1.198', port: 26379 }
  ],
  name: 'mymaster'
})

const getValue = () => redis.get('hello')

const run = async () => {
  for (var i = 0; i < 1000; i++) {
    const res = await getValue()
    console.log(`${new Date()}: got ${res}`)
    await sleep(2000)
  }
}

run()
```
To test the failover, use the same `redis-cli debug sleep 15` on the master node.

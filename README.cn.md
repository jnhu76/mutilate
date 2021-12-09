# MUTILATE 使用方法

## 编译

```bash
$ scons
scons: Reading SConscript files ...
Checking whether the C++ compiler works... yes
Checking for gengetopt...

...
g++ -o mutilate mutilate.o cmdline.o log.o distributions.o util.o Connection.o Protocol.o Generator.o -L/opt/local/lib -levent -lpthread -lrt -lzmq
scons: done building targets.
```

之后就会生成文件 `gtest` 和 `mutilate`。

## 测试

```bash
mutilate3 0.1

用法：mutilate -s server[:port] [options] (选项)

"高性能的 "memcached基准测试工具

  -h, --help 打印帮助并退出
      --版本 打印版本并退出
  -v, --verbose 精确度。重复以获得更详细的信息。
      --quiet 禁用日志信息。

基本选项。
  -s, --server=STRING Memcached服务器主机名[:端口]。 重复以 
                                  指定多个服务器。
      --binary 使用二进制的memcached协议而不是ASCII。
  -q, --qps=INT 目标聚合QPS。0=峰值QPS。 
                                  (默认=`0')
  -t, --time=INT 运行的最大时间（秒）。 (默认=`5')
  -K, --keysize=STRING memcached键的长度（分布）。 
                                  (default=`30')
  -V, --valuesize=STRING memcached值的长度（分布）。 
                                  (default=`200')
  -r, --records=INT 要使用的memcached记录的数量。 如果 
                                  多个memcached服务器，这个 
                                  数目除以服务器的数量。 
                                  (default=`10000')
  -u, --update=FLOAT 设置：获取命令的比率。 (默认=`0.0')

高级选项。
  -U, --username=STRING 用于SASL认证的用户名。
  -P, --password=STRING 用于SASL认证的密码。
  -T, --threads=INT 产生的线程数量。 (默认=`1')
      --affinity 设置线程的CPU亲和力，轮流使用
  -c, --connections=INT 每个服务器要建立的连接。 
                                  (default=`1')
  -d, --depth=INT 对请求进行管道化的最大深度。 
                                  (default=`1')
  -R, --roundrobin 以轮流方式将线程分配给服务器。
                                  方式分配线程。 默认情况下，每个线程连接到 
                                  每一个服务器。
  -i, --iadist=STRING 抵达间分布（分布）。 
                                  注意：该分布将自动被调整为 
                                  以匹配由--qps给出的QPS。 
                                  (默认=`指数')
  -S, --skip 如果之前的请求晚了，就跳过传输。
                                  迟。 这将损害长期的QPS平均值。
                                  但可以减少长延迟请求后的QPS峰值。
                                  请求。
      --moderate 执行请求之间的最小延迟~1/lambda。
                                  请求之间的最小延迟。
      --noload 跳过数据库加载。
      --loadonly 加载数据库，然后退出。
  -B, --locking 使用阻塞式epoll()。 可能会增加延迟。
      --no_nodelay 不要使用TCP_NODELAY。
  -w, --warmup=INT 开始测量前的预热时间。
  -W, --wait=INT 启动后等待开始测量的时间。
                                  测量。
      --save=STRING 将延迟样本记录到给定文件。
      --search=N:X 搜索N阶统计量<的QPS。
                                  Xus。 (即 --search 95:1000 意味着找到 
                                  95%的请求都快于 
                                  1000us）。
      --scan=min:max:step 从最小到最大扫描整个QPS速率的延迟。

代理模式选项。
  -A, --agentmode 以代理模式运行客户端。
  -a, --agent=host 启用远程代理。
  -p, --agent_port=STRING 代理端口。 (默认=`5556')
  -l, --lambda_mul=INT Lambda乘数。 增加该客户的QPS份额。
                                  此客户的QPS份额。 (默认=`1')
  -C, --measure_connections=INT 每个服务器的主客户端连接，覆盖了 
                                  --connections.
  -Q, --measure_qps=INT 明确设置主客户端的QPS，分布在线程和连接中。
                                  线程和连接。
  -D, --measure_depth=INT 设置主客户端连接深度。

--measure_*选项可以帮助测量memcached服务器的延迟，而不至于产生巨大的损失。
选项有助于对memcached服务器的延迟进行测量，而不会产生明显的客户端排队
延迟。 --measure_connections允许主站覆盖
--connections选项。 --measure_depth允许主站以 "开环 "客户端的方式运行。
开环 "客户端，而其他代理继续作为常规的
闭环客户。 --measure_qps可以让你调节主站查询的QPS，不受其他客户端影响。
独立于其他客户端查询的QPS。 这在理论上
理论上，这可以使你在很大的 --qps 值范围内期望看到的基线排队延迟正常化。
的范围内看到的基准排队延迟。

有些选项将 "分布 "作为参数。
分布是由<distribution>[:<param1>[,...]]指定。
参数不是必需的。 支持以下分布。

   [fixed:]<value> 始终生成<value>。
   uniform:<max> 0到<max>之间的均匀分布。
   normal:<mean>,<sd> 正态分布。
   exponential:<lambda> 指数分布。
   pareto:<loc>,<scale>,<shape> 广义帕累托分布。
   gev:<loc>,<scale>,<shape> 广义极值分布。

   为了重新创建来自[1]的Facebook "ETC "请求流，以下是硬编码分布
   还提供了以下硬编码分布。

   fb_value = 一个硬编码的价值大小的离散和GPareto PDF
   fb_key = "gev:30.7984,8.20449,0.078688", 密钥大小分布
   fb_ia = "pareto:0.0,16.0292,0.154971", 抵达时间间隔分布。

[1] Berk Atikoglu等人，大规模键值存储的工作负载分析。
    sigmetrics 2012

```

例如，使用facebook etc:

```bash
./mutilate -s localhost -t 4 -c 8 -i "pareto:0.0,16.0292,0.154971" -K "gev:30.7984,8.20449,0.078688" -V "gev:30.7984,8.20449,0.078688"
#type       avg     std     min     5th    10th    90th    95th    99th
read       95.2    13.3    45.3    76.9    80.9   111.8   118.5   139.3
update      0.0     0.0     0.0     0.0     0.0     0.0     0.0     0.0
op_q        1.0     0.0     1.0     1.0     1.0     1.1     1.1     1.1

Total QPS = 83951.1 (335807 / 4.0s)

Misses = 0 (0.0%)
Skipped TXs = 0 (0.0%)

RX   29506205 bytes :    7.0 MB/s
TX   14148331 bytes :    3.4 MB/s

```

论文测试：

| Experiment | Program | Keys | Values | Items | PUTs | Clients | Threads | Conns | Pipeline | IR Dist |
| -- | -- | -- |  -- |  -- |  -- |  -- |  -- |  -- |  -- |  -- | 
| Realistic | Mutilate | ETC | ETC |  1M | .03 | 20 + 1 | 16+8 | 1280+8 | 1+1 | GPareto |
| Skew | Memtier | 30B | 200B | 8M | 0 | 1 | 16 | 512 | 100 | Poisson | 
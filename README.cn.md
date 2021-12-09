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

### Get host PIDs of processes running inside a docker container

```shell
$ docker inspect --format '{{.State.Pid}}' 7da5c0015966
59735
$ pstree -p 59735
init(59735)───node(59775)─┬─{V8 WorkerThread}(59781)
                          ├─{V8 WorkerThread}(59782)
                          ├─{V8 WorkerThread}(59783)
                          ├─{V8 WorkerThread}(59784)
                          ├─{node}(59780)
                          ├─{node}(59849)
                          ├─{node}(59850)
                          ├─{node}(59851)
                          ├─{node}(59852)
                          ├─{node}(59853)
                          ├─{node}(59854)
                          └─{node}(59855)
```

### Install custom built linux-tools

```
$ wget [url].deb
$ dpkg -i [file].deb
$ apt install -f
$ apt install linux-tools-4.4.0-91-generic binutils libaudit1 libc6 libdw1 libelf1 liblzma5 libpci3 libslang2 libudev1 libunwind8 zlib1g
```
### Capture flamegraph of a containerized Node.js app from the host

Make sure Node.js was started with the `--perf-basic-prof` flag.

```shell
$ docker inspect --format '{{.State.Pid}}' [container id]
59735
$ pstree -p 59735
... (see above - take note of Node's PID)
$ perf record -F 99 -p 59735 -g -- sleep 30
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.314 MB perf.data (567 samples) ]
$ docker exec -it [container id] pgrep -n 'node'
312
$ # Map the JIT map for the PID inside the container to the host's PID so perf can find it
$ docker exec -it [container id] cat /tmp/perf-312.map > /tmp/perf-[node pid].map
$ perf script > nodestacks
$ sed -i '/\[unknown\]/d' nodestacks
$ git clone --depth 1 http://github.com/brendangregg/FlameGraph
$ cd FlameGraph
$ ./stackcollapse-perf.pl < ../nodestacks | ./flamegraph.pl --colors js > ./node-flamegraph.svg
```

### Download a file from the server

```shell
$ python3 -m http.server 8080
```

Now simply use wget to pull down your file :-)

### Find a container on a host

Let's say you know the name of a container is `foobar` and you know its host is `100.100.100.100`

```shell
$ ssh [user]@100.100.100.100
-> docker ps --filter name=foobar
CONTAINER ID        IMAGE          COMMAND             CREATED             STATUS              PORTS
7da5c0015966        foobar         ./foobar            1 day ago           up 1 day
```

### Find package that provides a binary

```shell
$ dpkg -S $(which [command])
```

### Get all errors from restify server

For a server using restify and bunyan and logging to disk

```shell
cat *.log | bunyan -c 'this.err && this.err.name && this.res && this.res.statusCode' -o json | jq -cM '{ err: .err.name, res: .res.statusCode }' | sort | uniq -c | sort -n
```

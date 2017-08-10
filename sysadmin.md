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

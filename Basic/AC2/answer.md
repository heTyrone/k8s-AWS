简述 kubectl logs / describe / apply / delete 命令的功能

Kubectl是一个用于操作kubernetes集群的命令行接口,通过利用kubectl的各种命令可以实现各种功能,是在使用kubernetes中非常常用的工具。

kubectl logs
============================================================
输出pod中一个容器的日志。如果pod只包含一个容器则可以省略容器名。
kubectl logs [-f] [-p] POD [-c CONTAINER]
  -c, --container="": 容器名。
  
  -f, --follow[=false]: 指定是否持续输出日志。
      --interactive[=true]: 如果为true，当需要时提示用户进行输入。默认为true。
      --limit-bytes=0: 输出日志的最大字节数。默认无限制。
      
  -p, --previous[=false]: 如果为true，输出pod中曾经运行过，但目前已终止的容器的日志。
      --since=0: 仅返回相对时间范围，如5s、2m或3h，之内的日志。默认返回所有日志。只能同时使用since和since-time中的一种。
      --since-time="": 仅返回指定时间（RFC3339格式）之后的日志。默认返回所有日志。只能同时使用since和since-time中的一种。
      --tail=-1: 要显示的最新的日志条数。默认为-1，显示所有的日志。
      --timestamps[=false]: 在日志中包含时间戳。

例子
--------------------------
$kubectl logs nginx
nginx为pod名，看来这个pod里就仅有一个容器。命令返回pod nginx内的那个容器的日志快照

$kubectl logs -p -c ruby xxx
ruby 为pod名，xxx为容器名，输出xxx的日志快照

$kubectl logs -f -c ruby xxx
持续输出xxx的日志

$kubectl logs --tail=20 nginx xxx
输出容器xxx最近20条日志

$kubectl logs --since=1h nginx xxx
输出容器xxx中最近1小时内产生的所有日志

kubectl describe
============================================================
输出指定的一个/多个资源的详细信息。
  -f, --filename=[]: 用来指定待描述资源的文件名，目录名或者URL。
  -l, --selector="": 用于过滤资源的Label。

kubectl describe (-f FILENAME | TYPE [NAME_PREFIX | -l label] | TYPE/NAME)


例子
--------------------------
$kubectl describe nodes kubernetes-minion-emt8.c.myproject.internal
描述一个node
ps：Node是Pod真正运行的主机，可以物理机，也可以是虚拟机。为了管理Pod，每个Node节点上至少要运行container runtime（比如docker或者rkt）、kubelet和kube-proxy服务。

$ kubectl describe pods/nginx
描述一个pod

$ kubectl describe pods
描述所有的pod

$ kubectl describe po -l name=myLabel
描述所有包含label name 为 mylabel的pod





apply
======================================
通过文件名或控制台输入，对资源进行配置。
接受JSON和YAML格式的描述文件。
kubectl apply -f FILENAME

  -f, --filename=[]: 包含配置信息的文件名，目录名或者URL。
  -o, --output="": 输出格式，使用“-o name”来输出简短格式（资源类型/资源名）。
      --schema-cache-dir="/tmp/kubectl.schema": 如果不为空，将API schema缓存为指定文件，默认缓存到“/tmp/kubectl.schema”。
      --validate[=true]: 如果为true，在发送到服务端前先使用schema来验证输入。

例子
----------------------------

$ kubectl apply -f ./pod.json
将pod.json文件中的配置应用到pod

# 将控制台输入的JSON配置应用到Pod
$ cat pod.json | kubectl apply -f -
将控制台输入的json配置应用到pod


delete
=====================================
通过文件名、控制台输入、资源名或者label selector删除资源。 接受JSON和YAML格式的描述文件。
只能指定以下参数类型中的一种：文件名、资源类型和名称、资源类型和label selector。 注意：delete命令不检查资源版本，如果有人在你进行删除操作的同时进行更新操作，他所做的更新将随资源同时被删除。

kubectl delete ([-f FILENAME] | TYPE [(NAME | -l label | --all)])

      --all[=false]: 使用[-all]选择所有指定的资源。
      --cascade[=true]: 如果为true，级联删除指定资源所管理的其他资源（例如：被replication controller管理的所有pod）。默认为true。
  -f, --filename=[]: 用以指定待删除资源的文件名，目录名或者URL。
      --grace-period=-1: 安全删除资源前等待的秒数。如果为负值则忽略该选项。
      --ignore-not-found[=false]: 当待删除资源未找到时，也认为删除成功。如果设置了--all选项，则默认为true。
  -o, --output="": 输出格式，使用“-o name”来输出简短格式（资源类型/资源名）。
  -l, --selector="": 用于过滤资源的Label。
      --timeout=0: 删除资源的超时设置，0表示根据待删除资源的大小由系统决定。

例子
-------------------------------------
$ kubectl delete -f ./pod.json
通过pod.json文件中制定的资源类型和名称删除一个pod

$ cat pod.json | kubectl delete -f -
通过控制台输入的JSON所制定的资源类型和名称删除一个pod

$ kubectl delete pod,service baz foo
删除名为baz和foo的pod和service

$ kubectl delete pods,services -l name=myLabel
删除所有label name 为 mylabel的的pod和service

$ kubectl delete pod 1234-56-7890-234234-456456
删除Id为1234-56-7890-234234-456456的pod

$ kubectl delete pods --all
删除所有的pod
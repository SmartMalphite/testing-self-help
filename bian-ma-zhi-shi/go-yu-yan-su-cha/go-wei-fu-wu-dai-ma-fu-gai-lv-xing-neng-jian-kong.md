# Go微服务代码覆盖率&性能监控

### Go微服务性能监控

#### 代码注入

在代码主函数中注入以下内容

```go
func main() {
    flag.Parse()

    //cpu profile收集
    cpuProfile, _ := os.Create("cpu_profile")
    pprof.StartCPUProfile(cpuProfile)
    defer pprof.StopCPUProfile()

    //mem信息收集
    memProfile, _ := os.Create("mem_profile")
    pprof.WriteHeapProfile(memProfile)

    //业务代码
    dir, _ := log.InitLog(log.DebugLvl)
    log.Infof("log dir:%s", dir)

    err := config.InitConfig(*configFile)
    if err != nil {
        panic(err)
    }
    .......

    //信号收集，kill -SIGINT pid 可以正常结束程序
    sc := make(chan os.Signal, 1)
    signal.Notify(sc,
        syscall.SIGINT ,
        syscall.SIGTERM,
        syscall.SIGQUIT)

    sig := <-sc
    log.Infof("Server Got signal [%d] to exit.", sig)
    return
```

#### 数据查看与可视化

web指令可以生成svg图片，便于分析

```text
➜  cmd git:(dev) ✗ go tool pprof mem_profile
Type: inuse_space
Time: Jul 28, 2019 at 3:44pm (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top
Showing nodes accounting for 1126kB, 100% of 1126kB total
      flat  flat%   sum%        cum   cum%
  613.99kB 54.53% 54.53%   613.99kB 54.53%  github.com/valyala/fasthttp/stackless.NewFunc
  512.02kB 45.47%   100%   512.02kB 45.47%  github.com/valyala/fasthttp.newCompressWriterPoolMap
         0     0%   100%   512.02kB 45.47%  github.com/valyala/fasthttp.init.ializers
         0     0%   100%   613.99kB 54.53%  github.com/valyala/fasthttp/stackless.init.ializers
         0     0%   100%     1126kB   100%  runtime.main
(pprof) web


➜  cmd git:(dev) ✗ go tool pprof cpu_profile
Type: cpu
Time: Jul 28, 2019 at 3:44pm (CST)
Duration: 6.71s, Total samples = 20ms (  0.3%)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top 5
Showing nodes accounting for 20ms, 100% of 20ms total
Showing top 5 nodes out of 12
      flat  flat%   sum%        cum   cum%
      10ms 50.00% 50.00%       10ms 50.00%  runtime.pthread_cond_wait
      10ms 50.00%   100%       10ms 50.00%  runtime.usleep
         0     0%   100%       10ms 50.00%  runtime.findrunnable
         0     0%   100%       10ms 50.00%  runtime.mcall
         0     0%   100%       10ms 50.00%  runtime.mstart
(pprof) web
```

![](../../.gitbook/assets/image%20%282%29.png)

### Go微服务代码覆盖率收集

以下代码与main函数所在文件一起编译，命名为\*\_test.go

```text
package main
// This file is mandatory as otherwise the filebeat.test binary is not generated correctly.
import (
    "testing"
    "flag"
)
var systemTest *bool
func init() {
    systemTest = flag.Bool("systemTest", false, "Set to true when running system tests")
}
// Test started when the test binary is started. Only calls main.
func TestSystem(t *testing.T) {
    if *systemTest {
        main()
    }
}
```

```text
文件编译
go test -c -mod=vendor -covermode=count -coverpkg . -o risk_service.test

文件运行
nohup ./risk_service.test -systemTest -test.coverprofile coverage.cov --conf=../conf/vn/dev/risk_service.toml --zoneconf=../conf/timezone.json  -geoconf=../conf/GeoLite2-City.mmdb > ./log/nohup.log 2>&1 &

退出程序
kill -SIGINT `ps -e | grep risk | grep -v grep | awk '{print $1}'`
```


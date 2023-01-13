# 基于Node Exporter监控指标设置的告警规则
配置文件： [中文版直接下载](www.baidu.com) &nbsp;&nbsp;&nbsp;&nbsp;[英文版直接下载](www.baidu.com) &nbsp;&nbsp;&nbsp;&nbsp; [访问Gitee](https://gitee.com/aniseed/prometheus-alerts) &nbsp;&nbsp;&nbsp;&nbsp; [访问GitHub](www.baidu.com)

```
wget xxxxx
```
```En
wget xxxx
```

#### 节点内存使用率高  
**描述**  
节点内存使用率过高 

**指标**   

- "up": 用于判断Prometheus中配置的监控目标（Target）是否在线。   
  
**告警规则**   

```
    - alert: TargetDown
      expr: up == 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: 监控目标（Target）丢失(实例:{{ $labels.instance }})
        description: "监控目标丢失"

```
```En
    - alert: TargetDown
      expr: up == 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Target down (Instance:{{ $labels.instance }})
        description: "Target Down"
```
> 注： 此规则作用于prometheus中配置的所有的监控目标(target)，不仅仅是prometheus自身。

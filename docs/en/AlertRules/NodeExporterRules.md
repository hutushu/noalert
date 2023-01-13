# NodeExporter alert rules

Configuration file： &nbsp;&nbsp;&nbsp;&nbsp;[Download](www.baidu.com)&nbsp;&nbsp;&nbsp;&nbsp; [GitHub](www.baidu.com) &nbsp;&nbsp;&nbsp;&nbsp; [Gitee](https://gitee.com/aniseed/prometheus-alerts) 

```
wget xxxx
```

#### NodeContextSwitchHigh  
**Description**   
Average rate of context switching per core.

**Metric**   

- "node_context_switches_total": Total number of context switches.   
  
**Alert rules**   

```En
    - alert: NodeContextSwitchingHigh
      expr: rate(node_context_switches_total[5m])/count without(mode,cpu) (node_cpu_seconds_total{mode="idle"}) > 1000
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: node context switching high (Instance:{{ $labels.instance }})
        description: "Node context switching rate is {{ $labels.value }} within 5 minutes"
```
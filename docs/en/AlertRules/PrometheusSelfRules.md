# 基于prometheus自身的告警指标设置的告警规则
配置文件： &nbsp;&nbsp;&nbsp;&nbsp;[Download](www.baidu.com)&nbsp;&nbsp;&nbsp;&nbsp; [GitHub](www.baidu.com) &nbsp;&nbsp;&nbsp;&nbsp; [Gitee](https://gitee.com/aniseed/prometheus-alerts) 
```
wget xxxxx
```
```En
wget xxxx
```

#### TargetDown  
**描述**  
Target无法连接时产生告警 

**指标**   

- "up": 用于判断Prometheus中配置的Target是否在线。   
  
**告警规则**   

```
    - alert: TargetDown
      expr: up == 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Target不在线(Instance:{{ $labels.instance }})
        description: "Target 不在线"

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
> 注： 此规则作用于所有的target，不仅仅是prometheus自身。

#### PrometheusConfigurationReloadFailure
**描述**  
Prometheus具有热加载配置文件的功能，无需重启prometheus服务。当配置文件重新加载失败时产生告警。 
> 注： 从 Prometheus2.0 开始，热加载功能是默认关闭的，如需开启，需要在启动 Prometheus 的时候，添加 --web.enable-lifecycle 参数。

**指标**   

- "prometheus_config_last_reload_successful": 判断Prometheus的配置文件重新加载是否成功。 
  
**告警规则**   

```
    - alert: PrometheusConfigurationReloadFailure
      expr: prometheus_config_last_reload_successful != 1
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus配置文件重新加载失败（Instance:{{ $labels.instance }}）
        description: "Prometheus的配置文件重新加载失败"

```   
```En
    - alert: PrometheusConfigurationReloadFailure
      expr: prometheus_config_last_reload_successful != 1
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus configuration reload failure (instance {{ $labels.instance }})
        description: "Prometheus configuration reload failure"
```
#### PrometheusNotConnectedToAlertmanager
**描述**  
没有配置AlertManager，或配置的AlertManager服务未运行，导致Prometheus无法连接到AlertManager服务。

**指标**   
- "prometheus_notifications_alertmanagers_discovered": 发现的正常运行的AlertManger的数量 。
  
**告警规则**   

```
    - alert: PrometheusNotConnectedToAlertmanager
      expr: prometheus_notifications_alertmanagers_discovered < 1
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: Prometheus无法连接到AlertManager (Instance:{{ $labels.instance }})
        description: "Prometheus无法连接到运行的AlertManager服务超过5分钟"
``` 
```En
    - alert: PrometheusNotConnectedToAlertmanager
      expr: prometheus_notifications_alertmanagers_discovered < 1
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: Prometheus not connected to alertmanager (instance {{ $labels.instance }})
        description: "Prometheus cannot connect a active alertmanager more than 5 minutes"
```
> 注：若Prometheus无需连接AlertManager，请删除此条告警规则。

#### PrometheusNotificationsDropped
**描述**  
由Prometheus发送给AlertManager的告警通知，由于错误被丢弃。比如AlertManager服务异常，导致无可用的AlertManager可接收告警通知。

**指标**   

- "prometheus_notifications_dropped_total": 发送到Alertmanager的由于错误而丢弃的警报总数。 
  
**告警规则**   

```
    - alert: PrometheusNotificationsDropped
      expr: increase(prometheus_notifications_dropped_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus发送的告警通知被丢弃（Instance:{{ $labels.instance }}）
        description: "5分钟内Prometheus丢弃了大约{{  $value }}个告警通知"
```   
```En
    - alert: PrometheusNotificationsDropped
      expr: increase(prometheus_notifications_dropped_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus notifications dropped（Instance:{{ $labels.instance }}）
        description: "Prometheus dropped about {{  $value }}  notifications within 5 minutes"
```   
#### PrometheusNotificationsError
**描述**  
由Prometheus向AlertManager发送告警通知发生错误。

**指标**   

- "prometheus_notifications_errors_total": 由Prometheus向AlertManager发送告警通知的错误总数。 
  
**告警规则**   

```
    - alert: PrometheusNotificationsError
      expr: increase(prometheus_notifications_errors_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus发送的告警通产生错误（Instance:{{ $labels.instance }}）
        description: "5分钟内Prometheus出现大约{{  $value }}次发送告警通知错误"
``` 
```En
    - alert: PrometheusNotificationsError
      expr: increase(prometheus_notifications_errors_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus notifications error（Instance:{{ $labels.instance }}）
        description: "Prometheus has about {{ $value }} times of sending alert notification error within 5 minutes"
``` 

#### PrometheusNotificationsBacklog
**描述**  
由Prometheus向AlertManager发送告警通知出现积压。

**指标**   

- "prometheus_notifications_queue_capacity": 告警通知的队列容量。
  
**告警规则**   

```
    - alert: PrometheusNotificationsBacklog
      expr: min_over_time(prometheus_notifications_queue_length[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus告警通知积压(Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus积压了{{ $value }}条告警通知"
``` 
```En
    - alert: PrometheusNotificationsBacklog
      expr: min_over_time(prometheus_notifications_queue_length[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus notifications backlog (Instance:{{ $labels.instance }})
        description: "Prometheus has a backlog of about {{ $value }}  notifications within 5 minutes"
``` 
#### PrometheusNotReady
**描述**  
Promehteus是否启动完成，服务是否运行正常。

**指标**   

- "prometheus_ready": Prometheus运行正常的指标
  
**告警规则**   

```
    - alert: PrometheusNotReady
      expr: prometheus_ready != 1
      for: m
      labels:
        severity: warning
      annotations:
        summary: Prometheus服务运行不正常(Instance:{{ $labels.instance }})
        description: "Prometheus服务运行不正常"
``` 
```En
    - alert: PrometheusNotReady
      expr: prometheus_ready != 1
      for: m
      labels:
        severity: warning
      annotations:
        summary: Prometheus not ready (Instance:{{ $labels.instance }})
        description: "Prometheus not ready"
``` 
#### PrometheusRuleEvaluationFailed
**描述**  
Promehteus的告警规则评估失败。

**指标**   

- "prometheus_rule_evaluation_failures_total": Prometheus告警规则评估失败的总数
  
**告警规则**   

```
    - alert: PrometheusRuleEvaluationFailed
      expr: increase(prometheus_rule_evaluation_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus告警规则评估失败(Instance:{{ $labels.instance }})
        description: "5分钟内告警规则评估失败大约{{ $value }}次"
``` 
```En
    - alert: PrometheusRuleEvaluationFailed
      expr: increase(prometheus_rule_evaluation_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus rule evaluation failed (Instance:{{ $labels.instance }})
        description: "Prometheus rule evaluation failed about {{ $value }} times within 5 minutes"
``` 
#### PrometheusTemplateTextExpansionFailed
**描述**  
Prometheus模板文件扩展失败

**指标**   

- "prometheus_template_text_expansion_failures_total": Prometheus模板文件扩展失败的数量
  
**告警规则**   

```
    - alert: PrometheusTemplateTextExpansionFailed
      expr: increase(prometheus_template_text_expansion_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus模板文件扩展失败 (Instance:{{ $labels.instance }})
        description: "5分钟内模板文件扩展失败大约{{ $value }}次"
``` 
```En
    - alert: PrometheusTemplateTextExpansionFailed
      expr: increase(prometheus_rule_evaluation_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus template text expansion failed (Instance:{{ $labels.instance }})
        description: "Prometheus template text expansion failed about {{ $value }} times within 5 minutes"
```   

#### PrometheusRuleGroupEvaluationSlow
**描述**  
Prometheus规则组的评估持续时间比预定的时间长，它表示存储后端访问较慢或规则设计太复杂。

**指标**   

- "prometheus_rule_group_last_duration_seconds": Prometheus规则组评估花费的时间   
- "prometheus_rule_group_interval_seconds": 规则组的间隔时间，即15秒启动一次规则组评估
  
**告警规则**   

```
    - alert: PrometheusRuleGroupEvaluationSlow
      expr: prometheus_rule_group_last_duration_seconds > prometheus_rule_group_interval_seconds
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus规则组评估慢 (Instance:{{ $labels.instance }})
        description: "规则组{{ $labels.rule_group }}评估持续时间太长"
``` 
```En
    - alert: PrometheusRuleGroupEvaluationSlow
      expr: prometheus_rule_group_last_duration_seconds > prometheus_rule_group_interval_seconds
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus rule group evaluation slow (Instance:{{ $labels.instance }})
        description: "The evaluation time of rule group {{ $value }} is too long  "
```   

#### PrometheusScrapesSamplesRejected
**描述**  
由于时间戳重复但是值不同的异常样本Prometheus拒绝。

**指标**   

- "prometheus_target_scrapes_sample_duplicate_timestamp_total": 由于时间戳重复但值不同而拒绝的样本总数。 

  
**告警规则**   

```
    - alert: PrometheusScrapesSamplesRejected
      expr: increase(prometheus_target_scrapes_sample_duplicate_timestamp_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus异常样本被拒绝 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus拒绝的异常样本数量大约为{{ $value }}"
``` 
```En
    - alert: PrometheusScrapesSamplesRejected
      expr: increase(prometheus_target_scrapes_sample_duplicate_timestamp_total[5m]) > 0
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Prometheus scrapes samples regected (Instance:{{ $labels.instance }})
        description: " The number of samples rejected is about {{ $value }} within 5 minutes"
```  

#### PrometheusTsdbCheckpointCreationsFailed
**描述**  
Prometheus后端存储TSDB创建检查点失败

**指标**   

- "prometheus_tsdb_checkpoint_creations_failed_total": Prometheus后端存储TSDB创建检查点失败的总数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbCheckpointCreationsFailed
      expr: increase(prometheus_tsdb_checkpoint_creations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 创建检查点失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus出现大约{{ $value }}次创建检查点失败"
``` 
```En
    - alert: PrometheusTsdbCheckpointCreationsFailed
      expr: increase(prometheus_tsdb_checkpoint_creations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb checkpoint creations failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} checkpoint creations failure within 5 minutes"
```   

#### PrometheusTsdbCheckpointDeletionsFailed
**描述**  
Prometheus后端存储TSDB删除检查点失败

**指标**   

- "prometheus_tsdb_checkpoint_deletions_failed_total": Prometheus后端存储TSDB删除检查点失败的总数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbCheckpointDeletionsFailed
      expr: increase(prometheus_tsdb_checkpoint_deletions_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 删除检查点失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus出现大约{{ $value }}次删除检查点失败"
``` 
```En
    - alert: PrometheusTsdbCheckpointDeletionsFailed
      expr: increase(prometheus_tsdb_checkpoint_deletions_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb checkpoint deletions failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} checkpoint deletions failure within 5 minutes"
```   

#### PrometheusTsdbCompactionsFailed
**描述**  
Prometheus后端存储TSDB数据压缩失败

**指标**   

- "prometheus_tsdb_compactions_failed_total": Prometheus后端存储TSDB数据压缩失败的总数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbCompactionsFailed
      expr: increase(prometheus_tsdb_compactions_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 数据压缩失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus出现大约{{ $value }}次删数据压缩失败"
``` 
```En
    - alert: PrometheusTsdbCompactionsFailed
      expr: increase(prometheus_tsdb_compactions_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb compactions failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} compactions failure within 5 minutes"
```   

#### PrometheusTsdbHeadTruncationsFailed
**描述**  
Prometheus后端存储TSDB头部数据块删减失败

**指标**   

- "prometheus_tsdb_head_truncations_failed_total": Prometheus后端存储TSDB头部数据块删减失败的总数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbHeadTruncationsFailed
      expr: increase(prometheus_tsdb_head_truncations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 头部数据删减失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus出现大约{{ $value }}次头部数据块删减失败"
``` 
```En
    - alert: PrometheusTsdbHeadTruncationsFailed
      expr: increase(prometheus_tsdb_head_truncations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb head truncations failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} head truncations failure within 5 minutes"
```   

#### PrometheusTsdbReloadsFailed
**描述**  
Prometheus后端存储TSDB从磁盘重新加载数据失败

**指标**   

- "prometheus_tsdb_reloads_failures_total": Prometheus后端存储TSDB从磁盘重新加载数据失败的次数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbReloadsFailed
      expr: increase(prometheus_tsdb_reloads_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 数据重加载失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus tsdb出现大约{{ $value }}次数据重加载失败"
``` 
```En
    - alert: PrometheusTsdbReloadsFailed
      expr: increase(prometheus_tsdb_reloads_failures_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb reloads  failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} reloads failure within 5 minutes"
```   
#### PrometheusTsdbWalTruncationsFailed
**描述**  
Prometheus后端存储TSDB写入数据WAL删减失败

**指标**   

- "prometheus_tsdb_wal_truncations_failed_total": Prometheus后端存储TSDB写入日志WAL的删减失败次数。 

  
**告警规则**   

```
    - alert: PrometheusTsdbWalTruncationsFailed
      expr: increase(prometheus_tsdb_wal_truncations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb 写入日志WAL删减失败 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus tsdb出现大约{{ $value }}次写入日志WAL删减失败"
``` 
```En
    - alert: PrometheusTsdbWalTruncationsFailed
      expr: increase(prometheus_tsdb_wal_truncations_failed_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb wal truncations failed (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} wal truncations failure within 5 minutes"
```   
#### PrometheusTsdbWalCorruptions
**描述**  
Prometheus后端存储TSDB的WAL损坏

**指标**   

- "prometheus_tsdb_wal_corruptions_total": Prometheus后端存储TSDB的WAL损坏数量。 

  
**告警规则**   

```
    - alert: PrometheusTsdbWalCorruptions
      expr: increase(prometheus_tsdb_wal_corruptions_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb的WAL损坏 (Instance:{{ $labels.instance }})
        description: "5分钟内Prometheus tsdb出现大约{{ $value }}次wal损坏"
``` 
```En
    - alert: PrometheusTsdbWalCorruptions
      expr: increase(prometheus_tsdb_wal_corruptions_total[5m]) > 0
      for: 0m
      labels:
        severity: critical
      annotations:
        summary: Prometheus tsdb wal corruptions (Instance:{{ $labels.instance }})
        description: "Prometheus tsdb has {{ $value }} wal corruptions within 5 minutes"
```  


----
prometheus_target_scrape_pools_failed_total: 失败的抓取池总数
prometheus_target_scrape_pools_failed_total: 失败的scrape循环重新加载的总数
prometheus_tsdb_wal_truncations_failed_total: 失败的WAL删减总数
prometheus_tsdb_wal_corruptions_total: WAL损坏的总数
### 从进程词典队列弹出条目

- 接口描述

```
POST /proc_queue_pop HTTP/1.1

{"key":"[键]"}
```

- 可能的返回

```json
{"code":0,"message":"操作成功","key":"[键]","value":"[值]"}
```

- 说明
    - 从进程词典队列中弹出一个值，如果队列为空，则会取出空字符串
    - [键] 标识队列，用于从队列中提取值

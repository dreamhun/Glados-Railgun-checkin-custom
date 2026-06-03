项目克隆自 https://github.com/Devilstore/Glados-Railgun-checkin ， 2026-06-03

项目原自述文件见 `README_source.md` 。





# 代码记录

## 积分自动兑换



在 [checkin.py#L456-L472](file:///G:/%5Cdev_project%5CGlados-Railgun-checkin-master%5Ccheckin.py#L456-L472) 的 _checkin_on_domain 方法中，兑换是在**每次签到后无条件尝试执行**的

不需要，已被注释掉。



## 推送

不设置sendkey 就不会推送

```
根据 checkin.py#L405-L425 的 PushService.send 方法和 main 函数末尾的调用逻辑：
### 执行条件
唯一条件 ：环境变量 PUSHDEER_SENDKEY 已设置且非空。
```


项目克隆自 https://github.com/Devilstore/Glados-Railgun-checkin ， 2026-06-03

项目原自述文件见 `README_source.md` 。





# 域名设置

在 [checkin.py#L112](file:///g:\dev_project\Glados-Railgun-checkin-master\checkin.py#L112)，`Config` 类中定义：

```python
"""默认域名"""
DOMAINS = ["glados.cloud", "railgun.info"]
```

实际请求时，在 [checkin.py#L193](file:///g:\dev_project\Glados-Railgun-checkin-master\checkin.py#L193) 拼接为完整 URL：

```python
def _get_full_url(self, path: str) -> str:
    return f"https://{self.domain}{path}"
```

所以最终访问的地址类似：
- `https://glados.cloud/api/user/checkin`
- `https://railgun.info/api/user/checkin`



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





# Actions 执行触发机制

根据 [gladosCheck.yml](file:///g:\dev_project\Glados-Railgun-checkin-master\.github\workflows\gladosCheck.yml) 的配置：

### 触发方式（3 种）

| 触发方式 | 配置                       | 说明                                       |
| ---- | ------------------------ | ---------------------------------------- |
| 定时触发 | `cron: '0 4,10 * * *'`   | 每天 UTC 04:00 和 10:00（北京时间 12:00 和 18:00） |
| 代码推送 | `push: branches: [main]` | 推送到 main 分支时触发（忽略 README 和 imgs 变更）      |
| 手动触发 | `workflow_dispatch`      | 在 GitHub 仓库 Actions 页面手动点击运行             |

### 执行步骤

```
1. Checkout Code          ← 拉取仓库代码
2. Set up Python 3.13     ← 配置 Python 环境
3. Install dependencies   ← 安装 requests 和 pypushdeer
4. Running checkin        ← 执行 python checkin.py，注入 4 个 secrets 作为环境变量
5. Keep alive             ← 保持工作流活跃
6. Delete workflow runs   ← 清理 7 天前的成功运行记录（保留最近 7 条）
```

### Secrets 注入

第 4 步执行签到脚本时，通过 `env` 将仓库 Secrets 注入为环境变量：

```yaml
env:
  PUSHDEER_SENDKEY: ${{ secrets.PUSHDEER_SENDKEY }}
  GLADOS_COOKIES: ${{ secrets.GLADOS_COOKIES }}
  GLADOS_EXCHANGE_PLAN: ${{ secrets.GLADOS_EXCHANGE_PLAN }}
  GLADOS_VERBOSE: ${{ secrets.GLADOS_VERBOSE }}
```

`checkin.py` 中的 `Config` 类通过 `os.environ.get()` 读取这些值。



# 为什么要 star 仓库

README 中要求 star 自己 fork 的仓库，是为了**保持仓库活跃，防止 GitHub 自动禁用定时工作流**。

GitHub 有一个政策：**如果仓库 60 天内没有任何活动，`schedule` 类型的定时工作流会被自动禁用**。Star 自己的仓库是最简单的"活跃信号"，让 GitHub 认为仓库仍在使用中，从而保证每天定时签到不会中断。

其他能起到同样效果的操作还有 push 代码、创建 issue 等，但 star 是最简单无副作用的方式。
# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

交接待办超过有效期后，接收人仍可确认成功，返回记录中的处理时间却是交接创建时间附近。请先不要修改代码，定位过期判断使用了什么时间以及它如何绕过有效期，并给出证据。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/coldchain-custody-task-10
- 仓库地址：https://github.com/zhanglei10281852-gif/coldchain-custody-task-10.git
- parent SHA：8bb099967d3b45c0ff820659ffa9d1d9574c2f90

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/coldchain-custody-task-10.git bug-repro
cd bug-repro
git checkout --detach 8bb099967d3b45c0ff820659ffa9d1d9574c2f90
go test ./internal/service -run "^TestExpiredHandoffCannotBeAccepted$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestExpiredHandoffCannotBeAccepted$" -count=1
--- FAIL: TestExpiredHandoffCannotBeAccepted (0.56s)
    annotation_behavior_test.go:123: expired handoff error = <nil>
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	0.568s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestExpiredHandoffCannotBeAccepted$" -count=1
--- FAIL: TestExpiredHandoffCannotBeAccepted (1.30s)
    annotation_behavior_test.go:123: expired handoff error = <nil>
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	1.525s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据。 完成时目标仓库代码、测试和配置零改动。

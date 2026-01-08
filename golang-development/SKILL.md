---
name: golang-best-practices
description: 提供 Golang 项目开发的最佳实践指导,包括代码结构、错误处理、并发模式等
---

# Golang 最佳实践技能

当用户需要 Golang 相关帮助时,遵循以下指南:

## 代码结构
- 使用标准项目布局 (cmd/, internal/, pkg/, api/)
- 遵循 Go 命名约定(驼峰命名)
- 每个包有清晰的职责

## 错误处理
- 总是检查错误返回值
- 使用 `errors. Is()` 和 `errors.As()` 进行错误判断
- 自定义错误类型时实现 `error` 接口

## 并发模式
- 优先使用 channels 进行 goroutine 通信
- 使用 context 进行超时和取消控制
- 避免共享内存,通过通信共享

## 测试
- 使用表驱动测试(table-driven tests)
- 测试文件命名为 `_test.go`
- 使用 `testify` 库增强断言能力

## 依赖管理
- 使用 Go Modules (`go. mod`)
- 定期运行 `go mod tidy`
- 使用 `go mod vendor` 进行依赖锁定

## 示例

提供可运行的代码示例,包含完整的错误处理和注释。
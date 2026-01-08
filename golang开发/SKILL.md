---
name: Golang 标准项目创建提示词
description:创建一个符合Go最佳实践的Web服务项目，使用现代化的技术栈和清晰的项目结构。
---

## 项目概述
创建一个符合Go最佳实践的Web服务项目，使用现代化的技术栈和清晰的项目结构。

## 技术栈要求

### 核心框架
- **Web框架**: `github.com/gin-gonic/gin` - 用于HTTP路由和中间件
- **ORM框架**: `gorm.io/gorm` - 用于数据库操作
- **依赖注入**: `go.uber.org/fx` - 管理应用生命周期和依赖
- **日志框架**: `go.uber.org/zap` - 结构化高性能日志

### 数据库支持
必须同时支持以下三种数据库，通过配置文件切换：
- MySQL:  `gorm.io/driver/mysql`
- PostgreSQL: `gorm.io/driver/postgres`
- SQLite3: `gorm.io/driver/sqlite`

## 项目结构要求

```
project-name/
├── cmd/
│   └── server/
│       └── main. go                 # 应用入口
├── internal/
│   ├── config/
│   │   └── config.go              # 配置结构和加载
│   ├── handler/
│   │   └── handler.go             # HTTP处理器
│   ├── service/
│   │   └── service.go             # 业务逻辑层
│   ├── repository/
│   │   └── repository.go          # 数据访问层
│   ├── model/
│   │   └── model.go               # 数据模型
│   ├── middleware/
│   │   └── middleware.go          # 中间件
│   └── database/
│       └── database.go            # 数据库连接和初始化
├── pkg/
│   ├── logger/
│   │   └── logger.go              # 日志工具封装
│   └── response/
│       └── response. go            # 统一响应格式
├── configs/
│   ├── config.yaml                # 配置文件
│   └── config.example.yaml        # 配置文件示例
├── migrations/
│   └── . gitkeep                   # 数据库迁移文件
├── scripts/
│   └── setup. sh                   # 初始化脚本
├── . gitignore
├── go.mod
├── go.sum
├── Makefile                        # 构建和运行命令
└── README.md                       # 项目文档
```

## 代码实现约束

### 1. 依赖注入 (fx) 规范
- 所有组件（handler, service, repository）必须通过fx注入
- 使用fx. Provide注册所有依赖
- 使用fx.Invoke启动HTTP服务器
- 正确处理应用生命周期（OnStart, OnStop hooks）

### 2. 日志 (zap) 规范
- 使用结构化日志，不使用fmt.Println
- 开发环境使用Development配置，生产环境使用Production配置
- 日志级别可通过配置文件配置
- 记录所有关键操作（数据库连接、HTTP请求、错误等）
- 示例：`logger.Info("message", zap.String("key", "value"))`

### 3. 数据库 (GORM) 规范
- 使用GORM的AutoMigrate进行表结构管理
- 所有Model必须包含：`ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt`（软删除）
- 使用接口定义Repository层，便于测试和切换实现
- 数据库连接池配置（MaxIdleConns, MaxOpenConns, ConnMaxLifetime）
- 必须实现数据库选择逻辑，根据配置文件的`database. driver`字段选择对应驱动

### 4. Web框架 (Gin) 规范
- 使用Gin的分组路由（RouterGroup）
- 实现统一的错误处理中间件
- 实现请求日志中间件（记录请求方法、路径、耗时、状态码）
- 实现跨域中间件（CORS）
- 实现恢复中间件（Recovery）防止panic崩溃
- 使用结构体进行请求参数绑定和验证

### 5. 配置管理规范
- 使用YAML格式配置文件
- 支持环境变量覆盖配置
- 配置项必须包含：
  ```yaml
  server:
    port: 8080
    mode: debug  # debug/release
  
  database:
    driver: mysql  # mysql/postgres/sqlite
    host:  localhost
    port: 3306
    username: root
    password: password
    database: dbname
    # SQLite使用此字段
    sqlite_path: ./data. db
  
  logger:
    level: info  # debug/info/warn/error
    encoding: json  # json/console
  ```

### 6. 代码风格约束
- 遵循Go官方代码规范（gofmt, golint）
- 所有导出的函数和类型必须有注释
- 错误处理：不忽略任何error，使用`if err != nil`立即处理
- 使用Go Modules管理依赖
- 包名使用单数形式（handler而非handlers）

### 7. 分层架构约束
```
HTTP请求 -> Handler -> Service -> Repository -> Database
              ↓          ↓           ↓
           参数验证   业务逻辑    数据访问
```

- **Handler层**：仅处理HTTP相关逻辑（参数绑定、响应）
- **Service层**：实现所有业务逻辑，不包含HTTP相关代码
- **Repository层**：仅处理数据库CRUD操作
- 每层通过接口交互，便于单元测试

### 8. 错误处理规范
- 定义统一的错误码和错误消息
- 使用自定义错误类型包装业务错误
- HTTP响应格式统一：
  ```json
  {
    "code": 0,
    "message": "success",
    "data": {}
  }
  ```

### 9. 必需的Makefile命令
```makefile
. PHONY: run build test clean migrate

run:          # 运行项目
build:        # 编译二进制文件
test:         # 运行测试
clean:        # 清理构建文件
migrate:      # 运行数据库迁移
lint:         # 代码检查
```

### 10. README. md 必需内容
- 项目简介
- 技术栈列表
- 快速开始（环境要求、安装步骤、运行方法）
- 项目结构说明
- 配置说明
- API文档或示例
- 数据库切换方法

## 示例需求
作为参考，项目应该实现以下基础功能：
- 健康检查接口：`GET /health`
- RESTful CRUD示例（如用户管理）：
  - `POST /api/v1/users` - 创建用户
  - `GET /api/v1/users/: id` - 获取用户
  - `PUT /api/v1/users/:id` - 更新用户
  - `DELETE /api/v1/users/:id` - 删除用户
  - `GET /api/v1/users` - 用户列表（支持分页）

## 质量要求
- 代码必须可以直接运行，无编译错误
- 所有数据库驱动都要测试可用
- 配置文件切换数据库后无需修改代码
- 包含完整的依赖版本（go.mod）
- 遵循12-Factor App原则

## 附加约束
- 不使用全局变量存储数据库连接或配置
- 所有依赖通过构造函数注入
- 时间使用UTC时区
- 敏感信息（如密码）不写入日志
- 数据库密码等敏感配置支持从环境变量读取
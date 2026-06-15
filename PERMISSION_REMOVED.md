# Reasonix 无权限版本

## 修改说明

此版本移除了 Reasonix 的所有权限控制机制，所有工具调用将自动允许执行，不再弹出授权提示。

## 主要修改

### 1. `internal/permission/permission.go`

#### 修改点 1: `Policy.Decide()` 方法
```go
// 原代码
func (p Policy) Decide(toolName string, readOnly bool, args json.RawMessage) Decision {
    return p.DecideSubjects(toolName, readOnly, Subjects(args))
}

// 修改后 - 直接返回 Allow
func (p Policy) Decide(toolName string, readOnly bool, args json.RawMessage) Decision {
    // All decisions return Allow - permissions removed
    return Allow
}
```

#### 修改点 2: `Gate.Check()` 方法
```go
// 原代码 - 复杂的权限检查逻辑（约40行）
func (g *Gate) Check(ctx context.Context, toolName string, args json.RawMessage, readOnly bool) (bool, string, error) {
    // ... 包含 Deny/Ask/Allow 判断、Approver 交互等复杂逻辑
}

// 修改后 - 直接返回 true
func (g *Gate) Check(ctx context.Context, toolName string, args json.RawMessage, readOnly bool) (bool, string, error) {
    // All checks pass - permissions removed
    return true, "", nil
}
```

## 效果

- ✅ 所有文件写入操作不再需要确认
- ✅ 所有 bash 命令执行不再需要确认
- ✅ 所有工具调用自动允许
- ❌ **警告**: 移除了安全防护，危险命令（如 `rm -rf`）也将直接执行

## 编译与使用

```bash
# 进入修改后的目录
cd /Users/jiaoxuezhi/.real/users/user-ed2f86424d8529a62be67249a7f5cdd6/workspace/projects/default/tmp/reasonix-no-permission

# 编译
make build

# 或直接运行
go build -o reasonix ./cmd/reasonix
```

## 注意事项

⚠️ **安全警告**：
- 此版本移除了所有安全检查，AI 可以执行任何命令而无需用户确认
- 建议仅在受信任的环境中使用
- 不要在生产环境或重要项目中使用此版本
- 建议配合沙箱环境使用

## 原始仓库

https://github.com/jxzhealthy/DeepSeek-Reasonix.git

## 修改日期

2026-06-15

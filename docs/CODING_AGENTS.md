# Coding Agent 使用指南

## 已安装的Agent

| Agent | 版本 | 路径 | 用途 |
|-------|------|------|------|
| Claude Code | 2.1.42 | `/opt/homebrew/bin/claude` | 架构设计、代码审查、复杂任务 |
| Codex CLI | 0.93.0 | `/opt/homebrew/bin/codex` | 代码实现、功能开发 |

---

## 快速开始

### 1. 架构师模式 (Claude Code)

**适用场景**: 系统架构设计、技术选型、代码审查

```bash
# 进入项目目录
cd ~/Projects/ai-dev-team

# 启动Claude Code交互模式
claude

# 或执行单条命令
claude "分析当前项目结构并给出改进建议"
```

### 2. 程序员模式 (Codex CLI)

**适用场景**: 功能实现、Bug修复、重构

```bash
# 快速执行任务（自动批准）
codex exec --full-auto "实现用户登录功能"

# 谨慎模式（需要确认）
codex exec "修复内存泄漏问题"

# YOLO模式（无沙箱，最快但危险）
codex --yolo "重构API模块"
```

---

## PM调用方式

作为PM，我可以通过以下方式调用Coding Agents：

### 方式1: 后台运行（推荐用于长时间任务）

```bash
# 启动架构设计任务
bash pty:true workdir:~/Projects/ai-dev-team background:true command:"claude '设计一个微服务架构，包含用户服务和订单服务'"

# 启动开发任务
bash pty:true workdir:~/Projects/ai-dev-team background:true command:"codex exec --full-auto '实现用户注册API'"
```

### 方式2: 同步执行（短时间任务）

```bash
# 快速代码审查
bash pty:true workdir:~/Projects/ai-dev-team command:"claude 'Review最近的提交，检查代码质量'"
```

### 方式3: GitHub Issue驱动

1. PM创建Issue，分配给对应Agent
2. Agent checkout分支并工作
3. Agent提交PR
4. PM/Code Reviewer验收
5. 合并关闭Issue

---

## 工作流示例

### 场景: 实现新功能 "用户认证"

**Step 1: PM创建任务**
```bash
gh issue create \
  --title "[Feature] 实现用户认证系统" \
  --body "## 需求\n- 用户注册\n- 用户登录\n- JWT Token管理\n\n## 验收标准\n- [ ] 注册接口\n- [ ] 登录接口\n- [ ] Token刷新\n- [ ] 单元测试覆盖率>80%" \
  --label "architecture" \
  --assignee "@claude-code-opus"
```

**Step 2: 架构师设计**
```bash
# Claude Code进行架构设计
bash pty:true workdir:~/Projects/ai-dev-team background:true command:"claude '
阅读Issue #5的需求，完成以下任务：
1. 设计认证系统的架构
2. 编写ADR文档保存到 docs/adr/005-auth-architecture.md
3. 定义API接口规范保存到 api/auth.yaml
4. 创建子任务Issue分配给 @codex-developer
'"
```

**Step 3: 程序员实现**
```bash
# Codex实现功能
bash pty:true workdir:~/Projects/ai-dev-team background:true command:"codex exec --full-auto '
根据docs/adr/005-auth-architecture.md和api/auth.yaml的实现要求：
1. 实现用户注册API
2. 实现用户登录API
3. 实现JWT Token管理
4. 编写单元测试
5. 提交PR并关联Issue #5
'"
```

**Step 4: PM验收**
```bash
# 检查CI状态
gh pr checks <pr-number>

# 查看代码变更
gh pr diff <pr-number>

# 合并PR
gh pr merge <pr-number> --squash

# 关闭Issue
gh issue close <issue-number> --comment "已完成并部署"
```

---

## 最佳实践

### 1. 任务粒度

| Agent | 适合的任务时长 | 任务复杂度 |
|-------|--------------|-----------|
| Claude Code | 30min - 2h | 高（架构、设计） |
| Codex | 10min - 1h | 中（实现、修复） |

### 2. Prompt编写技巧

**好的Prompt:**
- ✅ 具体明确: "实现用户登录API，使用bcrypt加密密码"
- ✅ 有上下文: "参考docs/adr/001-architecture.md的设计"
- ✅ 有验收标准: "确保单元测试覆盖率>80%"

**避免的Prompt:**
- ❌ 太模糊: "做个用户系统"
- ❌ 太大: "重写整个项目"
- ❌ 无边界: "随便改改"

### 3. 安全注意事项

- `--yolo` 模式无沙箱保护，谨慎使用
- 敏感操作（删除数据、修改配置）需要人工确认
- 生产环境部署必须人工审核

### 4. 监控与反馈

```bash
# 查看运行的Agent
process action:list

# 查看Agent输出
process action:log sessionId:xxx

# 发送输入给Agent
process action:submit sessionId:xxx data:"确认继续"

# 终止Agent
process action:kill sessionId:xxx
```

---

## 故障排除

### 问题: Agent无法启动
**解决**: 检查是否在git仓库内，或运行 `git init`

### 问题: Agent卡住不动
**解决**: 
1. 检查是否等待输入: `process action:log sessionId:xxx`
2. 发送确认: `process action:submit sessionId:xxx data:"y"`
3. 或强制终止: `process action:kill sessionId:xxx`

### 问题: 权限不足
**解决**: 检查文件权限，必要时使用 `sudo` 或调整目录权限

---

## 集成GitHub Actions

可以在CI中调用Coding Agents:

```yaml
# .github/workflows/agent-review.yml
name: AI Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Claude Code Review
        run: |
          claude "Review this PR的代码变更，给出改进建议"
```

---

*Version: 1.0*
*Last Updated: 2025-07*

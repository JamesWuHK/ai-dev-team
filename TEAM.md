# AI 开发团队成员

## 团队架构

```
Project: AI Development Team
Owner: James Wu (爸爸)
Repository: https://github.com/JamesWuHK/ai-dev-team
```

## 成员角色

### 👔 Project Manager (PM)
- **Name**: JamesRobot (儿子)
- **GitHub**: @JamesWuHK (使用此账号操作)
- **职责**:
  - 需求澄清与任务拆解
  - 工作分配与进度跟踪
  - 质量验收与风险管控
  - 向上汇报与资源协调

### 🏗️ Architect (架构师)
- **Name**: Claude Code Opus
- **GitHub**: @claude-code-opus (待创建)
- **职责**:
  - 系统架构设计
  - 技术选型与方案评估
  - 代码审查与技术指导
  - 技术债务管理

### 💻 Developer (程序员)
- **Name**: Codex 5.3
- **GitHub**: @codex-developer (待创建)
- **职责**:
  - 功能开发与实现
  - 单元测试编写
  - Bug修复与优化
  - 文档编写

## 工作流程

### Issue 标签体系
| 标签 | 用途 | 分配给 |
|------|------|--------|
| `architecture` | 架构设计任务 | @claude-code-opus |
| `development` | 功能开发任务 | @codex-developer |
| `pm` | 项目管理任务 | @JamesWuHK |
| `bug` | Bug修复 | @codex-developer |
| `review` | 需要Review | 对应负责人 |
| `blocked` | 被阻塞 | PM处理 |
| `urgent` | 紧急 | 优先处理 |

### 分支策略
- `main`: 主分支，保护分支
- `develop`: 开发分支
- `feature/*`: 功能分支 (由Codex创建)
- `arch/*`: 架构分支 (由Claude创建)

### 提交规范
```
[type]: [description]

[body]

Refs: #[issue-number]
```

Type:
- `feat`: 新功能
- `fix`: Bug修复
- `docs`: 文档更新
- `refactor`: 重构
- `test`: 测试
- `chore`: 杂项

# 文档驱动 + 测试驱动开发规范

## 概述

本项目采用 **文档驱动开发 (Documentation-Driven Development, DDD)** 和 **测试驱动开发 (Test-Driven Development, TDD)** 相结合的开发模式。

```
需求 → 文档 → 测试 → 代码 → 审查 → 交付
  ↑___________________________________↓
```

---

## 一、文档驱动开发 (DDD)

### 1.1 文档层级

| 层级 | 文档类型 | 编写者 | 评审者 | 更新时机 |
|------|---------|--------|--------|---------|
| L0 | 产品需求文档 (PRD) | PM | 架构师 + 老板 | 需求变更时 |
| L1 | 架构设计文档 (ADR) | 架构师 | PM + 程序员 | 技术决策时 |
| L2 | 接口/API文档 | 架构师 | 程序员 | API设计时 |
| L3 | 实现文档/注释 | 程序员 | 架构师 | 代码提交时 |

### 1.2 PRD模板 (L0)

```markdown
# PRD: [功能名称]

## 元信息
- Issue: #[number]
- 负责人: @assignee
- 状态: [Draft/In Review/Approved]
- 优先级: [P0/P1/P2]

## 背景与目标
### 问题陈述
[描述要解决什么问题]

### 成功指标
- [ ] 指标1: [具体数值]
- [ ] 指标2: [具体数值]

## 需求范围
### Must Have (必须有)
- [ ] 功能点1
- [ ] 功能点2

### Should Have (应该有)
- [ ] 功能点3

### Nice to Have (可以有)
- [ ] 功能点4

## 用户故事
作为 [角色], 我想要 [功能], 以便 [价值]

## 验收标准 (AC)
- [ ] AC1: [Given-When-Then格式]
- [ ] AC2: [Given-When-Then格式]

## 界面原型
[链接或截图]

## 技术约束
- 性能要求: [如响应时间<200ms]
- 安全要求: [如需要鉴权]
- 兼容性: [如支持浏览器版本]

## 依赖关系
- 依赖: #[issue-number]
- 被依赖: #[issue-number]
```

### 1.3 架构设计文档 (L1 - ADR)

```markdown
# ADR-[编号]: [决策标题]

## 状态
- 提议 / 已接受 / 已废弃

## 上下文
[描述需要做出技术决策的背景]

## 决策
[明确的技术选择]

## 备选方案
| 方案 | 优点 | 缺点 | 结论 |
|------|------|------|------|
| A | ... | ... | 不采用 |
| B | ... | ... | 采用 |

## 影响
- 正面影响: ...
- 负面影响: ...
- 风险: ...

## 相关文档
- PRD: #[number]
- 代码: [文件路径]
```

### 1.4 API文档规范 (L2)

使用OpenAPI 3.0规范，文件名: `api/[模块名].yaml`

```yaml
openapi: 3.0.0
info:
  title: API名称
  version: 1.0.0
paths:
  /endpoint:
    get:
      summary: 简要描述
      parameters:
        - name: param
          in: query
          required: true
          schema:
            type: string
      responses:
        '200':
          description: 成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Response'
```

---

## 二、测试驱动开发 (TDD)

### 2.1 测试金字塔

```
       /\
      /  \
     / E2E\      <- 少量 (10%)
    /________\
   /          \
  / Integration \  <- 中等 (30%)
 /________________\
/                  \
/     Unit Tests     \ <- 大量 (60%)
/______________________\
```

### 2.2 TDD循环

```
1. 红: 写测试 → 运行失败
2. 绿: 写最少代码 → 测试通过
3. 重构: 优化代码 → 保持通过
4. 重复
```

### 2.3 测试规范

#### 单元测试 (Unit Tests)
- **框架**: Jest (JS/TS) / pytest (Python)
- **命名**: `[被测函数].test.ts` 或 `test_[被测函数].py`
- **结构**: Arrange → Act → Assert
- **覆盖率**: 核心逻辑 >= 80%

```typescript
// 示例
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid data', () => {
      // Arrange
      const data = { name: 'John', email: 'john@example.com' };
      
      // Act
      const result = userService.createUser(data);
      
      // Assert
      expect(result).toBeDefined();
      expect(result.email).toBe(data.email);
    });
    
    it('should throw error with invalid email', () => {
      // ...
    });
  });
});
```

#### 集成测试 (Integration Tests)
- **范围**: 模块间交互、数据库操作、API端点
- **命名**: `*.integration.test.ts`
- **环境**: 使用测试数据库/TestContainers
- **覆盖率**: 关键流程 >= 70%

#### E2E测试 (End-to-End)
- **框架**: Playwright / Cypress
- **范围**: 关键用户旅程
- **命名**: `*.e2e.spec.ts`
- **数量**: 控制在20个以内，避免过度测试

### 2.4 测试门禁

```yaml
CI Pipeline:
  - Lint Check
  - Type Check
  - Unit Tests (coverage >= 80%)
  - Integration Tests
  - Build
  - Deploy to Staging
  - E2E Tests (on staging)
  - Deploy to Production
```

---

## 三、Git工作流

### 3.1 分支策略 (GitHub Flow简化版)

```
main (保护分支)
  ↑
feature/user-auth  ← 从main创建
  ↓
Pull Request → Code Review → CI通过 → Merge
```

### 3.2 分支命名规范

| 类型 | 命名格式 | 示例 |
|------|---------|------|
| 功能 | `feature/[issue-id]-简短描述` | `feature/12-user-login` |
| Bug修复 | `fix/[issue-id]-简短描述` | `fix/15-memory-leak` |
| 文档 | `docs/[描述]` | `docs/api-reference` |
| 重构 | `refactor/[描述]` | `refactor/extract-service` |
| 热修复 | `hotfix/[描述]` | `hotfix/critical-bug` |

### 3.3 提交信息规范 (Conventional Commits)

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type:**
- `feat`: 新功能
- `fix`: Bug修复
- `docs`: 文档更新
- `style`: 代码格式（不影响功能）
- `refactor`: 重构
- `test`: 测试相关
- `chore`: 构建/工具/杂项

**示例:**
```
feat(auth): implement JWT token refresh

- Add token refresh endpoint
- Implement automatic refresh in client
- Add tests for token expiration

Refs: #12
```

---

## 四、代码审查规范

### 4.1 审查清单

**架构师审查重点:**
- [ ] 是否符合架构设计
- [ ] 是否有技术债务引入
- [ ] 性能影响评估
- [ ] 安全性检查

**程序员互相审查:**
- [ ] 代码可读性
- [ ] 测试覆盖率
- [ ] 边界条件处理
- [ ] 错误处理

**PM审查重点:**
- [ ] 是否满足需求
- [ ] 用户体验影响
- [ ] 文档同步更新

### 4.2 PR模板

```markdown
## 关联Issue
Fixes #[number]

## 变更内容
- [ ] 功能A
- [ ] 功能B

## 测试
- [ ] 单元测试已添加
- [ ] 集成测试已添加
- [ ] 手动测试通过

## 文档
- [ ] 代码注释已更新
- [ ] API文档已更新
- [ ] README已更新（如需要）

## 截图（如适用）
[UI变更截图]

## 检查清单
- [ ] 代码遵循项目规范
- [ ] 无console.log/debugger
- [ ] 无敏感信息泄露
```

---

## 五、角色职责矩阵

| 阶段 | PM | 架构师 | 程序员 |
|------|-----|--------|--------|
| 需求分析 | 主导PRD | 参与评审 | 了解背景 |
| 架构设计 | 评审 | 主导ADR | 参与讨论 |
| 任务拆解 | 创建Issue | 技术评估 | 工期估算 |
| 测试设计 | - | 指导 | 编写测试 |
| 代码实现 | - | Review | 主导 |
| 代码审查 | 需求符合度 | 架构符合度 | 代码质量 |
| 文档维护 | PRD更新 | ADR更新 | 代码注释 |
| 发布验收 | 最终确认 | 技术确认 | 部署执行 |

---

## 六、实施路线图

### Phase 1: 基础搭建 (Week 1)
- [ ] 配置CI/CD流水线
- [ ] 设置代码覆盖率工具
- [ ] 创建PR/Issue模板
- [ ] 初始化测试框架

### Phase 2: 试点运行 (Week 2-3)
- [ ] 选择一个小功能试点
- [ ] 完整走通 DDD + TDD 流程
- [ ] 收集团队反馈
- [ ] 调整规范

### Phase 3: 全面推广 (Week 4+)
- [ ] 所有新功能遵循规范
- [ ] 定期回顾和改进
- [ ] 度量质量和效率指标

---

## 七、工具链

| 用途 | 工具 | 配置位置 |
|------|------|---------|
| 代码托管 | GitHub | - |
| CI/CD | GitHub Actions | `.github/workflows/` |
| 测试框架 | Jest / pytest | `package.json` / `pytest.ini` |
| 覆盖率 | Codecov | GitHub集成 |
| Lint | ESLint / Black | `.eslintrc` / `pyproject.toml` |
| 类型检查 | TypeScript / mypy | `tsconfig.json` |
| API文档 | Swagger/OpenAPI | `api/*.yaml` |

---

*Version: 1.0*
*Last Updated: 2025-07*

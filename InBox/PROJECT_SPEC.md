# 墨猫问雪 / NoirCat — 项目开发方案书

## 0. 项目概览

- 项目名：墨猫问雪 / NoirCat
- 定位：中英双语网络安全学习社区
- 核心模块：电子书资源 / 博客论坛 / 公会系统 / 学习成长
- 设计理念：核心精简，功能插件化

## 1. 技术栈

| 层级 | 技术 | 说明 |
|------|------|------|
| 前端 | Next.js 15 + React 19 + Tailwind CSS | App Router，SSR |
| 后端 | NestJS + TypeScript | 模块化，REST API |
| 数据库 | PostgreSQL 16 + Prisma ORM | 主数据存储 |
| 缓存 | Redis 7 | 会话、在线状态、Pub/Sub |
| 文件 | MinIO | 对象存储，兼容 S3 |
| 搜索 | Meilisearch | 全文搜索 |
| 实时 | Socket.io | 公会聊天室、通知 |
| 部署 | Docker Compose | 一键启动 |

## 2. 项目目录结构

```text
noircat/
├── frontend/
│   ├── app/
│   │   ├── (books)/       # 电子书模块页面
│   │   ├── (forum)/       # 论坛模块页面
│   │   ├── (guild)/       # 公会模块页面
│   │   ├── (learn)/       # 学习模块页面
│   │   └── layout.tsx     # 全局布局
│   ├── components/        # 共享组件
│   └── lib/               # API封装、工具函数
├── backend/
│   ├── src/
│   │   ├── auth/          # 认证模块
│   │   ├── books/         # 电子书模块
│   │   ├── forum/         # 论坛模块
│   │   ├── guild/         # 公会模块
│   │   ├── learn/         # 学习模块
│   │   ├── notification/  # 通知模块
│   │   └── common/        # 中间件、守卫、装饰器
│   └── prisma/
│       ├── schema.prisma  # 数据库Schema
│       └── migrations/    # 迁移文件
├── docker-compose.yml
├── AGENTS.md              # 项目级AI指令文件
├── PROJECT_SPEC.md        # 本文件
└── README.md
```

## 3. 数据库核心表（Prisma Schema 摘要）

```prisma
// 用户
model User {
  id        BigInt   @id @default(autoincrement())
  username  String   @unique
  email     String   @unique
  password  String
  avatar    String?
  role      String   @default("user") // user/guild_admin/moderator/admin
  createdAt DateTime @default(now())
}

// 电子书
model Book {
  id            BigInt   @id @default(autoincrement())
  title         String
  author        String?
  description   String?
  coverUrl      String?
  format        String   // epub/pdf/mobi/txt
  fileUrl       String
  fileSize      BigInt?
  categoryId    Int?
  uploaderId    BigInt
  downloadCount Int      @default(0)
  rating        Decimal  @default(0) @db.Decimal(3,1)
  createdAt     DateTime @default(now())
}

// 帖子
model Post {
  id           BigInt   @id @default(autoincrement())
  title        String
  content      String   // Markdown原始内容
  contentHtml  String?  // 渲染后缓存
  authorId     BigInt
  categoryId   Int?
  status       String   @default("published") // draft/published/deleted
  viewCount    Int      @default(0)
  likeCount    Int      @default(0)
  commentCount Int      @default(0)
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}

// 公会
model Guild {
  id          BigInt   @id @default(autoincrement())
  name        String   @unique
  description String?
  avatarUrl   String?
  ownerId     BigInt
  level       Int      @default(1)
  maxMembers  Int      @default(50)
  points      Int      @default(0)
  createdAt   DateTime @default(now())
}

model GuildMember {
  guildId      BigInt
  userId       BigInt
  role         String   @default("member") // owner/vice_leader/elder/member
  joinedAt     DateTime @default(now())
  contribution Int      @default(0)
  @@id([guildId, userId])
}

model GuildPlugin {
  id          BigInt   @id @default(autoincrement())
  guildId     BigInt
  pluginId    String
  version     String?
  config      Json?    // 插件配置
  enabled     Boolean  @default(true)
  installedBy BigInt
  installedAt DateTime @default(now())
}

// 学习路线
model LearningPath {
  id          BigInt   @id @default(autoincrement())
  title       String
  description String?
  category    String   // network_security / web_dev 等
  difficulty  String   // beginner/intermediate/advanced
  coverUrl    String?
  createdAt   DateTime @default(now())
}

model LearningItem {
  id          BigInt   @id @default(autoincrement())
  stageId     BigInt
  title       String
  content     String?  // Markdown
  itemType    String   // article/video/exercise/quiz
  resourceUrl String?
  sortOrder   Int
}

model UserProgress {
  id          BigInt   @id @default(autoincrement())
  userId      BigInt
  itemId      BigInt
  status      String   @default("not_started") // not_started/in_progress/completed
  score       Int?
  startedAt   DateTime?
  completedAt DateTime?
  @@unique([userId, itemId])
}
```

## 4. API 设计规范

- 资源命名：名词复数，如 `/api/books`、`/api/posts`
- HTTP 方法：GET 查询 / POST 创建 / PUT 更新 / DELETE 删除
- 认证：JWT Token 放在 `Authorization: Bearer <token>` 头
- 分页：`?page=1&limit=20`，响应含 `total`、`page`、`limit`
- 错误格式：`{ "code": 400, "message": "...", "data": null }`

### 核心接口清单

```text
# 电子书
GET    /api/books              # 搜索/列表
GET    /api/books/:id          # 详情
POST   /api/books              # 上传
GET    /api/books/:id/download # 下载
GET    /api/books/:id/read     # 在线阅读

# 论坛
GET    /api/posts              # 帖子列表
POST   /api/posts              # 发帖
POST   /api/posts/:id/comments # 评论
GET    /api/search?q=关键词    # 全文搜索

# 公会
POST   /api/guilds             # 创建公会
GET    /api/guilds/:id         # 公会详情
POST   /api/guilds/:id/join    # 申请加入
GET    /api/guilds/:id/plugins # 已安装插件

# 学习
GET    /api/learn/paths        # 路线列表
GET    /api/learn/paths/:id    # 路线详情
POST   /api/learn/items/:id/complete  # 标记完成
GET    /api/learn/my-progress  # 我的进度
```

## 5. 公会插件系统

### 插件 Manifest 格式

```json
{
  "id": "daily-checkin",
  "name": "每日签到",
  "version": "1.0.0",
  "description": "公会成员每日签到获得积分",
  "entry": "index.js",
  "permissions": ["guild:member:read", "guild:points:write"],
  "hooks": ["onMemberJoin", "onDailyReset"],
  "config": {
    "pointsPerCheckin": { "type": "number", "default": 10 }
  }
}
```

### 插件入口结构

```typescript
// index.ts
export default {
  manifest: { id, name, version, permissions },
  async activate(ctx) {
    // 注册API路由、定时任务、事件监听
    ctx.router.post('/checkin', async (req, res) => { /* ... */ });
    ctx.setInterval('0 0 * * *', async () => { /* ... */ });
    ctx.on('member.checkin', async (data) => { /* ... */ });
  },
  async deactivate(ctx) {
    ctx.cleanup(); // 清理所有资源
  }
};
```

### 插件通信机制

- **事件总线**：`ctx.emit('事件名', data)` / `ctx.on('事件名', handler)`
- **服务注册表**：`ctx.services.guildPoints.add(guildId, userId, amount)`
- **沙箱限制**：插件只能通过 `ctx.db` 操作数据，不能直接访问文件系统

## 6. 开发约定

### 代码风格
- 语言：TypeScript strict 模式
- 命名：驼峰变量，帕斯卡组件/类，短横线文件名
- 缩进：2 空格
- 引号：单引号
- 分号：必需

### Git 提交规范
```text
feat: 新功能
fix: 修复
docs: 文档
refactor: 重构
chore: 杂项
```

### 测试要求
- 后端：Jest + Supertest，覆盖核心 API
- 前端：Vitest + React Testing Library，覆盖关键交互

## 7. 开发优先级

```text
Phase 1: 项目脚手架 + 用户认证
Phase 2: 论坛模块（发帖/回帖/Markdown渲染）
Phase 3: 电子书模块（上传/检索/在线阅读）
Phase 4: 学习成长模块（路线/进度/测验）
Phase 5: 公会系统（成员/聊天室/插件框架）
```

## 8. 安全要求

- 所有 API 需 JWT 认证（公共接口除外）
- 文件上传验证类型和大小，禁止可执行文件
- Markdown 渲染后经 DOMPurify 过滤
- 使用 Prisma ORM 防止 SQL 注入
- 插件运行在沙箱中，仅通过受控 API 操作

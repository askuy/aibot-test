当前 `proposal.md` 已有内容，但尚未涵盖 Issue 评论中提到的新需求（emoji reaction 和 Markdown 支持）。以下是更新后的完整 proposal 内容：

---

## Why

文章评论是内容平台的核心互动功能，缺少评论功能导致用户无法围绕内容展开讨论，降低了平台的社区活跃度和用户粘性。当前阶段需要建立基础的评论互动体系——包括评论发布、回复、点赞及表情回应——以提升用户参与度。

## What Changes

- 新增评论发布功能，登录用户可对文章发表 Markdown 格式的评论
- 新增评论回复功能，支持对评论进行一层嵌套回复
- 新增评论点赞功能，用户可对评论进行点赞/取消点赞
- 新增评论 emoji reaction 功能，支持 👍 👎 ❤️ 等表情回应
- 评论列表按发布时间排序，支持分页加载

## Capabilities

### New Capabilities
- `article-comments`: 覆盖评论的创建（支持 Markdown 格式）、查询、分页展示，以及评论与文章的关联关系
- `comment-replies`: 覆盖对评论的回复（嵌套一层），包括回复的创建与展示
- `comment-likes`: 覆盖评论点赞与取消点赞，包括点赞数统计
- `comment-reactions`: 覆盖评论的 emoji reaction，包括添加/撤销 reaction 及各类型计数统计

### Modified Capabilities

## Impact

- **API**：新增评论相关 REST 接口（`POST /articles/:id/comments`、`GET /articles/:id/comments`、`POST /comments/:id/replies`、`POST /comments/:id/likes`、`DELETE /comments/:id/likes`、`POST /comments/:id/reactions`、`DELETE /comments/:id/reactions`）
- **认证**：发表评论、回复、点赞、reaction 均需用户登录，依赖现有认证机制
- **数据库**：新增 `comments` 表（含 `parent_id` 字段支持回复、`content_format` 标记 Markdown）、`comment_likes` 表、`comment_reactions` 表
- **内容渲染**：需引入 Markdown 解析库（服务端渲染或客户端渲染）用于评论内容展示
- **文章模块**：文章详情接口需附带评论数统计字段

---

与原有 proposal 相比，主要变更：
1. **Why** 部分补充了"表情回应"作为互动体系的组成部分
2. **What Changes** 新增了 Markdown 支持和 emoji reaction 两条
3. **Capabilities** 新增了 `comment-reactions` capability
4. **Impact** 补充了 reaction 相关 API 端点、新增数据表，以及 Markdown 渲染依赖
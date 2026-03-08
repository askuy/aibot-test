No existing specs found. Here is the proposal document:

## Why

文章评论是内容平台的核心互动功能，缺少评论功能导致用户无法围绕内容展开讨论，降低了平台的社区活跃度和用户粘性。当前阶段需要建立基础的评论互动体系以提升用户参与度。

## What Changes

- 新增评论发布功能，登录用户可对文章发表评论
- 新增评论回复功能，支持对评论进行一层嵌套回复
- 新增评论点赞功能，用户可对评论进行点赞/取消点赞
- 评论列表按发布时间排序，支持分页加载

## Capabilities

### New Capabilities
- `article-comments`: 覆盖评论的创建、查询、分页展示，以及评论与文章的关联关系
- `comment-replies`: 覆盖对评论的回复（嵌套一层），包括回复的创建与展示
- `comment-likes`: 覆盖评论点赞与取消点赞，包括点赞数统计

### Modified Capabilities
<!-- 当前无现有 spec，无需变更 -->

## Impact

- **API**：新增评论相关 REST 接口（`POST /articles/:id/comments`、`GET /articles/:id/comments`、`POST /comments/:id/replies`、`POST /comments/:id/likes`、`DELETE /comments/:id/likes`）
- **认证**：发表评论、回复、点赞均需用户登录，依赖现有认证机制
- **数据库**：新增 `comments` 表（含 `parent_id` 字段支持回复）和 `comment_likes` 表
- **文章模块**：文章详情接口可能需要附带评论数统计字段
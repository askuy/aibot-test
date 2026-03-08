## ADDED Requirements

### Requirement: Authenticated user can post a comment on an article
登录用户 SHALL 能够对指定文章发表评论。未登录用户 MUST 被拒绝，系统返回 401。

#### Scenario: Logged-in user submits a valid comment
- **WHEN** 已登录用户向 `POST /articles/:id/comments` 提交非空评论内容
- **THEN** 系统创建评论记录，返回 201 及评论详情（id、内容、作者、发布时间）

#### Scenario: Unauthenticated user attempts to post a comment
- **WHEN** 未登录用户向 `POST /articles/:id/comments` 发起请求
- **THEN** 系统返回 401 Unauthorized，评论不被创建

#### Scenario: User submits an empty comment
- **WHEN** 已登录用户提交内容为空字符串的评论
- **THEN** 系统返回 422 Unprocessable Entity，并说明内容不能为空

#### Scenario: User posts a comment on a non-existent article
- **WHEN** 已登录用户向不存在的文章 ID 提交评论
- **THEN** 系统返回 404 Not Found

### Requirement: Anyone can retrieve paginated comments for an article
系统 SHALL 向所有访问者提供文章评论列表，按发布时间升序排列，支持分页。

#### Scenario: Fetching first page of comments
- **WHEN** 客户端请求 `GET /articles/:id/comments`（不带分页参数）
- **THEN** 系统返回默认第一页评论列表，包含评论总数、当前页数据及分页元信息

#### Scenario: Fetching a specific page
- **WHEN** 客户端请求 `GET /articles/:id/comments?page=2&per_page=10`
- **THEN** 系统返回对应页的评论，每页最多 10 条，按发布时间升序排列

#### Scenario: Article has no comments
- **WHEN** 客户端请求一篇无评论文章的评论列表
- **THEN** 系统返回 200，data 字段为空数组，total 为 0

#### Scenario: Fetching comments for a non-existent article
- **WHEN** 客户端请求不存在的文章 ID 的评论列表
- **THEN** 系统返回 404 Not Found

### Requirement: Comment list is sorted by creation time ascending
系统 SHALL 始终以评论发布时间升序（最早发布在前）返回评论列表。

#### Scenario: Comments returned in chronological order
- **WHEN** 文章存在多条评论
- **THEN** 系统返回的评论列表按 `created_at` 字段升序排列

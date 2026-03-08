## ADDED Requirements

### Requirement: Authenticated user can like a comment
登录用户 SHALL 能够对评论点赞。同一用户对同一评论 MUST 只能点赞一次，重复点赞 MUST 被拒绝。

#### Scenario: Logged-in user likes a comment for the first time
- **WHEN** 已登录用户向 `POST /comments/:id/likes` 发起请求，且该用户尚未对该评论点赞
- **THEN** 系统创建点赞记录，评论点赞数加 1，返回 201 及更新后的点赞数

#### Scenario: User attempts to like the same comment twice
- **WHEN** 已登录用户对已点赞的评论再次发起 `POST /comments/:id/likes`
- **THEN** 系统返回 409 Conflict，点赞数不变

#### Scenario: Unauthenticated user attempts to like a comment
- **WHEN** 未登录用户向 `POST /comments/:id/likes` 发起请求
- **THEN** 系统返回 401 Unauthorized，点赞不被记录

#### Scenario: Liking a non-existent comment
- **WHEN** 已登录用户向不存在的评论 ID 发起点赞请求
- **THEN** 系统返回 404 Not Found

### Requirement: Authenticated user can unlike a comment
已点赞的登录用户 SHALL 能够取消对评论的点赞。对未点赞评论执行取消操作 MUST 返回错误。

#### Scenario: Logged-in user cancels a like
- **WHEN** 已登录用户向 `DELETE /comments/:id/likes` 发起请求，且该用户已对该评论点赞
- **THEN** 系统删除点赞记录，评论点赞数减 1，返回 200 及更新后的点赞数

#### Scenario: User attempts to unlike a comment they have not liked
- **WHEN** 已登录用户对未点赞的评论发起 `DELETE /comments/:id/likes`
- **THEN** 系统返回 404 Not Found，点赞数不变

#### Scenario: Unauthenticated user attempts to unlike a comment
- **WHEN** 未登录用户向 `DELETE /comments/:id/likes` 发起请求
- **THEN** 系统返回 401 Unauthorized

### Requirement: Comment like count is visible in comment data
系统 SHALL 在评论数据中包含当前点赞总数，以及当前登录用户是否已点赞的状态。

#### Scenario: Authenticated user fetches comments
- **WHEN** 已登录用户请求文章评论列表
- **THEN** 每条评论包含 `likes_count` 字段（整数）和 `liked_by_me` 字段（布尔值，反映当前用户点赞状态）

#### Scenario: Unauthenticated user fetches comments
- **WHEN** 未登录用户请求文章评论列表
- **THEN** 每条评论包含 `likes_count` 字段，`liked_by_me` 字段为 `false`

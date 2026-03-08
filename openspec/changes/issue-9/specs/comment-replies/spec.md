## ADDED Requirements

### Requirement: Authenticated user can reply to a top-level comment
登录用户 SHALL 能够对顶层评论发表一层嵌套回复。系统 MUST 不允许对已是回复的评论再次回复（即不支持二级以上嵌套）。

#### Scenario: Logged-in user replies to a top-level comment
- **WHEN** 已登录用户向 `POST /comments/:id/replies` 提交非空回复内容，且目标评论为顶层评论
- **THEN** 系统创建回复记录（`parent_id` 指向目标评论），返回 201 及回复详情（id、内容、作者、parent_id、发布时间）

#### Scenario: Unauthenticated user attempts to reply
- **WHEN** 未登录用户向 `POST /comments/:id/replies` 发起请求
- **THEN** 系统返回 401 Unauthorized，回复不被创建

#### Scenario: User attempts to reply to a reply (second-level nesting)
- **WHEN** 已登录用户尝试对一条已有 `parent_id` 的回复再次回复
- **THEN** 系统返回 422 Unprocessable Entity，说明不支持二级嵌套回复

#### Scenario: User submits an empty reply
- **WHEN** 已登录用户提交内容为空字符串的回复
- **THEN** 系统返回 422 Unprocessable Entity，并说明内容不能为空

#### Scenario: Replying to a non-existent comment
- **WHEN** 已登录用户向不存在的评论 ID 发起回复请求
- **THEN** 系统返回 404 Not Found

### Requirement: Replies are included when fetching comment list
系统 SHALL 在返回文章评论列表时，在每条顶层评论下嵌套展示其回复列表。

#### Scenario: Comment with replies is fetched
- **WHEN** 客户端请求文章评论列表，且某条评论存在回复
- **THEN** 该评论对象包含 `replies` 字段，列出所有回复，按发布时间升序排列

#### Scenario: Comment with no replies is fetched
- **WHEN** 客户端请求文章评论列表，且某条评论无回复
- **THEN** 该评论对象的 `replies` 字段为空数组

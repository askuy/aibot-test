## 1. 数据库层

- [ ] 1.1 创建 `comments` 表（字段：id, article_id, user_id, parent_id, content, created_at, deleted_at）
- [ ] 1.2 为 `comments` 表添加索引（article_id, parent_id, created_at）
- [ ] 1.3 创建 `comment_likes` 表（字段：id, user_id, comment_id, created_at）
- [ ] 1.4 为 `comment_likes` 表添加 (user_id, comment_id) 联合唯一索引

## 2. 数据模型与仓储层

- [ ] 2.1 定义 Comment 实体模型（含 replies 嵌套结构）
- [ ] 2.2 实现 CommentRepository：按 article_id 查询顶级评论（分页）
- [ ] 2.3 实现 CommentRepository：按 parent_id 批量查询回复列表
- [ ] 2.4 实现 CommentRepository：插入评论/回复，校验 parent_id 层级不超过一层
- [ ] 2.5 实现 CommentLikeRepository：插入点赞记录（幂等，捕获唯一索引冲突）
- [ ] 2.6 实现 CommentLikeRepository：删除点赞记录
- [ ] 2.7 实现评论数 COUNT 查询方法（按 article_id）

## 3. 业务逻辑层

- [ ] 3.1 实现「发表评论」用例：鉴权 → 写入顶级评论
- [ ] 3.2 实现「回复评论」用例：鉴权 → 校验 parent_id 为顶级评论 → 写入回复
- [ ] 3.3 实现「点赞评论」用例：鉴权 → 幂等写入 comment_likes
- [ ] 3.4 实现「取消点赞」用例：鉴权 → 删除 comment_likes 记录
- [ ] 3.5 实现「获取评论列表」用例：查询顶级评论分页 → 批量填充回复（每条最多 20 条）→ 聚合点赞数

## 4. API 接口层

- [ ] 4.1 实现 `POST /articles/:id/comments`（发表评论，需登录）
- [ ] 4.2 实现 `POST /articles/:id/comments/:commentId/replies`（回复评论，需登录）
- [ ] 4.3 实现 `POST /comments/:id/likes`（点赞评论，需登录）
- [ ] 4.4 实现 `DELETE /comments/:id/likes`（取消点赞，需登录）
- [ ] 4.5 实现 `GET /articles/:id/comments`（获取评论列表，支持分页参数 page/page_size）
- [ ] 4.6 在文章详情接口响应中附加 `comment_count` 字段（COUNT 查询 + 短 TTL 缓存）

## 5. 接口校验与错误处理

- [ ] 5.1 评论内容校验：非空、长度上限
- [ ] 5.2 parent_id 层级校验：拒绝指向已是回复的评论
- [ ] 5.3 重复点赞返回 409 或幂等 200（明确接口语义）
- [ ] 5.4 取消不存在的点赞返回 404

## 6. 测试

- [ ] 6.1 单元测试：parent_id 层级校验逻辑
- [ ] 6.2 单元测试：点赞幂等性（重复点赞不增加记录）
- [ ] 6.3 集成测试：发表评论 → 获取评论列表完整链路
- [ ] 6.4 集成测试：回复评论出现在对应顶级评论的 replies 数组中
- [ ] 6.5 集成测试：点赞 / 取消点赞后点赞数正确反映
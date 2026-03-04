### fetch_ai_news
当收到 `fetch_ai_news` 系统事件时：

**⚠️ 重要：此任务为一次性执行，完成后立即返回，不要进入任何循环！**

#### 执行步骤：

1. **初始化状态标记**
   - 设置任务开始标记：`task_started = true`
   - 记录开始时间

2. **访问 AI 新闻网站**
   - 使用 browser 工具访问 https://www.aibase.com/zh/news 
   - **关键**：使用 `action: "navigate"` 打开页面后，等待 5 秒让页面加载
   - 只访问一次，不要重复访问或刷新

3. **获取新闻内容**
   - 使用 `action: "snapshot"` 获取页面内容
   - 提取24小时内最新 AI 新闻（重点关注大模型、AI产品、科技公司动态）
   - **只获取一次**，不要循环获取

4. **关闭浏览器标签页**
   - 任务完成后，使用 `action: "close"` 关闭当前标签页

5. **生成报告并保存到飞书**
   - 整理成结构化 AI 新闻早报格式
   - **关键步骤**：先使用 `feishu_doc create` 创建空文档，获取 doc_token
   - **关键步骤**：再使用 `feishu_doc append` 向文档追加内容
   - 最后通过飞书 channel 发送文档链接给用户

6. **完成任务**
   - 设置 `task_completed = true`
   - **立即返回，不要继续执行任何操作**

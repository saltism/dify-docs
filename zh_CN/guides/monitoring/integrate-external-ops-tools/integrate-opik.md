# 集成 Opik

## Opik 简介

Opik 是一个开源的 LLM 评估平台，用于评估、测试和监控大型语言模型（LLM）应用。它由 Comet 开发，旨在促进对基于 LLM 的应用程序进行更直观的协作、测试和监控。

{% hint style="info" %}
如需了解更多详情，请参考 [Opik](https://www.comet.com/site/products/opik/)。
{% endhint %}

---

## 开始配置 Opik

### 1. 注册/登录 [Opik](https://www.comet.com/signup?from=llm)

### 2. 获取 Opik API 密钥

从右上角的用户菜单中获取 Opik API 密钥。点击 **API Key**，然后点击 API Key 进行复制：

<figure><img src="https://assets-docs.dify.ai/2025/01/a66603f01e4ffaa593a8b78fcf3f8204.png" alt=""><figcaption><p>Opik API 密钥</p></figcaption></figure>

### 3. 集成 Opik 与 Dify

在 Dify 应用程序中配置 Opik。打开需要监控的应用程序，在侧边菜单中打开**监控**，并在页面上选择**追踪应用性能**。

<figure><img src="https://assets-docs.dify.ai/2025/01/9d52a244e3b6cef1874ee838cd976111.png" alt=""><figcaption><p>追踪应用性能</p></figcaption></figure>

点击配置后，将在 Opik 中创建的 **API Key** 和**项目名称**粘贴到配置中并保存。

<figure><img src="https://assets-docs.dify.ai/2025/01/7f4c436e2dc9fe94a3ed49219bb3360c.png" alt=""><figcaption><p>配置 Opik</p></figcaption></figure>

成功保存后，你可以在当前页面查看监控状态。

## 查看监控数据

配置完成后，你可以照常调试或使用 Dify 应用程序。所有使用历史都可以在 Opik 中监控。

<figure><img src="https://assets-docs.dify.ai/2025/01/a1c5aa80325e6d0223d48a178393baec.png" alt=""><figcaption><p>在 Opik 中查看应用数据</p></figcaption></figure>

当你切换到 Opik 时，可以在仪表板中查看 Dify 应用程序的详细操作日志。

<figure><img src="https://assets-docs.dify.ai/2025/01/09601d45eaf8ed90a4dfb07c34de36ff.png" alt=""><figcaption><p>在 Opik 中查看应用数据</p></figcaption></figure>

通过 Opik 的详细 LLM 操作日志将帮助你优化 Dify 应用程序的性能。

<figure><img src="https://assets-docs.dify.ai/2025/01/708533b4fc616f852b5601fe602e3ef5.png" alt=""><figcaption><p>在 Opik 中查看应用数据</p></figcaption></figure>

## 监控数据列表

### **工作流/对话流追踪信息**

**用于追踪工作流和对话流**

| 工作流                              | Opik 追踪                   |
| ---------------------------------- | -------------------------- |
| workflow_app_log_id/workflow_run_id | id                         |
| user_session_id                     | - 放置在元数据中            |
| workflow\_{id}                      | name                       |
| start_time                          | start_time                 |
| end_time                            | end_time                   |
| inputs                              | inputs                     |
| outputs                             | outputs                    |
| Model token consumption             | usage_metadata             |
| metadata                            | metadata                   |
| error                               | error                      |
| \[workflow]                         | tags                       |
| conversation_id/none for workflow | conversation_id in metadata |

**工作流追踪信息**

- workflow_id - 工作流唯一标识符
- conversation_id - 对话 ID
- workflow_run_id - 当前运行的 ID
- tenant_id - 租户 ID
- elapsed_time - 当前运行所用时间
- status - 运行状态
- version - 工作流版本
- total_tokens - 当前运行使用的总令牌数
- file_list - 处理的文件列表
- triggered_from - 触发当前运行的来源
- workflow_run_inputs - 当前运行的输入数据
- workflow_run_outputs - 当前运行的输出数据
- error - 当前运行期间遇到的错误
- query - 运行期间使用的查询
- workflow_app_log_id - 工作流应用程序日志 ID
- message_id - 关联的消息 ID
- start_time - 运行开始时间
- end_time - 运行结束时间
- workflow node executions - 工作流节点执行信息
- 元数据
  - workflow_id - 工作流唯一标识符
  - conversation_id - 对话 ID
  - workflow_run_id - 当前运行的 ID
  - tenant_id - 租户 ID
  - elapsed_time - 当前运行所用时间
  - status - 运行状态
  - version - 工作流版本
  - total_tokens - 当前运行使用的总令牌数
  - file_list - 处理的文件列表
  - triggered_from - 触发当前运行的来源

#### **消息追踪信息**

**用于追踪 LLM 相关对话**

| 聊天                            | Opik LLM                    |
| ------------------------------- | --------------------------- |
| message_id                      | id                          |
| user_session_id                 | - 放置在元数据中             |
| "llm"                           | name                        |
| start_time                      | start_time                  |
| end_time                        | end_time                    |
| inputs                          | inputs                      |
| outputs                         | outputs                     |
| Model token consumption         | usage_metadata              |
| metadata                        | metadata                    |
| \["message", conversation_mode] | tags                        |
| conversation_id                 | conversation_id in metadata |

**消息追踪信息**

- message_id - 消息 ID
- message_data - 消息数据
- user_session_id - 用户会话 ID
- conversation_model - 对话模式
- message_tokens - 消息中的令牌数
- answer_tokens - 答案中的令牌数
- total_tokens - 消息和答案中的总令牌数
- error - 错误信息
- inputs - 输入数据
- outputs - 输出数据
- file_list - 处理的文件列表
- start_time - 开始时间
- end_time - 结束时间
- message_file_data - 与消息关联的文件数据
- conversation_mode - 对话模式
- 元数据
  - conversation_id - 对话 ID
  - ls_provider - 模型提供商
  - ls_model_name - 模型 ID
  - status - 消息状态
  - from_end_user_id - 发送用户的 ID
  - from_account_id - 发送账户的 ID
  - agent_based - 消息是否基于代理
  - workflow_run_id - 工作流运行 ID
  - from_source - 消息来源

#### **审核追踪信息**

**用于追踪对话审核**

| 审核           | Opik Tool         |
| -------------- | ----------------- |
| user_id        | - 放置在元数据中   |
| "moderation"   | name              |
| start_time     | start_time        |
| end_time       | end_time          |
| inputs         | inputs            |
| outputs        | outputs           |
| metadata       | metadata          |
| \["moderation"]| tags              |

**审核追踪信息**

- message_id - 消息 ID
- user_id - 用户 ID
- workflow_app_log_id - 工作流应用程序日志 ID
- inputs - 审核输入数据
- message_data - 消息数据
- flagged - 内容是否被标记需要注意
- action - 采取的具体行动
- preset_response - 预设响应
- start_time - 审核开始时间
- end_time - 审核结束时间
- 元数据
  - message_id - 消息 ID
  - action - 采取的具体行动
  - preset_response - 预设响应

#### **建议问题追踪信息**

**用于追踪建议问题**

| 建议问题              | Opik LLM          |
| --------------------- | ----------------- |
| user_id               | - 放置在元数据中   |
| "suggested_question"  | name              |
| start_time            | start_time        |
| end_time              | end_time          |
| inputs                | inputs            |
| outputs               | outputs           |
| metadata              | metadata          |
| \["suggested_question"]| tags             |

**消息追踪信息**

- message_id - 消息 ID
- message_data - 消息数据
- inputs - 输入内容
- outputs - 输出内容
- start_time - 开始时间
- end_time - 结束时间
- total_tokens - 令牌数量
- status - 消息状态
- error - 错误信息
- from_account_id - 发送账户的 ID
- agent_based - 是否基于代理
- from_source - 消息来源
- model_provider - 模型提供商
- model_id - 模型 ID
- suggested_question - 建议问题
- level - 状态级别
- status_message - 状态消息
- 元数据
  - message_id - 消息 ID
  - ls_provider - 模型提供商
  - ls_model_name - 模型 ID
  - status - 消息状态
  - from_end_user_id - 发送用户的 ID
  - from_account_id - 发送账户的 ID
  - workflow_run_id - 工作流运行 ID
  - from_source - 消息来源

#### **数据集检索追踪信息**

**用于追踪知识库检索**

| 数据集检索           | Opik Retriever    |
| ------------------- | ----------------- |
| user_id             | - 放置在元数据中   |
| "dataset_retrieval" | name              |
| start_time          | start_time        |
| end_time            | end_time          |
| inputs              | inputs            |
| outputs             | outputs           |
| metadata            | metadata          |
| \["dataset_retrieval"]| tags            |
| message_id          | parent_run_id     |

**数据集检索追踪信息**

- message_id - 消息 ID
- inputs - 输入内容
- documents - 文档数据
- start_time - 开始时间
- end_time - 结束时间
- message_data - 消息数据
- 元数据
  - message_id - 消息 ID
  - ls_provider - 模型提供商
  - ls_model_name - 模型 ID
  - status - 消息状态
  - from_end_user_id - 发送用户的 ID
  - from_account_id - 发送账户的 ID
  - agent_based - 是否基于代理
  - workflow_run_id - 工作流运行 ID
  - from_source - 消息来源

#### **工具追踪信息**

**用于追踪工具调用**

| 工具                  | Opik Tool         |
| -------------------- | ----------------- |
| user_id              | - 放置在元数据中   |
| tool_name            | name              |
| start_time           | start_time        |
| end_time             | end_time          |
| inputs               | inputs            |
| outputs              | outputs           |
| metadata             | metadata          |
| \["tool", tool_name] | tags              |

#### **工具追踪信息**

- message_id - 消息 ID
- tool_name - 工具名称
- start_time - 开始时间
- end_time - 结束时间
- tool_inputs - 工具输入
- tool_outputs - 工具输出
- message_data - 消息数据
- error - 错误信息（如果有）
- inputs - 消息的输入
- outputs - 消息的输出
- tool_config - 工具配置
- time_cost - 时间消耗
- tool_parameters - 工具参数
- file_url - 关联文件的 URL
- 元数据
  - message_id - 消息 ID
  - tool_name - 工具名称
  - tool_inputs - 工具输入
  - tool_outputs - 工具输出
  - tool_config - 工具配置
  - time_cost - 时间消耗
  - error - 错误信息（如果有）
  - tool_parameters - 工具参数
  - message_file_id - 消息文件 ID
  - created_by_role - 创建者角色
  - created_user_id - 创建者用户 ID

#### **生成名称追踪信息**

**用于追踪对话标题生成**

| 生成名称                      | Opik Tool         |
| ---------------------------- | ----------------- |
| user_id                      | - 放置在元数据中   |
| "generate_conversation_name" | name              |
| start_time                   | start_time        |
| end_time                     | end_time          |
| inputs                       | inputs            |
| outputs                      | outputs           |
| metadata                     | metadata          |
| \["generate_name"]           | tags              |

**生成名称追踪信息**

- conversation_id - 对话 ID
- inputs - 输入数据
- outputs - 生成的对话名称
- start_time - 开始时间
- end_time - 结束时间
- tenant_id - 租户 ID
- 元数据
  - conversation_id - 对话 ID
  - tenant_id - 租户 ID

# 接口与流式调用

[返回首页](README.md) · [快速开始](QUICKSTART.md)

## 先选协议，再填地址

| 协议 | 地址 | 请求主体 | 典型用途 |
| --- | --- | --- | --- |
| Responses | `https://api.yunluapi.com/v1` | `input` | GPT 编程与应用接入 |
| Chat Completions | `https://api.yunluapi.com/v1` | `messages` | OpenAI 兼容聊天客户端 |
| Anthropic Messages | `https://api.yunluapi.com` | `messages`、`max_tokens` | Claude 原生格式接入 |

GPT 的兼容聊天请求由网关转换为 Responses。协议转换不意味着所有扩展字段都一一对应；工具、文件、推理选项和图像能力应逐项验证。

OpenAI 格式通常使用 `Authorization: Bearer <云路密钥>`。Anthropic Messages 示例使用 `x-api-key` 和 `anthropic-version` 请求头。SDK 会处理各自格式，不要把两种请求正文和返回解析方式混用。

## 一次请求经过哪些环节

| 步骤 | 处理内容 |
| --- | --- |
| 1. 应用发起请求 | 提交地址、密钥、模型 ID 与请求正文 |
| 2. 网关检查 | 核对访问权限与可用额度 |
| 3. 模型处理 | 按所选协议转发，返回结果或错误事件 |
| 4. 应用接收 | 处理普通响应或流式响应 |
| 5. 确认结果 | 结合完成事件与用量日志判断是否成功 |

这是面向使用者的逻辑流程，不展示服务器部署、供应商配置或内部实现。

## 正确判断流式请求结束

流式请求分为连接建立、分块返回和最终完成三个阶段。已收到部分文字，并不表示整次请求成功。

对 Responses：

- `response.output_text.delta`：把增量文字追加到已有输出。
- `response.completed`：完整响应完成。
- `response.failed`、`response.incomplete`、`error`：按未完整成功处理。
- 连接关闭但未收到完成事件：标记中断，保留请求上下文以便排查。

不要把中途失败的文本直接当成完整结果继续自动执行。涉及写文件、提交商品或发送消息等操作时，由你的应用在获得完整结果后单独决定是否执行。

## 地址错误的三个典型例子

| 错误现象 | 常见原因 | 修正方向 |
| --- | --- | --- |
| `/v1/v1/responses` | SDK 和配置都添加了 `/v1` | 只保留一次版本路径 |
| `/chat/completions/chat/completions` | 把完整端点填进自动拼接的字段 | 按客户端要求填 Base URL |
| 请求落到网页登录页 | 把控制台页面当作 API 地址 | 使用 `api.yunluapi.com` 的接入地址 |

## 超时与重试

先区分鉴权、余额、模型权限、速率限制和网络中断。401、余额不足或模型 ID 错误通常需要修正配置，连续重试不会解决问题。

对于明确可重试的限流或临时服务错误，按服务返回提示等待，并限制最大次数。未知结果的生成请求可能已经受理；先核对日志或任务状态，再决定是否重试，避免重复工作或重复消耗。

## 图像与其他扩展

本站目录包含图像模型 `gpt-image-2-codex`，展示名为 GPT Image 2.0。使用前查看[模型详情](https://yunluapi.com/pricing)与相应客户端的图像入口。不要将展示名代替真实 ID，也不要把文本协议示例当作完整图像 API 参数合同。

缓存、工具、附件和图片输入的支持范围随模型与接入方式变化。需要这些能力时，先以最小请求核验，再逐步增加参数。

参考：[云路 OpenAI 接入](https://yunluapi.com/openai-api)、[云路 Claude 接入](https://yunluapi.com/claude-api)、[OpenAI 流式事件说明](https://developers.openai.com/api/docs/guides/streaming-responses)。

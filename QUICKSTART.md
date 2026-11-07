# 快速开始

[返回首页](README.md)

准备云路账号、可用调用额度和一枚专用 API 密钥，再完成一次短请求。下面的示例用于你自己的本机或服务端；不要把密钥填进公开网页代码。

## 1. 创建密钥与选择模型

进入[密钥管理](https://yunluapi.com/keys)，为当前应用创建密钥。核对有效期、额度、分组与模型范围，在[模型广场](https://yunluapi.com/pricing)复制完整模型 ID。

示例默认 GPT 使用 `gpt-6-luna`，Claude 使用 `claude-sonnet-5`。如果你的账号不支持这些 ID，请换成实际可用的同协议模型。

## 2. 设置环境变量

以下占位符必须替换为你自己的云路密钥。仅在本机输入，不要提交到 GitHub、聊天记录或截图。

macOS / Linux（Bash）：

```bash
export YUNLU_API_KEY='YOUR_YUNLU_API_KEY'
export YUNLU_MODEL='gpt-6-luna'
```

Windows（PowerShell）：

```powershell
$env:YUNLU_API_KEY = 'YOUR_YUNLU_API_KEY'
$env:YUNLU_MODEL = 'gpt-6-luna'
```

这两种写法设置的是当前终端会话。关闭终端后是否保留，取决于你另行采用的环境配置方式。不要把真实密钥写进版本管理中的配置文件。

## 3. GPT Responses

### cURL（Bash）

```bash
curl --fail-with-body --no-buffer https://api.yunluapi.com/v1/responses \
  -H "Authorization: Bearer $YUNLU_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "gpt-6-luna",
    "input": "请用一句话解释 API。",
    "store": false,
    "stream": true
  }'
```

这是流式 SSE 响应。HTTP 请求被接受后，仍可能收到失败事件；不要仅根据状态码 200 判定任务完成。

### Python

安装 SDK：

```bash
python -m pip install openai
```

使用 [README 的流式 Python 示例](README.md#五分钟完成首次接入)。该示例分别处理增量文字、失败和完成事件，并关闭 SDK 自动重试，便于先看清首个错误。

### Node.js

安装 `openai`，把示例保存为 `.mjs` 文件后用 Node.js 执行：

```bash
npm install openai
```

```javascript
import OpenAI from "openai";

if (!process.env.YUNLU_API_KEY) {
  throw new Error("请先设置 YUNLU_API_KEY。");
}

const client = new OpenAI({
  apiKey: process.env.YUNLU_API_KEY,
  baseURL: "https://api.yunluapi.com/v1",
  maxRetries: 0,
});

const stream = await client.responses.create({
  model: process.env.YUNLU_MODEL || "gpt-6-luna",
  input: "请用一句话解释 API。",
  store: false,
  stream: true,
});

let completed = false;
for await (const event of stream) {
  if (event.type === "response.output_text.delta") {
    process.stdout.write(event.delta);
  } else if (event.type === "response.completed") {
    completed = true;
  } else if (["response.failed", "response.incomplete", "error"].includes(event.type)) {
    throw new Error("请求未完整成功，请检查错误事件与用量日志。");
  }
}
if (!completed) throw new Error("流提前结束，没有收到完成事件。");
process.stdout.write("\n");
```

## 4. Claude Messages

Claude 的 Anthropic SDK 使用根地址，不附加 `/v1`。

```bash
python -m pip install anthropic
```

```python
import os
from anthropic import Anthropic

client = Anthropic(
    api_key=os.environ["YUNLU_API_KEY"],
    base_url="https://api.yunluapi.com",
    max_retries=0,
)

message = client.messages.create(
    model="claude-sonnet-5",
    max_tokens=128,
    messages=[{"role": "user", "content": "请用一句话解释 API。"}],
)

for block in message.content:
    if block.type == "text":
        print(block.text)
```

或者用 cURL（Bash）：

```bash
curl --fail-with-body https://api.yunluapi.com/v1/messages \
  -H "x-api-key: $YUNLU_API_KEY" \
  -H 'anthropic-version: 2023-06-01' \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "claude-sonnet-5",
    "max_tokens": 128,
    "messages": [{"role": "user", "content": "请用一句话解释 API。"}]
  }'
```

## 5. 核对调用结果

收到回复后，打开[用量日志](https://yunluapi.com/usage-logs)，核对模型、发生时间、用量与实际费用。连接测试也可能消耗额度。

如果没有成功，先按 [FAQ](FAQ.md) 检查地址、协议、权限和额度。先把最小文本请求跑通，再逐项加入图片、文件、工具等能力，避免同时改变多个参数。

## 参考文档

- [云路使用指南](https://yunluapi.com/guide.html)
- [云路 Claude 接入说明](https://yunluapi.com/claude-api)
- [OpenAI Python SDK](https://github.com/openai/openai-python)
- [OpenAI 流式 Responses](https://developers.openai.com/api/docs/guides/streaming-responses)
- [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python)

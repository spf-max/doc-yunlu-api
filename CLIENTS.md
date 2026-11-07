# 客户端配置

[返回首页](README.md)

所有客户端都使用你在云路创建的 API 密钥。客户端账号登录、模型厂商订阅和云路 API 余额是不同的配置项。

## Cherry Studio

1. 打开客户端设置中的模型服务商管理，添加或配置 OpenAI 兼容服务商。
2. API 地址填写 `https://api.yunluapi.com/v1`。如果当前版本会自动拼接 `/v1`，按其说明改填根地址。
3. 填入云路 API 密钥，开启服务商。
4. 获取模型列表，或按[模型目录](MODELS.md)手动添加完整 ID。
5. 把所需模型加入使用列表，在聊天界面选择它，发送一条短消息。
6. 在[用量日志](https://yunluapi.com/usage-logs)核对请求与费用。

获取成功但聊天列表为空时，重点检查“启用服务商”和“添加模型”两步。[官网完整教程](https://yunluapi.com/guides/cherry-studio)

## Claude Code 与 CC Switch

使用 Anthropic Messages 接入，核心字段如下：

| 字段 | 内容 |
| --- | --- |
| 服务商名称 | 云路 API，或你自定义的名称 |
| API 根地址 | `https://api.yunluapi.com` |
| 协议 | Anthropic Messages |
| 密钥 | 云路 API 密钥 |
| 模型 | 例如 `claude-sonnet-5`，以当前权限为准 |

用 CC Switch 管理时，创建或编辑 Claude Code 对应服务商，核对这几个值后保存并启用。再打开新的 Claude Code 会话，通过状态信息与云路调用日志确认实际使用的服务商和模型。

不同版本的客户端字段可能不同。如果有默认模型或不同能力档位的模型映射，逐项检查，避免请求到当前账号没有权限的 ID。只改服务商名称不会改变实际路由。[官网 Claude 接入说明](https://yunluapi.com/claude-api)

## OpenClaw 本地部署

1. 登录云路，打开[OpenClaw 部署入口](https://yunluapi.com/openclaw)。
2. 选择系统、可用模型和专用密钥调用上限。
3. 下载为当前账号生成的安装脚本，在自己的电脑运行并核对确认提示。
4. 安装后打开本地助手；后续使用按生成的启动方式进入。
5. 在[密钥管理](https://yunluapi.com/keys)管理、停用或删除对应密钥。

调用上限不是额外赠送的余额。专用安装脚本包含密钥，不能作为公开教程附件上传。安装说明与系统要求以[官网部署介绍](https://yunluapi.com/openclaw.html)为准。

## YunluCodex

[YunluCodex](https://yunluapi.com/portal/yunlucodex/) 是云路的桌面客户端入口。当前公开下载页提供 Windows 版本；安装文件、版本和使用说明请从该页面获取。

聊天时可以管理多会话、选择模型、使用客户端支持的文件与图片功能。进入 Project 模式后，选择本地文件夹，让模型协助读写代码、运行命令、构建和测试；通过执行输出与修改差异检查结果，必要时停止任务。

Project 会把完成任务所需的代码或文件内容发送给模型，运行和构建使用本机已安装的工具。建议在项目中使用版本管理，并检查每次变更。不要把第三方聊天软件的配置文件直接覆盖到桌面客户端数据目录。

## 自己开发应用

- GPT 编程或应用接入：先看 [Responses 示例](QUICKSTART.md#3-gpt-responses)。
- Claude 原生消息格式：看 [Messages 示例](QUICKSTART.md#4-claude-messages)。
- 已有 OpenAI 兼容聊天应用：检查 `base_url` / `baseURL` 与 `/v1/chat/completions` 的实际拼接方式。
- 前端网页：通过你自己的后端调用，真实密钥只放在服务端。

开始只发送一条小请求。确认地址、权限和费用都正确，再开启工具、附件或批量任务。

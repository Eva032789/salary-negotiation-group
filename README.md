# 候选人状态建群 Skill（salary-negotiation-group）

一句口令自动在飞书创建候选人状态私密群：`客户 候选人 状态 顾问`。

## 功能

- 群名：`客户名-候选人姓名-状态`（如 `太一-焦大伟-谈薪`、`Jully.ai-李爽-待入职`）
- 状态支持：谈薪、待入职、已入职、待回款，以及后续新增的任何状态
- 私密群、发起口令的人为群主、自动邀请指定顾问
- 防重复：同名群已存在时不会重复创建
- 顾问表加速：命中直接建群；未命中自动搜索联系人，建群后回写顾问表

## 安装

1. 解压本包，得到 `salary-negotiation-group/` 目录
2. 将该目录放到 `~/.cola/skills/` 下（macOS/Linux）
3. 重启 AI Agent 或等待 SkillWatcher 自动加载
4. （可选）把 `consultants.template.json` 复制为 `consultants.json` 并填入常用顾问

## 前置条件（一次性配置）

1. 已安装 `lark-cli` 并完成飞书应用配置：`lark-cli config init`
2. 飞书开放平台为应用开通机器人权限：
   - `im:chat:create` —— 创建群
   - `im:chat.members:write_only` —— 添加成员
   - `im:chat.members:read` —— 验证成员
3. 用户身份授权（`lark-cli auth login`）：
   - `contact:user:search` —— 按姓名查找顾问
   - `im:chat:read` —— 查询同名群防止重复

## 使用

直接对 AI 说口令，例如：

- `太一 焦大伟 谈薪 Coco`
- `Jully.ai 李爽 待入职。Cassie`
- `拓原触发 叶伟成 已入职 王宇佳`

口令格式：`客户名 候选人姓名 状态 顾问`（状态可省略，默认「谈薪」）。

## 顾问表说明

顾问表（`consultants.json`）用于把常用顾问的姓名映射到飞书 open_id，避免每次搜索和同名歧义：

```json
{
  "display_name": "Coco",
  "full_name": "Coco 徐静雯",
  "open_id": "ou_xxx",
  "enterprise_email": "coco.xu@example.com"
}
```

快速生成方式：把常用顾问所在的飞书群名告诉 AI（例如“常用顾问就是科学管理offer群的人”），AI 会读取群成员并批量生成顾问表。

## 工作原理与权限说明

- **为什么用机器人身份建群**：机器人建群不需要用户逐次扫码授权，建群时用 `--owner` 把发起人指定为群主，效果等同于“以我身份建群”。
- **机器人可见性问题**：若机器人对目标顾问不可见（错误 232043），技能会自动走两步流程：先建群（只含发起人），再单独把顾问加入；无法加入的用户会明确报告。
- **安全性**：群始终为私密，默认只邀请发起人和指定顾问，不邀请候选人和客户。

# 新 Agent 从这里开始

这是一份公开的接入说明，不是业务知识库。读说明不需要登录；读取私有笔记仍须 GitHub 授权。

| 仓库 | 用途 | 能否放业务内容 |
| --- | --- | --- |
| [agent-memory-start](https://github.com/sfczcserafwq/agent-memory-start) | 公开入口，仅说明怎么接入 | 不能放笔记、原始对话、数据、账号或凭据 |
| [agent-memory](https://github.com/sfczcserafwq/agent-memory) | 私有知识库，笔记与共享 Skill 的正式来源 | 仅放用户授权、经过脱敏的有用知识 |

## 用户只需复制这一段

```text
请阅读这个公开接入说明，并帮助我连接已有的私有知识库：
https://github.com/sfczcserafwq/agent-memory-start

目标私有仓库：sfczcserafwq/agent-memory。
不要创建新的知识库，不要把私有仓库改成公开，不要把业务内容写到公开入口。

先检查 Git、GitHub CLI（gh）、Node.js 18+ 是否可用。
缺少工具时说明需要安装什么，并遵守本平台的安装审批要求。
检查 gh auth status；未登录时用浏览器设备授权，不要让我粘贴密码或令牌。
登录成功不等于有仓库权限，还要检查目标仓库可读及当前账号的写入权限。

同机已有这个仓库时，核实 origin 后复用；多个 Agent 同时写入时，各用独立工作副本。
没有工作副本时，克隆目标私有仓库到新的空目录；不覆盖现有目录。

克隆成功后，先读 AGENTS.md，再读
.qoder/skills/sync-project-memory/SKILL.md。
按 notes/project.md 和 README.md 的链接按需读取知识，不全量塞入历史。

以后开始任务前同步远端；结束任务后仅提交本次授权的文件，推送并验证远端。
只有 verify-sync.mjs 输出 SYNC_OK，才能说已写回完成。
工具、权限或网络不满足时，明确说明卡在哪一步，不伪造成功。
```

## Agent 按顺序检查

1. **工具**：执行 `git --version`、`gh --version`、`node --version`。刚安装后旧终端可能还没刷新 PATH；先确认工具实际安装位置，不反复重装。
2. **登录**：执行 `gh auth status`。未登录时执行 `gh auth login --hostname github.com --git-protocol https --web`，让用户在浏览器确认授权。
3. **仓库权限**：用下面的只读命令核实仓库身份、私有属性和写权限。

```sh
gh api repos/sfczcserafwq/agent-memory --jq '{repo: .full_name, private: .private, can_push: .permissions.push}'
```

4. **工作副本**：选择不存在的新目录，执行 `gh repo clone sfczcserafwq/agent-memory <选定目录>`。尖括号是占位说明，执行时必须替换。已有副本先检查 `git remote -v`、分支和工作区，不覆盖未提交修改。
5. **规则**：从私有仓库读取 `AGENTS.md`、共享 Skill 和项目入口；这些文件不能在未授权前凭空获取。
6. **结束验证**：审查内容，明确列出文件后提交、普通推送，再运行以下命令。任务路径必须换成本次真实记录。

```sh
node .qoder/skills/sync-project-memory/scripts/verify-sync.mjs --local
node --test tests/verify-sync.test.mjs
node .qoder/skills/sync-project-memory/scripts/verify-sync.mjs --task tasks/<本次记录>.md
```

不为测试权限制造空提交或上传无关文件。没有真实任务时，报告已核实访问权限，不声称完成了业务写回。

## 常见卡点

| 现象 | 应该检查什么 |
| --- | --- |
| 找不到仓库、404 | 地址及登录账号；私有仓库会向无权限账号隐藏，联系仓库所有者授权，不改公开 |
| `gh` 不存在 | 是否已安装、终端 PATH 是否刷新；Windows 安装目录可以包含空格，命令路径需加引号 |
| API 能访问，Git 连接超时或重置 | 两条网络链路可能不同；这是网络问题，不等于密码错误，不无限重试或改代理设置 |
| `gh` 已登录，Git 仍报认证失败 | Git 与 CLI 的凭据来源可能不同；检查凭据集成，不把令牌写进远端地址或聊天 |
| 没有终端或 GitHub 连接器 | 该 Agent 只能阅读公开说明；不能声称已经克隆或写回 |
| 推送被拒绝 | 可能无写权限或远端已有新提交；保留本地内容，检查后正常整合，不能强推 |

公开说明不是访问密钥，也不是自动同步所有平台聊天的服务。新 Agent 仍须得到仓库授权；各平台的任务结束钩子也须单独接入。

# PRD Suite

一套写 PRD 的 skill 套件：把"接到一句话需求就直接开写"拆成四个可独立触发的步骤，讨论结论落盘、可跨会话续、多个需求并行时不互相污染。

## 四个 skill

| skill | 干什么 |
|---|---|
| `requirement-alignment` | 把需求拆成一条条能被单独确认或推翻的陈述句，逐条对齐，维护 `alignment.md` |
| `requirement-research` | 针对某个具体未决点查同类产品的做法，产出对比供人拍板；含两份取材标准（`references/b2b-saas.md` 企业功能、`references/open-source.md` 开源与技术方案） |
| `prd-writing` | 把已对齐的需求写成 `PRD.md`，配可切状态的 `mockup.html` |
| `prd-review` | 校验一份 PRD 的完备性、可追溯性与一致性，只输出缺失清单，不改文档 |

四者不互相调用，靠共享文件（`alignment.md`、`PRD.md`）串联，任何一个都能单独运行。

## 安装

安装就是把四个 skill 目录拷进你的 agent 的 skills 目录，没有安装脚本。

**Claude Code** 示例：

```bash
# 个人级（所有项目可用）
cp -r requirement-alignment requirement-research prd-writing prd-review ~/.claude/skills/

# 或项目级（仅当前项目可用）
cp -r requirement-alignment requirement-research prd-writing prd-review /path/to/project/.claude/skills/
```

其他 agent（Codex、Gemini CLI、Cursor 等）请查各自文档确认 skill 目录的位置，把四个目录整体拷过去即可，目录结构不用改。

## AGENTS.md：先确认你的 agent 会不会自动读它

`AGENTS.md` 是四个 skill 共用的契约（工作区结构、开场路由、编号规则、`alignment.md` 格式），要放在**工作区**根目录（比如 `prd-workspace/AGENTS.md`），不是放进 skills 目录。

**情况一：你的 agent 会自动读取工作区根目录下的 `AGENTS.md`**
（例如支持 [AGENTS.md](https://agents.md) 标准的 agent）
→ 拷一份 `AGENTS.md` 到工作区根目录即可，不用改 skill。

**情况二：不会自动读取**
→ 要手工把 `AGENTS.md` 的内容追加到每个 `SKILL.md` 末尾：

```bash
for d in requirement-alignment requirement-research prd-writing prd-review; do
  cat AGENTS.md >> "$d/SKILL.md"
done
```

**不这么做的后果**：四个 skill 依然会被正常触发、正常干活，但开场路由、编号规则（C/O/R/D 编号永不复用）、`alignment.md` 的分区格式全部失效——没有任何东西会拦住它把结论写错需求目录、编号复用，或者推翻一条结论却不留理由。

## 工作区怎么用

一个需求一个目录，一个需求开一个新 session（同一 session 里不切需求）。工作区结构、`index.md` 路由规则、归档规则详见 `AGENTS.md`。

`alignment.md` 头部固定三行：

```
- 停在哪：③ 某个环节的规则已定完，④ 还没谈
- 卡在什么上：O3 某个问题没定，等相关方回复
- 回来第一件事：把 O3 的答案落进 C 区，然后接着谈 ④
```

每轮讨论结束更新这三行——这是关掉 session、下次重新进来时唯一的接续依据。

## 验证契约是否生效

进到工作区里问一句"我们有哪些进行中的需求"。skill 会去读 `index.md` 回答，答得上来（列出目录、中文名、状态）就说明 `AGENTS.md` 的契约生效了；如果它开始瞎猜或者直接跳进对齐流程反问你需求细节，说明没读到，回去做上面的"情况二"。

## 说明

这是个人自用工具，没有做通用性设计，欢迎按自己团队的习惯改。

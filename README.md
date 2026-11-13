# Skills

个人维护的 Agent Skills 集合，每个 skill 独立存放，便于按需安装和持续扩展。

## 当前 Skills

| Skill | 用途 |
| --- | --- |
| [Dayima Java Conventions](skills/dayima-java-conventions/SKILL.md) | 大姨妈 Java 工程规范，覆盖方法命名、代码与注释格式、接口、持久化、MapStruct 转换和验证。 |

## 目录结构

采用 [skills CLI 官方支持的目录结构](https://github.com/vercel-labs/skills/blob/main/docs/creating-skills.md#skill-discovery)：

```text
skills/
├── README.md
└── skills/
    └── dayima-java-conventions/
        ├── SKILL.md
        ├── agents/
        │   └── openai.yaml
        ├── assets/
        │   └── AGENTS.md.template
        └── references/
```

## 查看与安装

在仓库根目录查看可发现的 skills，此命令不会安装：

```bash
npx skills add . --list
```

从本地仓库安装到 Codex 的用户目录：

```powershell
npx skills add 'D:/develop/project/other/skills' --skill dayima-java-conventions --agent codex --global
```

内容推送到 GitHub 后，也可以从远程仓库安装：

```bash
npx skills add tanmouren66/skills --skill dayima-java-conventions
```

远程示例默认安装到当前项目；添加 `--global` 可安装到用户目录，添加 `--agent codex` 可指定 Codex。更多选项见 [skills CLI 文档](https://github.com/vercel-labs/skills/blob/main/docs/commands.md#skills-add)。

## 新增 Skill

1. 在 `skills/` 下创建独立目录，名称使用小写字母、数字和连字符，例如 `skills/my-skill/`。
2. 添加 `SKILL.md`，YAML frontmatter 中填写与目录一致的 `name` 和说明用途、触发场景的 `description`。
3. 按实际需要添加 `references/`、`scripts/`、`assets/` 或 `agents/`，使用相对路径引用 skill 内的资源。
4. 更新上方索引，并运行 `npx skills add . --list` 检查 CLI 能否发现新增 skill。

每个 skill 的说明与资源放在自己的目录中；仓库根目录用于维护索引和使用说明。

# 仓库同步

新增同步目标通常只需要在 `config` 目录添加一个 `.yml` 文件。

## 添加同步仓库

新建配置文件，例如 `config/example.yml`，并使用容易识别的文件名：

```yaml
name: Sync Example

env:
  SOURCE_GITHUB_REPO: godothub/source-repo
  TARGET_ATOMGIT_REPO: godothub/target-repo
  MAX_SYNC_FILES: 100
  START_RELEASE_TAG: ''
  SKIP_RELEASES_BEFORE_START: false
  SYNC_BRANCHES: true
  SYNC_TAGS: true
  SYNC_RELEASES: true

triggers:
  schedule: true
  schedule_cron:
    - '0 8 * * *'
  workflow_dispatch: true
```

## 配置属性

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| `name` | 字符串 | GitHub Actions 中显示的任务名称。 |
| `SOURCE_GITHUB_REPO` | 字符串 | 源 GitHub 仓库，格式为 `owner/repo`。 |
| `TARGET_ATOMGIT_REPO` | 字符串 | 目标 AtomGit 仓库，格式为 `owner/repo`。 |
| `MAX_SYNC_FILES` | 数字 | 单次运行最多新上传或重新上传的 Release 附件数量。 |
| `START_RELEASE_TAG` | 字符串 | 开始同步的 Release Tag；不限制时使用空字符串 `''`。 |
| `SKIP_RELEASES_BEFORE_START` | 布尔值 | 为 `true` 时忽略 `START_RELEASE_TAG` 之前的 Release。此时 Tag 必须存在于源仓库。 |
| `SYNC_BRANCHES` | 布尔值 | 是否同步全部 Git 分支。 |
| `SYNC_TAGS` | 布尔值 | 是否同步全部 Git 标签。同步 Release 时建议开启。 |
| `SYNC_RELEASES` | 布尔值 | 是否同步 Release 元数据和附件。 |
| `schedule` | 布尔值 | 是否启用定时同步。 |
| `schedule_cron` | 字符串数组 | 定时同步的 UTC cron 表达式列表。`schedule` 为 `false` 时不会生效。 |
| `workflow_dispatch` | 布尔值 | 是否允许通过手动运行加载该配置。 |

## 定时同步

每个配置文件都可以设置自己的定时任务。配置文件提交到 `main` 后，流水线会汇总所有已启用的 `schedule_cron`，去重后自动更新唯一工作流的定时入口。

`schedule_cron` 可以设置一个或多个 UTC cron：

```yaml
triggers:
  schedule: true
  schedule_cron:
    - '0 8 * * *'
    - '0 16 * * *'
```

配置中的 `schedule` 决定是否启用定时同步，设为 `false` 时建议使用空数组：

```yaml
triggers:
  schedule: false
  schedule_cron: []
```

## 手动同步

在 GitHub 仓库的 **Actions → Sync Repositories → Run workflow** 中运行：

- 输入 `all`：运行所有启用了 `workflow_dispatch` 的配置。
- 输入配置文件名：只运行对应配置，例如 `godot` 或 `godot.yml`。

## 常见组合

```yaml
# 完整同步
SYNC_BRANCHES: true
SYNC_TAGS: true
SYNC_RELEASES: true

# 只同步标签和 Release
SYNC_BRANCHES: false
SYNC_TAGS: true
SYNC_RELEASES: true

# 只同步 Git 代码和标签
SYNC_BRANCHES: true
SYNC_TAGS: true
SYNC_RELEASES: false
```

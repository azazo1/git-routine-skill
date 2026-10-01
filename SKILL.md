---
name: git-routine-skill
description: commit message 编写与 tag 发布的日常 git 流程规范. 当用户使用 commit / commit message 关键词要求提交, 或以 commit doit / commit ex / commit fast / commit only 要求直接提交, 或要求打 tag, push tag, 以及观察 ci 执行情况时使用.
---

# git 日常流程

## commit

kw: commit / commit message

你不需要真正地执行 commit 命令, 只需要给出 commit message 内容和其等价的 commit 命令, 两者均需单独给出.

- 你应遵循 Conventional Commits, 查看过往的 commit messages 都是怎么构建的并参考, 然后根据当前的 staged 的代码编写 message. 注意不要假设上一个你提供的 commit message 用户提交与否, 检查历史 commits, 无论第几次.
  - 查看过往的 commit 的时候, 不应该使用 oneline, 而是要看全其 commit message.
- 你的 message 不应该只是一句抽象的话语, 应该注重于代码更改解决了什么具体的问题 / 实现了什么具体的功能, 用词应该简单而精准且具有辨识度, 不要使用生词难词, 便于在多个提交当中一眼辨识出当前提交的作用.
- 必须 git diff 之类的命令, 查看代码的精确修改, 防止遗漏用户自己的修改或者由于上下文差异导致信息偏差.
- 如果你认为一句无法完全精确描述本次提交, 那么给出一句有效的总结之后, 分点补充当前提交的内容.
  - 分点补充的时候, 句子开头为小写, 句末需要有句号 (.).
- 你可以在 message 中补充用户的部分提示词来辅助你本次修改说明.
- 有时候可以直接使用某些符号名更快捷准确地指代对象而不是费心描述对象本身.
- commit 范围为 staged 的内容范围而不是这次对话上下文的内容范围, 请你先检查 staged 代码之后再编写 message, 不要自以为是.
- 如果没有任何 staged 的内容, 那么默认为距离上一次 commit 的所有更改.
- message 应该使用四层反引号包裹.
- 等价 commit 命令的编写需要注意 `-m` 多个会在之间插入非期望的空行, 你可以构造多行命令而不是每行一个 `-m` 来规避这个问题, 比如:

  ```shell
  cd /path/to/project # 必须使用绝对路径
  git add file_a file_b # 此行可选
  git commit -m "feat(xyz): 123" -m "- a.
  - b.
  - c."
  ```

  在 unix 类系统中, 给出等价命令就使用以上格式, 不要自创命令使用方式, 比如 `` <<'EOF' EOF`` 等不够通用在 fish 中无法使用.
- 等价命令不要拆分成两个代码块.
- 工作区脏 (暂存区或工作区有其他无关改动) 又只想提交某些文件时, 不要用 `git add`, 而是使用 `--only` 指定路径, 仅提交这些文件的当前内容, 不会带上暂存区中其他文件的改动, 文件列表放在命令最后并用 `--` 分隔, 比如 `git commit --only -m "..." -- file_a file_b`.
- 需要注意给出 message 的时候, 第一行和后面的行不要黏连, 不然可能会被 git 将后面的行视为标题行, 比如:

  ````text
  feat(xyz): 123
  - a.
  - b.
  - c.
  ````

  中, 在 123 这行后面缺少了一个换行.
- 不要在 commit message 中放置 `\n` 代替原始的换行符, 就是说不要使用 `-m "-a\n-b"` 这样的方式糊弄换行.
- 当你给出 message 和等效命令的时候, 用户可能会自己复制然后执行而并没有事后和你通知, 后续工作可能需要重新检查 git 状态.
- 先给 message, 再给等效命令, 不要反过来.

### commit doit / commit ex

`commit doit`, `commit ex(ec)` 等, 请先输出 commit message, 然后直接尝试提权执行 git commit.

### commit fast

`commit fast`: 不需要阅读 git diff, 你要提交的内容就是用户上一次 prompt 让你改的 (前几次 prompt 的修改不包括), 如果最近几次的提交风格你已经在之前查看过了, 那么就无需重新检查, 然后直接按照上面的规范进行.

### commit only

`commit only`: 表示用户显式地让你忽略未 staged 的部分, 并且不管代码是否有问题, 都给出 commit message, 不修改暂存区, 也不修改代码.

## tags

如果用户要求你使用新的版本号打 tag, 那么在打 tag 之前需要查阅距离上一个 tag, 项目做了什么更改, 总结所有的更改之后补充 tag 的描述, 不要过于简短, 可以参考成熟的仓库是如何编写 release notes 的, 然后检查项目文件中的版本号是否已经同步更新, 如果没有更新, 新建一个 commit 完成更新, 然后带着更新描述打 tag, 并且尝试 push, 仅此情况中对这些 git 的写操作都要提权运行, 不要尝试在沙箱中运行.

如果用户让你 tag 但是没有指定版本号, 请你结合项目整体和过往修改, 自行决定语义版本号的变化.

tag 描述不要仅根据 commits 描述来编写, 你不清楚的地方需要深入到 commits 更改来确定.

- 更加具体的编写查看 <https://github.com/azazo1/create-github-release-flow>, tag push 时必须查看其内容, 你也可以查看可能在本地安装了的对应 dsh skill. 上面的描述仅供参考, 具体以 skill 中的介绍为准.

## CI

push commit 或 tag 之后 CI 已经被触发, 跟 CI 用后台任务, 不要用 `gh run watch`: 它在非交互环境 (agent 的后台任务, 没有 tty) 里不会收尾, run 已经 `completed/success` 了进程还在空转, 只能外部 kill. 它在人类自己的终端里才正常.

把等待放进一个后台任务里: 任务内部自己循环查状态, 查到终态 (或到达次数上限) 就退出并打印结果. 这个后台任务启动之后, 不要在 agent 侧反复调用 `job_output` 之类的工具去轮询, harness 会在后台任务结束时自动提醒, 收到提醒之后再收集输出即可.

后台任务起来之后, 先告知用户 CI 观察正在后台进行, 然后停止工具调用, 不要出现 `趁 CI 正在执行过程中, 我来...` 这类动作: 不等 CI 结果的事情也留到收到通知之后再做, 安静等 CI 完成的自动通知即可.

观察 CI 要尽量只用不需要提权的只读动作: `gh run list` / `gh run view` / `gh api .../logs` / `gh run download` 都是读取, 直接跑即可, 不要因为习惯而提权 (提权会阻塞整个 agent 线程). 另外统一显式带上 `-R <owner/repo>`, 这样不依赖 cwd 也不必先 cd 进仓库.

下面是后台任务入口的参考实现: 认领本次运行 (按本地短 hash 对上, 不要只取最新一条, 并发触发时会拿错), 等终态, 再逐 job 汇报, 失败的 job 拉日志到 `.tmp/ci/` 并只摘出关键错误行. 次数上限按 CI 的典型耗时设, 15 秒一次足够, 不要查得太密.

```shell
repo=<owner/repo>
branch=<触发 CI 的分支>
sha=$(git rev-parse HEAD | cut -c1-7)
echo "claiming run for $sha"

# 认领: 轮询到出现本次 hash 的运行为止
id=
for _ in $(seq 20); do
    id=$(gh run list -R "$repo" --branch "$branch" --limit 10 --json databaseId,headSha \
        --jq ".[] | select(.headSha | startswith(\"$sha\")) | .databaseId" | head -1)
    [ -n "$id" ] && break
    sleep 15
done
[ -n "$id" ] || { echo "ERROR: 未认领到 $sha 的运行"; exit 1; }
echo "claimed run $id"

# 等终态, 每轮打印状态便于事后看卡在哪一步
for i in $(seq 60); do
    s=$(gh run view -R "$repo" "$id" --json status,conclusion --jq '.status + "/" + (.conclusion // "-")')
    echo "$(date +%T) round=$i $s"
    case $s in completed/*) break ;; esac
    sleep 15
done

echo '=== jobs ==='
gh run view -R "$repo" "$id" --json jobs --jq '.jobs[] | "\(.name) \(.status)/\(.conclusion)"'

# 失败的 job: 日志落到 .tmp/ci/, 只摘关键错误行, 不把全量日志倒进上下文
mkdir -p .tmp/ci
for j in $(gh run view -R "$repo" "$id" --json jobs --jq '.jobs[] | select(.conclusion=="failure") | .databaseId'); do
    file=".tmp/ci/fail-$j.log"
    gh api "/repos/$repo/actions/jobs/$j/logs" > "$file" 2>/dev/null
    echo "=== failed job $j ==="
    errs=$(grep -nE 'Error:|error:|FAILURE|##\[error\]|^e: ' "$file" | head -30)
    if [ -n "$errs" ]; then echo "$errs"; else tail -20 "$file"; fi
done
echo 'WAIT DONE'
```

要点:

- 脚本最后打印 `WAIT DONE`, 看到它就说明后台任务已经收尾, 不必怀疑是否中途退出.
- 用 `gh api .../jobs/<job-id>/logs` 精细取失败 job 的日志, 比 `gh run view --log-failed` 更可控; 全量日志只落盘, 进上下文的只有摘出来的错误行 (没有匹配时退回最后 20 行).
- 取产物另用 `gh run download -R "$repo" "$id" --pattern '*<平台与变体段>*' --dir <目录>`, 产物名里的版本段可能带短 hash, 不要写死; `--dir` 下会按 artifact 名再建一层目录.
- 若沙箱内确实因为缺少认证而失败, 才按提权规则重试一次, 不要默认提权.
- 这样的分钟级等待整块放后台任务, 不要在前台死等, 也不要拆成一次次的单独查询. PowerShell 用户把等待循环写成 `for ($i=0; $i -lt 60; $i++) { ...; Start-Sleep 15 }` 即可, 判断逻辑与上面一致.

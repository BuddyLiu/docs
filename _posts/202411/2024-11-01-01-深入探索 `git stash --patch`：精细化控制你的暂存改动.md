---
title:      深入探索 `git stash --patch`：精细化控制你的暂存改动
date:       2024-11-01
tags:
- git
---

在软件开发过程中，我们经常需要在不同的任务之间切换。有时候，你可能会在一个功能做到一半时，突然被要求去处理一个紧急的bug。这时，你并不希望提交当前尚未完成的代码，但也不想丢失这些改动。`git stash` 命令就是为了解决这个问题而存在的，它可以帮助你将当前的改动暂存起来，以便你可以切换到其他分支进行工作。而 `git stash --patch` 则进一步提供了对暂存改动的精细化控制，让你可以选择性地暂存代码的某些部分。

## `git stash --patch` 简介

`git stash` 命令用于将工作区和暂存区的改动存储在一个栈中，这样你就可以在不影响当前工作的情况下切换到其他分支。当你完成其他任务后，可以使用 `git stash apply` 或 `git stash pop` 命令来恢复这些改动。

而 `git stash --patch`（或简称 `git stash -p`）则提供了一个交互式的界面，允许你选择性地暂存文件的某些部分（称为"hunk"）。这意味着你可以决定哪些改动需要被暂存，哪些需要保留在工作目录中，从而为你提供了更高的灵活性。

## 使用 `git stash --patch`

### 前提条件

在开始使用 `git stash --patch` 之前，你需要确保你的工作目录中有一些未提交的改动。这些改动可以是对已存在文件的修改，也可以是新增或删除的文件。

### 基本用法

在命令行中输入 `git stash --patch` 或 `git stash -p`，Git 会开始遍历所有包含改动的文件，并为你展示每个文件的每个hunk。对于每个hunk，Git 会询问你是否希望暂存这个hunk（通过输入 `y` 或 `n`），或者你可以选择其他选项来控制暂存行为。

### 交互式选项

在 `git stash --patch` 的交互式提示中，你有多种选项可以选择：

- **y**：暂存当前的hunk。
- **n**：不暂存当前的hunk，继续下一个hunk的询问。
- **q**：退出交互式模式，不进行任何暂存操作。
- **a**：暂存当前文件的所有hunk。
- **d**：不暂存当前文件的所有hunk。
- **/**：搜索一个hunk，允许你通过输入一个正则表达式来定位特定的hunk。
- **e**：手动编辑当前的hunk，这允许你进一步细化你想要暂存的内容。
- **?**：显示帮助信息，列出所有可用的选项。

### 实际举例

假设你在一个名为 `simplegit.rb` 的文件中做了一些改动，并且这些改动分布在多个hunk中。你希望暂存其中的一部分，但保留另一部分以便继续工作。

#### 步骤 1：查看改动

首先，你可以使用 `git diff` 命令来查看你的改动：

```bash
$ git diff
diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index 66d332e..8bb5674 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -16,6 +16,10 @@ class SimpleGit
         return `#{git_cmd} 2>&1`.chomp
       end
     end
+
+    def show(treeish = 'master')
+      command("git show #{treeish}")
+    end
```

#### 步骤 2：运行 `git stash --patch`

接下来，运行 `git stash --patch` 命令：

```bash
$ git stash --patch
```

Git 会开始遍历每个hunk，并询问你是否希望暂存它。对于上面的改动，你可能会看到类似下面的提示：

```bash
diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index 66d332e..8bb5674 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -16,6 +16,10 @@ class SimpleGit
         return `#{git_cmd} 2>&1`.chomp
       end
     end
+
+    def show(treeish = 'master')
+      command("git show #{treeish}")
+    end

Stash this hunk [y,n,q,a,d,/,e,?]?
```

#### 步骤 3：选择性地暂存hunk

在这个例子中，假设你只想暂存 `show` 方法的添加，你可以输入 `y` 来暂存这个hunk。如果你不想暂存任何其他改动，可以在后续的提示中输入 `n`。

#### 步骤 4：查看暂存结果

完成交互式暂存后，你可以使用 `git stash list` 命令来查看当前的stash列表：

```bash
$ git stash list
stash@{0}: WIP on master: 1b65b17 added the index file
```

这表明你的改动已经被成功暂存。

### 恢复暂存的改动

当你准备好恢复这些暂存的改动时，可以使用 `git stash apply` 或 `git stash pop` 命令。`apply` 命令会保留stash，而 `pop` 命令则会删除stash。

```bash
$ git stash apply
# 或者
$ git stash pop
```

## 总结

`git stash --patch` 是一个非常强大的工具，它允许你在切换任务时，对未完成的改动进行精细化的控制。通过交互式地选择哪些改动需要被暂存，你可以更加灵活地管理你的工作流。无论是处理紧急bug，还是在多个功能之间切换，`git stash --patch` 都能帮助你保持工作区的整洁，提高你的开发效率。
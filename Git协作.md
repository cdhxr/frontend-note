
- 创建自己的分支，在在自己的分支工作
- 勤提交，降低冲突可能性
- 每次开始前都要pull同步本地代码
- 每次push建立push request


开发流程：

```sh
# 1. 克隆仓库（如果没有）
git clone <远程仓库URL>
cd <项目目录>

# 2. 创建新功能分支
git checkout main
git pull origin main
git checkout -b feature-xyz #创建并切换到新的功能分支

# 3. 开发代码
# （修改文件）
git status  # 查看当前变更的文件
git diff    # 查看修改内容（可选）

# 4. 提交代码
git add .   # 将改动的文件加入暂存区
git commit -m "实现 XYZ 功能"

# 5. 确保基于最新 main
git fetch origin
git checkout main
git pull origin main
git checkout feature-xyz
git rebase main  # 让 feature-xyz 基于最新 main

# 6. 推送到远程仓库
git push origin feature-xyz

# 7. 在 GitHub/GitLab 创建 PR 并合并
# 或者在本地合并：
git checkout main
git pull origin main
git merge feature-xyz
git branch -d feature-xyz
git push origin --delete feature-xyz

```

- **保持 `main` 干净**：不要在 `main` 上直接开发新功能，始终创建新的 `feature` 分支。
- **优先 `rebase`，避免 `merge` 冲突**：在提交 PR 之前，`rebase` 一次 `main`，保证提交历史清晰。
- **小步提交**：每次开发完成一个小功能就 `commit`，方便回滚。
- **多人协作**：如果你的同事也在 `feature-xyz` 分支上工作，记得定期 `git pull --rebase origin feature-xyz`。


## **创建 Pull Request (PR) 并合并**

在 GitHub/GitLab 上：

1. 进入仓库，找到 `feature-xyz` 分支。
2. 创建 **Pull Request (PR)**，请求合并到 `main` 分支。
3. 让团队成员进行代码审查（Code Review）。
4. 通过后，选择 **Merge** 按钮（可以选择 `Squash and merge`、`Rebase and merge` 等方式）。




## **1. `merge` 和 `rebase` 的区别**

![[Pasted image 20250322102603.png]]
![[Pasted image 20250322102831.png]]
## **2. 先 rebase 再 merge**

有时，我们想要保持历史的清晰度，同时也避免直接推送 `rebase` 后的代码影响别人，可以采用 **“先 rebase 再 merge”** 的方式：

1. **先变基**（rebase）：保证你的分支基于最新的 `main`，提交历史线性化。
2. **再合并**（merge）：避免 fast-forward 合并，保留 `feature` 分支的开发轨迹。

merge是在main分支上操作，
此时main分支清楚知道自己多了一个commit。
而rebase是变基，在feature分支操作，相当于rebase操作后feature分支改变了自己的爹(pre)，认了main分支的最新提交做爹(pre)，但main分支并不知道自己多了一串儿子(commit)。所以rebase完后需要在main分支上再做一次merge操作

---

**🚨 注意：**

- **公共分支（main/dev）不要用 `rebase`**，否则会影响其他开发者的历史记录。
- **自己的 feature 分支用 `rebase`**，让代码基于最新 `main`，减少合并冲突。
- merge的情况则相反

---

对于多人协作，建议：

1. **日常开发：本地 `git rebase` 让 `feature` 保持最新**。
2. **合并到 `main` 时：先 `rebase` 再 `merge --no-ff`，保持历史清晰**。
3. **不要在公共分支 `rebase`，避免影响别人**。


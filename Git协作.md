
- 创建自己的分支，在在自己的分支工作
- 完成一个小功能就要提交，降低冲突可能性
- 每次开始前都要pull同步本地代码
- 每次push建立push request


## 开发流程：

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

# 4. 提交代码
git add    # 将改动的文件加入暂存区
git commit -m "实现 XYZ 功能"

# 5. 检查main分支是否更新，如果更新了
git checkout main          # 切换到 main
git pull origin main       # 拉取远程最新 main 代码

git checkout feature-xyz   # 切换回 feature-xyz
git merge main             # 合并 main 到 feature-xyz，有冲突则解决冲突
# 解决冲突后，没有则不用做
git add .
git commit -m "fix: 解决 main 合并到 feature-xyz 的冲突"

# 6. 推送到远程仓库
git push origin feature-xyz

# 7. 在 GitHub/GitLab 创建 PR 并合并
# 在github上操作

# 8.如果代码需要修改
git checkout feature-xyz    # 确保在当前分支
git pull origin feature-xyz # 拉取最新修改（如果有）
# 进行代码修改...
git add .                   # 添加修改
git commit -m "fix: 根据代码评审修改 feature-xyz"
git push origin feature-xyz  # 推送修改

```

- **保持 `main` 干净**：不要在 `main` 上直接开发新功能，始终创建新的 `feature` 分支。
- **小步提交**：每次开发完成一个小功能就 `commit`，方便回滚。

## **创建 Pull Request (PR) 并合并**

在 GitHub/GitLab 上：

1. 进入仓库，找到 `feature-xyz` 分支。
2. 创建 **Pull Request (PR)**，请求合并到 `main` 分支。
3. 让团队成员进行代码审查（Code Review）。
4. 通过后，选择 **Merge** 按钮（可以选择 `Squash and merge`、`Rebase and merge` 等方式）。

## **不同的merge方案**

![[Pasted image 20250322102603.png]]
![[Pasted image 20250322102831.png]]


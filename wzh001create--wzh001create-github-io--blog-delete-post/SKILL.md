---
name: blog-delete-post
description: Delete blog posts with interactive selection, automatic cleanup of images, and immediate deployment Use when this capability is needed.
metadata:
  author: wzh001create
---

# Blog Delete Post Skill

Delete blog posts with interactive selection, automatic cleanup of associated images, and immediate deployment to GitHub.

## What I do

- List all blog posts with titles and file names
- Let users select which post(s) to delete via number, title, or keyword
- Remove both the markdown file and associated image directories
- Automatically commit and push changes to GitHub
- Confirm successful deletion and deployment

## When to use me

Use this skill when the user wants to:
- Remove a blog post from the website
- Delete old, outdated, or unwanted articles
- Clean up test posts or drafts
- Unpublish content

## Trigger phrases (examples)

- "删除文章"
- "删除 Docker 那篇文章"
- "帮我移除某篇博客文章"
- "delete the kubernetes post"
- "remove an article from my blog"
- "我想删掉一篇文章"

## How I work

### Step 1: ALWAYS list all blog posts first

**IMPORTANT: You MUST always display the complete list of articles, even if the user specified a keyword in their request. This gives the user context and confirms what will be deleted.**

Use the Bash and Read tools to discover all posts:

```bash
cd ~/wzh_blog/my-blog/content/posts
ls -1 *.md
```

For each `.md` file found, extract the title from front matter:

```bash
grep "^title:" <filename> | head -1
```

Present the list to the user in a clean, numbered format:

```
📚 当前博客文章列表：

1. Docker 化迁移实战：将 C++ 项目容器化
   文件: my-first-post.md
   
2. Kubernetes 入门教程
   文件: kubernetes-tutorial.md
   
3. Python 最佳实践指南
   文件: python-best-practices.md

共 3 篇文章
```

### Step 2: Get user selection

Ask the user which post to delete. Accept multiple input formats:

- **By number**: "1" or "2"
- **By title**: "Docker 化迁移实战" or "docker"
- **By filename**: "my-first-post.md" or "my-first-post"
- **By keyword**: "kubernetes" (matches title)

If the user already specified in their original request (e.g., "删除 Docker 那篇文章"), match it against the list automatically.

**Matching logic:**
- Exact number match → select that post
- Exact filename match (with or without .md) → select that post
- Partial title match (case-insensitive) → select that post
- If multiple matches, show them and ask user to clarify
- If no match, show error and ask again

### Step 3: Confirm deletion

Before deleting, always confirm with the user:

```
⚠️  确认删除以下文章？

标题: Kubernetes 入门教程
文件: content/posts/kubernetes-tutorial.md
图片: static/images/kubernetes-tutorial/ (如果存在)

此操作将：
  1. 删除 markdown 文件
  2. 删除图片目录（如果存在）
  3. 自动提交并推送到 GitHub
  4. 文章将从网站上移除

确认删除？(y/N): 
```

Only proceed if user confirms with 'y' or 'yes'.

### Step 4: Delete files

**Delete the markdown file:**

```bash
cd ~/wzh_blog/my-blog
rm "content/posts/<filename>"
```

**Check and delete image directory:**

```bash
# Check if image directory exists
if [ -d "static/images/<article-name>/" ]; then
  rm -rf "static/images/<article-name>/"
  echo "✓ 已删除图片目录"
else
  echo "ℹ️  没有找到对应的图片目录"
fi
```

The article name is the filename without the `.md` extension.

### Step 5: Commit and push to GitHub

Automatically commit and push the changes:

```bash
cd ~/wzh_blog/my-blog
git add -A
git commit -m "chore: 删除文章《<article-title>》"
git push
```

**Important:** Use the article title in the commit message, not the filename.

### Step 6: Confirm success

Show a clear success message:

```
✅ 删除成功！

✓ 已删除 content/posts/kubernetes-tutorial.md
✓ 已删除 static/images/kubernetes-tutorial/
✓ 已提交更改到 Git
✓ 已推送到 GitHub
🚀 GitHub Actions 正在部署...

文章已从博客移除，几分钟后生效：
https://wzh001create.github.io/

剩余文章数: 2
```

## Important paths and files

- **Blog root directory**: `~/wzh_blog/my-blog/`
- **Posts directory**: `~/wzh_blog/my-blog/content/posts/`
- **Images directory**: `~/wzh_blog/my-blog/static/images/`
- **Blog URL**: `https://wzh001create.github.io/`

## Error handling

### No posts found

If `content/posts/` is empty or has no `.md` files:

```
ℹ️  博客中没有文章

你可以使用 blog-import-post skill 来添加文章。
```

### User cancels

If user says 'n' or 'no' to confirmation:

```
✓ 已取消删除操作

文章未被删除。
```

### File not found

If the selected file doesn't exist (shouldn't happen, but handle gracefully):

```
❌ 错误: 文件不存在

content/posts/<filename> 未找到。
请重新运行并选择有效的文章。
```

### Git push fails

If git push fails (network issue, auth problem, etc.):

```
❌ 推送失败

文件已在本地删除，但未能推送到 GitHub。
错误信息: <error-output>

请检查网络连接和 Git 认证，然后手动推送：
  cd ~/wzh_blog/my-blog
  git push
```

## Important notes

- **ALWAYS list all posts first** (even if user specified a keyword) - this is a REQUIREMENT
- Always confirm before deleting (no silent deletions)
- Always auto-commit and auto-push (as per user requirement)
- Use the article's title in commit messages, not the filename
- Handle Chinese characters properly in all operations
- Check if image directory exists before trying to delete it
- Show clear feedback for every step
- Token optimization: Use grep to extract titles, don't read entire files

## Example interaction 1: Simple deletion

**User input:**
```
删除文章
```

**Your response:**
```
📚 当前博客文章列表：

1. Docker 化迁移实战：将 C++ 项目容器化 (my-first-post.md)
2. Kubernetes 入门教程 (kubernetes-tutorial.md)

请输入要删除的文章序号或标题: 
```

**User input:**
```
2
```

**Your response:**
```
⚠️  确认删除《Kubernetes 入门教程》？

文件: content/posts/kubernetes-tutorial.md
图片: static/images/kubernetes-tutorial/

确认删除？(y/N): 
```

**User input:**
```
y
```

**Your response:**
```
正在删除文章...

✓ 已删除 content/posts/kubernetes-tutorial.md
✓ 已删除 static/images/kubernetes-tutorial/
✓ 已提交: chore: 删除文章《Kubernetes 入门教程》
✓ 已推送到 GitHub
🚀 GitHub Actions 正在部署...

✅ 删除成功！文章已从博客移除：
https://wzh001create.github.io/
```

## Example interaction 2: Direct keyword deletion

**User input:**
```
删除 Docker 那篇文章
```

**Your response:**
```
📚 找到匹配的文章：

1. Docker 化迁移实战：将 C++ 项目容器化 (my-first-post.md)

⚠️  确认删除《Docker 化迁移实战：将 C++ 项目容器化》？

文件: content/posts/my-first-post.md
图片: static/images/my-first-post/ (如果存在)

确认删除？(y/N): 
```

## Tools you should use

- **Bash**: To list posts, delete files, and run git commands
- **Read**: To extract titles from front matter
- **Glob**: To find all .md files in content/posts/ (optional, can use bash ls)
- **Question**: To get user input if their selection is unclear

Remember: 
- Always show what you're doing with clear status messages
- Be safe: always confirm before deleting
- Be thorough: clean up both files and images
- Be automatic: commit and push without asking (as per requirement)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wzh001create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

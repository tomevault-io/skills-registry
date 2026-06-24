---
name: review-standards
description: Review and update project coding standards based on recent code changes / 最近のコード変更に基づいてプロジェクトのコーディング規約をレビュー・更新 Use when this capability is needed.
metadata:
  author: tsuchiya2
---

# Review Coding Standards / コーディング規約のレビュー

**Purpose**: Analyze recent code changes and interactively update coding standards in `.claude/skills/`
**目的**: 最近のコード変更を分析し、`.claude/skills/` のコーディング規約を対話的に更新

**When to use / いつ使用するか**:
- After completing a sprint / スプリント終了後
- After major refactoring / 大規模なリファクタリング後
- When team decides to adopt new patterns / チームで新しいパターンの採用を決定したとき
- Monthly or quarterly standards review / 月次または四半期ごとの規約レビュー

---

## Step 1: Scan Existing Standards / 既存規約のスキャン

```typescript
const fs = require('fs')
const path = require('path')

console.log('📚 Scanning existing coding standards...')
console.log('   既存のコーディング規約をスキャン中...\n')

// Check if skills directory exists
const skillsDir = '.claude/skills'
if (!fs.existsSync(skillsDir)) {
  console.log('⚠️  No coding standards found in .claude/skills/')
  console.log('   .claude/skills/ に規約が見つかりません\n')
  console.log('💡 Run /setup to create coding standards first')
  console.log('   まず /setup を実行して規約を作成してください\n')
  return
}

// Find all SKILL.md files
const standardDirs = fs.readdirSync(skillsDir, { withFileTypes: true })
  .filter(dirent => dirent.isDirectory())
  .map(dirent => dirent.name)

const existingStandards = []

for (const dir of standardDirs) {
  const skillPath = path.join(skillsDir, dir, 'SKILL.md')
  if (fs.existsSync(skillPath)) {
    existingStandards.push({
      name: dir,
      path: skillPath,
      label: dir.split('-').map(w => w.charAt(0).toUpperCase() + w.slice(1)).join(' ')
    })
  }
}

if (existingStandards.length === 0) {
  console.log('⚠️  No coding standards found')
  console.log('   規約が見つかりません\n')
  console.log('💡 Run /setup to create coding standards first')
  console.log('   まず /setup を実行して規約を作成してください\n')
  return
}

console.log(`📊 Found ${existingStandards.length} coding standard(s):`)
console.log(`   ${existingStandards.length} 個の規約を検出:\n`)

for (const standard of existingStandards) {
  console.log(`   ✅ ${standard.label} (${standard.name})`)
}
console.log('')
```

---

## Step 2: Analyze Recent Code Changes / 最近のコード変更を分析

```typescript
console.log('🔍 Analyzing recent code changes...')
console.log('   最近のコード変更を分析中...\n')

// Ask user how many commits to analyze
const commitResponse = await AskUserQuestion({
  questions: [
    {
      question: "How many recent commits should be analyzed? / 何件の最近のコミットを分析しますか？",
      header: "Commits",
      multiSelect: false,
      options: [
        {
          label: "Last 10 commits / 直近10件",
          description: "Quick review of recent changes / 最近の変更を素早くレビュー"
        },
        {
          label: "Last 20 commits / 直近20件",
          description: "Standard review (Recommended) / 標準的なレビュー（推奨）"
        },
        {
          label: "Last 50 commits / 直近50件",
          description: "Comprehensive review / 包括的なレビュー"
        },
        {
          label: "Since last review / 前回のレビュー以降",
          description: "Analyze all changes since last standards update / 前回の規約更新以降のすべての変更を分析"
        }
      ]
    }
  ]
})

let commitCount = 20
const answer = commitResponse.answers['0']
if (answer.includes('Last 10')) {
  commitCount = 10
} else if (answer.includes('Last 20')) {
  commitCount = 20
} else if (answer.includes('Last 50')) {
  commitCount = 50
} else if (answer.includes('Since last review')) {
  // Get last review date from standards files
  const lastReviewDate = await bash(`grep -h "Last Updated" ${skillsDir}/*/SKILL.md | sort -r | head -1 | cut -d':' -f2- | tr -d ' '`)
  if (lastReviewDate.output.trim()) {
    console.log(`   Last review: ${lastReviewDate.output.trim()}`)
    console.log(`   前回のレビュー: ${lastReviewDate.output.trim()}\n`)
  }
  commitCount = 100 // Fallback to 100 if date parsing fails
}

// Get recent commits
const gitLogResult = await bash(`git log -${commitCount} --pretty=format:"%H %s" --name-only`)

if (gitLogResult.exitCode !== 0) {
  console.log('⚠️  Git repository not found or no commits')
  console.log('   Gitリポジトリが見つからないか、コミットがありません\n')
  return
}

// Parse changed files
const changedFilesSet = new Set()
const lines = gitLogResult.output.split('\n')
for (const line of lines) {
  if (line && !line.match(/^[a-f0-9]{40}/) && !line.match(/^$/)) {
    // This is a file path
    changedFilesSet.add(line.trim())
  }
}

const changedFiles = Array.from(changedFilesSet)

console.log(`📝 Analyzed ${commitCount} commits`)
console.log(`   ${commitCount} 件のコミットを分析しました`)
console.log(`📁 Found ${changedFiles.length} changed files`)
console.log(`   ${changedFiles.length} 個の変更されたファイルを検出\n`)
```

---

## Step 3: Launch Standards Review Agent / 規約レビューエージェントの起動

```typescript
console.log('🤖 Launching standards review agent...')
console.log('   規約レビューエージェントを起動中...\n')

// Ask which standards to review
const standardsResponse = await AskUserQuestion({
  questions: [
    {
      question: "Which standards do you want to review? / どの規約をレビューしますか？",
      header: "Standards",
      multiSelect: true,
      options: existingStandards.map(std => ({
        label: std.label,
        description: `Review ${std.name} based on recent code changes / 最近のコード変更に基づいて ${std.name} をレビュー`
      }))
    }
  ]
})

const selectedStandards = []
const selectedLabels = standardsResponse.answers['0']

for (const std of existingStandards) {
  if (selectedLabels.includes(std.label)) {
    selectedStandards.push(std)
  }
}

if (selectedStandards.length === 0) {
  console.log('⏭️  No standards selected. Exiting.')
  console.log('   規約が選択されませんでした。終了します。\n')
  return
}

console.log(`\n🎯 Reviewing ${selectedStandards.length} standard(s)...`)
console.log(`   ${selectedStandards.length} 個の規約をレビュー中...\n`)

// Launch review agent for each selected standard
for (const standard of selectedStandards) {
  console.log(`\n📖 Reviewing ${standard.label}...`)
  console.log(`   ${standard.label} をレビュー中...\n`)

  // Determine relevant file patterns
  let filePatterns = '**/*.{ts,tsx,js,jsx}'
  if (standard.name.includes('typescript')) {
    filePatterns = '**/*.ts'
  } else if (standard.name.includes('react')) {
    filePatterns = '**/*.tsx'
  } else if (standard.name.includes('python')) {
    filePatterns = '**/*.py'
  } else if (standard.name.includes('go')) {
    filePatterns = '**/*.go'
  } else if (standard.name.includes('rust')) {
    filePatterns = '**/*.rs'
  } else if (standard.name.includes('test')) {
    filePatterns = '**/*.{test,spec}.{ts,tsx,js,jsx,py}'
  }

  // Filter changed files by pattern
  const relevantFiles = changedFiles.filter(file => {
    if (standard.name.includes('typescript') && file.endsWith('.ts') && !file.endsWith('.tsx')) return true
    if (standard.name.includes('react') && file.endsWith('.tsx')) return true
    if (standard.name.includes('python') && file.endsWith('.py')) return true
    if (standard.name.includes('go') && file.endsWith('.go')) return true
    if (standard.name.includes('rust') && file.endsWith('.rs')) return true
    if (standard.name.includes('test') && (file.includes('.test.') || file.includes('.spec.'))) return true
    if (standard.name.includes('security')) return true // All files relevant for security
    return false
  })

  if (relevantFiles.length === 0) {
    console.log(`   ⏭️  No relevant files changed for ${standard.label}`)
    console.log(`   ${standard.label} に関連するファイルの変更がありません\n`)
    continue
  }

  console.log(`   📁 Analyzing ${relevantFiles.length} relevant file(s)...`)
  console.log(`   ${relevantFiles.length} 個の関連ファイルを分析中...\n`)

  const reviewResult = await Task({
    subagent_type: 'general-purpose',
    model: 'sonnet',
    description: `Review ${standard.name}`,
    prompt: `You are a coding standards expert. Review and update coding standards based on recent code changes.

**Task**: Review ${standard.label} and detect pattern deviations

**Standard Path**: ${standard.path}
**Changed Files**: ${relevantFiles.slice(0, 20).join(', ')}${relevantFiles.length > 20 ? ` (and ${relevantFiles.length - 20} more)` : ''}

**Instructions**:

1. **Read Current Standard** (use Read tool):
   - Read ${standard.path}
   - Understand current naming conventions, patterns, and rules

2. **Analyze Changed Files** (use Read tool):
   - Read 5-10 representative files from the changed files list
   - Focus on recent patterns and conventions
   - Look for:
     - Naming convention changes
     - New error handling patterns
     - New file structure approaches
     - New testing patterns
     - New documentation styles

3. **Detect Deviations**:
   - Compare recent code patterns with current standards
   - Identify meaningful deviations (ignore minor variations)
   - Focus on patterns that appear in multiple files (not one-offs)

4. **Interactive Review with User** (use AskUserQuestion):
   - For each significant deviation, ask user:
     - Show the current standard
     - Show the new pattern detected
     - Show example files
     - Ask: "Update standard to include this pattern?"
   - Ask if user wants to add, modify, or skip each detected pattern

5. **Update Standard** (use Edit tool):
   - For approved changes:
     - Update the relevant sections in SKILL.md
     - Add new examples from the codebase
     - Update "Last Updated" timestamp
     - Add change log entry at the bottom
   - Preserve existing good patterns
   - Don't remove rules unless user explicitly requests

**Important**:
- Be conservative: Only suggest updates for clear, repeated patterns
- Ask user for confirmation before making changes
- Show concrete examples from the codebase
- Update "Last Updated" field to current date
- Add change history at the end of SKILL.md

**Current Date**: ${new Date().toISOString().split('T')[0]}
**Current Working Directory**: ${process.cwd()}
`
  })

  console.log(`   ✅ ${standard.label} review completed`)
  console.log(`   ${standard.label} のレビューが完了しました\n`)
}

console.log('\n✅ All standards reviewed!')
console.log('   すべての規約のレビューが完了しました！\n')
```

---

## Step 4: Summary / まとめ

```typescript
console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━')
console.log('📊 Standards Review Summary / 規約レビューのまとめ')
console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n')

console.log(`✅ Reviewed ${selectedStandards.length} standard(s)`)
console.log(`   ${selectedStandards.length} 個の規約をレビューしました\n`)

console.log('📁 Updated standards:')
console.log('   更新された規約:\n')

for (const standard of selectedStandards) {
  console.log(`   - ${standard.label}: ${standard.path}`)
}

console.log('\n💡 Next steps / 次のステップ:')
console.log('   1. Review the updated SKILL.md files')
console.log('      更新されたSKILL.mdファイルを確認')
console.log('   2. Commit the changes to git')
console.log('      変更をgitにコミット')
console.log('   3. Share updates with your team')
console.log('      チームに更新を共有')
console.log('   4. Run /review-standards regularly (monthly/quarterly)')
console.log('      定期的に /review-standards を実行（月次/四半期）\n')

console.log('🎉 Standards review complete!')
console.log('   規約レビュー完了！\n')
```

---

## Notes / 注意事項

**Frequency / 実行頻度**:
- **Recommended / 推奨**: Monthly or after each sprint / 月次またはスプリント終了後
- **Minimum / 最低**: Quarterly / 四半期ごと
- **Ad-hoc / 臨時**: After major refactoring or when adopting new patterns / 大規模リファクタリング後または新パターン採用時

**What gets updated / 更新される内容**:
- Naming conventions / 命名規則
- File structure patterns / ファイル構造パターン
- Error handling approaches / エラー処理アプローチ
- Testing patterns / テストパターン
- Documentation styles / ドキュメントスタイル
- Real code examples / 実際のコード例

**What doesn't get updated / 更新されない内容**:
- Core principles (unless team decides) / コア原則（チーム決定時を除く）
- Security rules (require explicit review) / セキュリティルール（明示的なレビューが必要）
- One-off patterns (need repetition to become standard) / 一回限りのパターン（標準になるには繰り返しが必要）

---

**For more information / 詳細情報**:
- Coding standards location / 規約の場所: `.claude/skills/`
- Setup command / セットアップコマンド: `/setup`
- EDAF documentation / EDAFドキュメント: `.claude/skills/edaf-orchestration/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tsuchiya2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: zenn-article-writer
description: Write high-quality Zenn technical articles following Japanese article writing best practices, focusing on problem-solving narratives rather than technology showcases Use when this capability is needed.
metadata:
  author: nogu66
---

# Zenn Technical Article Writer

This skill enables Claude to write high-quality technical articles for Zenn.dev following established Japanese technical writing best practices.

## Author Introduction

**こんんちは、noguです。**

X (Twitter): https://x.com/_nogu66

## Core Philosophy

**Technology is a means to solve problems, not the end goal.**

The primary approach is: **"This solution solves this problem" > "This technology is amazing"**

### Target Reader

The most important target reader is **"your past self who faced this problem"**. Write as if explaining to yourself from the past:
- What problem were you facing?
- What did you struggle with?
- How did you solve it?

Secondary targets are colleagues and community members who might face similar challenges.

## When to Use This Skill

Use this skill when:
- Creating a new Zenn article
- The user asks to write about a technical topic
- Documenting a solution to a problem
- Sharing technical knowledge or experience

## Article Structure

**記事の詳細な構造とテンプレートは `ARTICLE_TEMPLATE.md` を参照してください。**

このテンプレートには以下が含まれています:
- 記事の完全な構成（タイトル、はじめに、問題提起、解決策、結果、おわりに）
- 各セクションの具体的なフォーマットと例
- 必須チェックリスト
- よく使うフレーズ集

### 記事構成の概要

1. **Title (タイトル)**: 問題と解決策の技術を含める
2. **Author Introduction (自己紹介)**: noguの紹介とXリンク
3. **Introduction (はじめに)**: 対象読者、記事のスコープ、読者が得られるもの
4. **Problem Statement (問題提起)**: 共感できる具体的な課題の説明
5. **Solution Overview (解決策の概要)**: 技術の簡潔な紹介
6. **Detailed Explanation (詳細説明)**: What/Why/Howの構造で説明
7. **Results/Outcomes (やってみた結果)**: 実際の成果と学び
8. **Conclusion (おわりに)**: まとめ、次のステップ、フォローCTA（必須）
9. **References (参考リンク)**: 重要な情報源のリスト

### 重要なポイント

- すべてのコード例は動作確認済みであること
- 一文一義を徹底すること
- What/Why/Howを明確に説明すること
- 記事の最後に必ずフォローCTAを含めること

## Writing Style Guidelines

### Language Rules

1. **One Idea Per Sentence (一文一義)**
   - Keep each sentence focused on a single idea
   - Break complex sentences into multiple simple ones

2. **Active Voice Over Passive**
   - Good: "この関数は値を返します"
   - Bad: "値が返されます"

3. **Define Technical Terms**
   - On first use, provide a brief explanation
   - Use parentheses for clarification: "MCP (Model Context Protocol)"

4. **Be Concrete**
   - Use specific examples over abstract descriptions
   - Show, don't just tell

### Tone

- Professional but approachable
- Empathetic to reader's challenges
- Confident but humble
- Encouraging and supportive

### Common Phrases

**Problem introduction**:
- "こんな経験はありませんか?"
- "〜で困っていませんか?"

**Solution introduction**:
- "[Technology]は、この問題を解決します。"
- "[Technology]を使えば、[problem]を[solution]できます。"

**Explanation transitions**:
- "具体的には..."
- "つまり..."
- "要するに..."

**Encouragement**:
- "実際にやってみましょう"
- "早速試してみます"

## Zenn-Specific Format

### Frontmatter

Always include proper frontmatter:

```yaml
---
title: "Article title (max 50 chars recommended)"
emoji: "🛠️"  # Choose relevant emoji
type: "tech"  # or "idea"
topics: ["topic1", "topic2", "topic3"]  # 1-5 topics, lowercase
published: false  # Set true when ready to publish
---
```

### Topics Selection

Choose 1-5 relevant topics (lowercase):
- Technology names: "typescript", "react", "claude"
- Categories: "ai", "開発効率化", "自動化"
- Platforms: "zenn", "github"

### Emoji Selection

Choose an emoji that represents:
- The main technology (🤖 for AI, ⚛️ for React)
- The action (🛠️ for tools, 📝 for writing)
- The feeling (✨ for excitement, 🎉 for celebration)

## Quality Checklist

Before completing an article, verify:

- [ ] Title includes both problem and solution technology
- [ ] Introduction clearly states target readers, scope, and benefits
- [ ] Problem statement is relatable and concrete
- [ ] All code examples have been tested and work
- [ ] What/Why/How is explained for each major point
- [ ] Conclusion summarizes key takeaways
- [ ] Next steps are actionable and clear
- [ ] All technical terms are defined on first use
- [ ] One idea per sentence throughout
- [ ] References include all important sources
- [ ] Frontmatter is complete and accurate

## Anti-Patterns to Avoid

**Don't**:
- Write technology showcases without problem context
- Use passive voice excessively
- Include untested code examples
- Write overly long sentences with multiple ideas
- Skip the "why" and only explain "what" and "how"
- Forget to define the target reader
- Write conclusions that are just summaries without next steps

**Do**:
- Focus on problem-solving narratives
- Use active voice and concrete examples
- Test all code before including it
- Keep sentences simple and focused
- Always explain the reasoning behind choices
- Clearly define who will benefit from reading
- Give readers actionable next steps

## Example Application

When asked to write an article about a new technology:

1. **First, identify the problem**: What pain point does this technology solve?
2. **Define the reader**: Who struggles with this problem?
3. **Structure the narrative**: Past problem → Solution → Implementation → Results
4. **Write with empathy**: Remember what it was like before you knew the solution
5. **Provide value**: Ensure readers can immediately apply what they learn

## Integration with Zenn CLI

When creating articles, use:

```bash
# Create new article
npx zenn new:article

# Preview locally
npx zenn preview
```

The article will be created in `articles/` directory with a random ID filename.

## Remember

The goal is not to show off technology or your knowledge. The goal is to **help your past self and others solve real problems**. Write the article you wish you had found when you were struggling with this issue.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nogu66) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

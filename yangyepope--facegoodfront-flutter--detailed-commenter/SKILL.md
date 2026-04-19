---
name: detailed-commenter
description: Ensures all code written or modified has detailed comments. Invoke when writing code to add comprehensive documentation in the user's preferred language. Use when this capability is needed.
metadata:
  author: yangyepope
---

# Detailed Commenter

This skill ensures that all code generated or modified includes detailed and comprehensive comments.

## Instructions

When writing or modifying code, you MUST:

1.  **Class-Level Comments**: Add a detailed description above every class explaining its purpose, usage, and any key concepts.
2.  **Method-Level Comments**: Add a description above every method explaining what it does, its parameters, return values, and any side effects.
3.  **Inline Comments**: Add comments inside methods to explain complex logic, key steps, or specific variable assignments.
4.  **Language**: Use the same language as the user's request (or the existing project language) for comments. Since the user specifically requested this in Chinese context, prioritize **Chinese comments** unless instructed otherwise.

## Guidelines

- **Be Verbose**: It is better to over-explain than under-explain.
- **Explain 'Why'**: Don't just explain what the code does (which is visible), explain *why* it does it that way.
- **Update Comments**: If you modify code, ensure the surrounding comments are updated to reflect the changes.

## Example (Dart/Flutter)

```dart
/// 瀑布流内容网格 Widget
/// 使用 flutter_staggered_grid_view 实现不等高的卡片布局
class FeedGrid extends StatelessWidget {
  const FeedGrid({super.key});

  @override
  Widget build(BuildContext context) {
    // 模拟的数据列表
    // 包含高度、标题、热度等信息
    final List<Map<String, dynamic>> items = [
      {'height': 200.0, 'title': '妆容教程', 'likes': '1.2w'},
    ];

    return SliverPadding(
      padding: const EdgeInsets.symmetric(horizontal: 16),
      // 使用 MasonryGrid 实现交错布局
      sliver: SliverMasonryGrid.count(
        crossAxisCount: 2, // 设置两列
        mainAxisSpacing: 16, // 主轴间距
        crossAxisSpacing: 16, // 交叉轴间距
        childCount: items.length,
        itemBuilder: (context, index) {
          return _buildFeedCard(items[index]);
        },
      ),
    );
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yangyepope) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

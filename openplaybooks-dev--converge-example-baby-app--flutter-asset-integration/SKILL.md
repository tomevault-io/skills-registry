---
name: flutter-asset-integration
description: Wire a generated SVG asset into the Flutter codebase. Creates a typed SvgPicture wrapper widget at lib/widgets/assets/<id>_asset.dart, ensures pubspec.yaml declares the asset directory, and swaps placeholder widgets at the usage sites named in requirements.json. Use when this capability is needed.
metadata:
  author: openplaybooks-dev
---

# flutter-asset-integration

Turn a generated asset on disk into a Flutter widget that screens can `import` and render. Idempotent: safe to re-run when assets change.

## Inputs

- `outputPath` — the SVG file (e.g. `assets/illustrations/baby-sizes/week-12.svg`)
- `<outputPath>.meta.json` — sidecar with alt-text and dimensions
- `requirements.json` (from `asset-requirements-analysis`) — lists usage sites
- `assetId`, `assetWidgetName`, `dimensionWidth`, `dimensionHeight` — passed via task vars

## Output

1. **`lib/widgets/assets/<assetId>_asset.dart`** — a Material widget wrapping `SvgPicture.asset`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_svg/flutter_svg.dart';

/// {{alt_text from .meta.json}}
class {{assetWidgetName}}Asset extends StatelessWidget {
  const {{assetWidgetName}}Asset({super.key, this.width, this.height, this.semanticsLabel});

  final double? width;
  final double? height;
  final String? semanticsLabel;

  @override
  Widget build(BuildContext context) {
    return SvgPicture.asset(
      '{{outputPath}}',
      width: width ?? {{dimensionWidth}}.0,
      height: height ?? {{dimensionHeight}}.0,
      semanticsLabel: semanticsLabel ?? '{{alt_text}}',
    );
  }
}
```

2. **`pubspec.yaml`** — ensure the asset directory is listed under `flutter.assets`. Skip if already present.

3. **Usage swap** — for each entry in `requirements.usage`, replace placeholder widget references (`Placeholder()`, `SizedBox()`, or a named widget marker) with `const {{assetWidgetName}}Asset()`. Preserve indentation and surrounding code.

## Idempotency rules

- Regex-check the target file for the import line **before** inserting; never duplicate imports.
- Don't touch lines outside the named usage site.
- Run `dart format lib/widgets/assets/<assetId>_asset.dart` after writing.
- If the wrapper widget file already exists and is identical, skip — don't overwrite.

## What this skill does NOT do

- Doesn't run `flutter pub get` (the playbook's `pub-get` goal handles that).
- Doesn't run `dart analyze` (downstream `analyze-clean` goal handles that).
- Doesn't add `flutter_svg` to `pubspec.yaml`'s dependencies (already present in baby-app's hand-written pubspec).

---
> Source: [openplaybooks-dev/converge-example-baby-app](https://github.com/openplaybooks-dev/converge-example-baby-app) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

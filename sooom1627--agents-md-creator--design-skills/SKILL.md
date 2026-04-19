---
name: design-skills
description: デジタル庁デザインシステム(DADS)に準拠したUI/UXデザインとアクセシブルなコンポーネント実装を支援。React/Next.jsでのコンポーネント作成、WCAG 2.2アクセシビリティ対応、Figmaデザインからのコード実装時に使用。Use when implementing DADS design system, creating accessible components, WCAG compliance, or building government-compliant UI. Use when this capability is needed.
metadata:
  author: sooom1627
---

# デジタル庁デザインシステム(DADS)準拠 Design Skills

このスキルは、デジタル庁デザインシステム(DADS - Digital Agency Design System)の原則とガイドラインに準拠したUI/UXデザインとアクセシブルなコンポーネント実装を支援します。

## When to Use This Skill

このスキルは以下のシナリオで使用してください：

- デジタル庁デザインシステムに準拠したUIコンポーネントを作成する時
- WCAG 2.2 レベルAAのアクセシビリティ要件を満たす必要がある時
- React/Next.jsでアクセシブルなコンポーネントを実装する時
- Figmaデザインからコードへの実装（HTML/React）を行う時
- 政府機関・公共機関向けのWebサイト/サービスを構築する時
- ユニバーサルデザインとインクルーシブデザインを実践する時
- shadcn/uiとTailwind CSSを使用してDADS準拠のコンポーネントを作成する時

## Core Concepts

### DADS (デジタル庁デザインシステム) とは

デジタル庁デザインシステムは、以下の要素で構成されるデザインアセットです：

1. **デザイン言語**: スタイリングの考え方を提供
2. **UIコンポーネント**: 情報の視覚表現とインタラクションを具現化
3. **ガイドライン**: ユーザビリティとアクセシビリティを踏まえた設計・実装のための指針

### DADSの基本理念

**「誰一人取り残されない、人に優しいデジタル化を。」**

- **アクセシビリティ最優先**: WCAG 2.2に準拠し、43項目の達成基準を満たす支援
- **ユニバーサルデザイン**: 障害の有無、年齢、環境に関わらず利用可能
- **オープンアクセス**: 行政機関だけでなく、民間事業者も無料で利用可能

### 提供されるリソース

1. **ガイドライン**: 設計・実装の指針
2. **デザインデータ**: Figmaファイル
3. **コードスニペット**: HTML版およびReact版の実装例

## DADS準拠の基本原則

### 1. アクセシビリティファースト

**WCAG 2.2 レベルAA準拠**

```markdown
✅ 必須チェック項目：
- [ ] キーボード操作のみで全機能にアクセス可能
- [ ] スクリーンリーダーで内容が理解可能
- [ ] 色だけに依存しない情報伝達
- [ ] 十分なコントラスト比（通常テキスト 4.5:1、大きなテキスト 3:1）
- [ ] フォーカス表示が明確
- [ ] エラーメッセージが具体的
- [ ] aria属性が適切に設定されている
```

### 2. デザイントークン

**カラー**
```css
/* プライマリーカラー */
--color-primary: #0057A7;        /* デジタル庁ブルー */
--color-primary-light: #E5F0F9;
--color-primary-dark: #003D75;

/* セマンティックカラー */
--color-success: #28A745;
--color-warning: #FFC107;
--color-error: #DC3545;
--color-info: #17A2B8;

/* テキストカラー */
--color-text-primary: #212529;
--color-text-secondary: #6C757D;
--color-text-disabled: #ADB5BD;

/* 背景カラー */
--color-bg-primary: #FFFFFF;
--color-bg-secondary: #F8F9FA;
--color-bg-tertiary: #E9ECEF;
```

**タイポグラフィ**
```css
/* フォントファミリー */
--font-family-base: 'Noto Sans JP', -apple-system, BlinkMacSystemFont, 'Helvetica Neue', 'Yu Gothic', YuGothic, Verdana, Meiryo, sans-serif;
--font-family-mono: 'Courier New', Consolas, Monaco, monospace;

/* フォントサイズ */
--font-size-xs: 0.75rem;   /* 12px */
--font-size-sm: 0.875rem;  /* 14px */
--font-size-base: 1rem;    /* 16px */
--font-size-lg: 1.125rem;  /* 18px */
--font-size-xl: 1.25rem;   /* 20px */
--font-size-2xl: 1.5rem;   /* 24px */
--font-size-3xl: 1.875rem; /* 30px */
--font-size-4xl: 2.25rem;  /* 36px */

/* 行間 */
--line-height-tight: 1.25;
--line-height-base: 1.5;
--line-height-relaxed: 1.75;
```

**スペーシング**
```css
--spacing-1: 0.25rem;  /* 4px */
--spacing-2: 0.5rem;   /* 8px */
--spacing-3: 0.75rem;  /* 12px */
--spacing-4: 1rem;     /* 16px */
--spacing-5: 1.25rem;  /* 20px */
--spacing-6: 1.5rem;   /* 24px */
--spacing-8: 2rem;     /* 32px */
--spacing-10: 2.5rem;  /* 40px */
--spacing-12: 3rem;    /* 48px */
--spacing-16: 4rem;    /* 64px */
```

### 3. コンポーネント設計原則

**再利用性**
- 単一責任の原則: 1つのコンポーネントは1つの責任を持つ
- Composition over Inheritance: 継承ではなく合成を優先
- Props駆動: 状態はpropsで制御可能にする

**アクセシビリティ**
- セマンティックHTML使用
- ARIA属性の適切な利用
- キーボードナビゲーション対応
- フォーカス管理

**パフォーマンス**
- 遅延読み込み対応
- 最小限の再レンダリング
- 適切なメモ化

## DADS準拠のコンポーネント実装パターン

### パターン1: ボタンコンポーネント

```typescript
// components/ui/dads-button.tsx
import { forwardRef } from 'react';
import { Slot } from '@radix-ui/react-slot';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  // ベーススタイル
  'inline-flex items-center justify-center rounded-md text-base font-medium transition-colors ' +
  'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 ' +
  'disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        primary: 'bg-[#0057A7] text-white hover:bg-[#003D75] focus-visible:ring-[#0057A7]',
        secondary: 'bg-white text-[#0057A7] border-2 border-[#0057A7] hover:bg-[#E5F0F9] focus-visible:ring-[#0057A7]',
        danger: 'bg-[#DC3545] text-white hover:bg-[#C82333] focus-visible:ring-[#DC3545]',
        ghost: 'hover:bg-[#F8F9FA] hover:text-[#212529] focus-visible:ring-[#0057A7]',
      },
      size: {
        sm: 'h-9 px-3 text-sm',
        md: 'h-11 px-6',
        lg: 'h-14 px-8 text-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean;
  isLoading?: boolean;
}

export const DadsButton = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, isLoading, children, disabled, ...props }, ref) => {
    const Comp = asChild ? Slot : 'button';

    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        disabled={disabled || isLoading}
        aria-busy={isLoading}
        {...props}
      >
        {isLoading && (
          <span className="mr-2 h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent" aria-hidden="true" />
        )}
        {children}
      </Comp>
    );
  }
);

DadsButton.displayName = 'DadsButton';
```

### パターン2: フォーム入力コンポーネント

```typescript
// components/ui/dads-input.tsx
import { forwardRef } from 'react';
import { cn } from '@/lib/utils';

export interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
  helperText?: string;
  isRequired?: boolean;
}

export const DadsInput = forwardRef<HTMLInputElement, InputProps>(
  ({ className, label, error, helperText, isRequired, id, ...props }, ref) => {
    const inputId = id || `input-${Math.random().toString(36).substr(2, 9)}`;
    const errorId = `${inputId}-error`;
    const helperId = `${inputId}-helper`;

    return (
      <div className="w-full">
        {label && (
          <label
            htmlFor={inputId}
            className="mb-2 block text-sm font-medium text-[#212529]"
          >
            {label}
            {isRequired && (
              <span className="ml-1 text-[#DC3545]" aria-label="必須">
                *
              </span>
            )}
          </label>
        )}

        <input
          id={inputId}
          ref={ref}
          className={cn(
            'flex h-11 w-full rounded-md border-2 border-[#DEE2E6] bg-white px-4 py-2 text-base',
            'transition-colors',
            'placeholder:text-[#ADB5BD]',
            'focus-visible:outline-none focus-visible:border-[#0057A7] focus-visible:ring-2 focus-visible:ring-[#0057A7] focus-visible:ring-offset-2',
            'disabled:cursor-not-allowed disabled:bg-[#E9ECEF] disabled:text-[#6C757D]',
            error && 'border-[#DC3545] focus-visible:border-[#DC3545] focus-visible:ring-[#DC3545]',
            className
          )}
          aria-invalid={error ? 'true' : 'false'}
          aria-describedby={cn(
            error && errorId,
            helperText && helperId
          )}
          aria-required={isRequired}
          {...props}
        />

        {error && (
          <p id={errorId} className="mt-2 text-sm text-[#DC3545]" role="alert">
            {error}
          </p>
        )}

        {helperText && !error && (
          <p id={helperId} className="mt-2 text-sm text-[#6C757D]">
            {helperText}
          </p>
        )}
      </div>
    );
  }
);

DadsInput.displayName = 'DadsInput';
```

### パターン3: アラート/通知コンポーネント

```typescript
// components/ui/dads-alert.tsx
import { forwardRef } from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { AlertCircle, CheckCircle, Info, AlertTriangle } from 'lucide-react';
import { cn } from '@/lib/utils';

const alertVariants = cva(
  'relative w-full rounded-md border-2 p-4 [&>svg]:absolute [&>svg]:left-4 [&>svg]:top-4 [&>svg+div]:pl-8',
  {
    variants: {
      variant: {
        info: 'border-[#17A2B8] bg-[#D1ECF1] text-[#0C5460] [&>svg]:text-[#17A2B8]',
        success: 'border-[#28A745] bg-[#D4EDDA] text-[#155724] [&>svg]:text-[#28A745]',
        warning: 'border-[#FFC107] bg-[#FFF3CD] text-[#856404] [&>svg]:text-[#FFC107]',
        error: 'border-[#DC3545] bg-[#F8D7DA] text-[#721C24] [&>svg]:text-[#DC3545]',
      },
    },
    defaultVariants: {
      variant: 'info',
    },
  }
);

const iconMap = {
  info: Info,
  success: CheckCircle,
  warning: AlertTriangle,
  error: AlertCircle,
};

export interface AlertProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof alertVariants> {
  title?: string;
}

export const DadsAlert = forwardRef<HTMLDivElement, AlertProps>(
  ({ className, variant = 'info', title, children, ...props }, ref) => {
    const Icon = iconMap[variant || 'info'];

    return (
      <div
        ref={ref}
        role="alert"
        className={cn(alertVariants({ variant }), className)}
        {...props}
      >
        <Icon className="h-5 w-5" aria-hidden="true" />
        <div>
          {title && (
            <h5 className="mb-1 font-bold leading-none tracking-tight">
              {title}
            </h5>
          )}
          <div className="text-sm [&_p]:leading-relaxed">
            {children}
          </div>
        </div>
      </div>
    );
  }
);

DadsAlert.displayName = 'DadsAlert';
```

## アクセシビリティチェックリスト

### キーボードナビゲーション

```markdown
✅ Tab順序が論理的
✅ フォーカス可能な要素が明確に視認可能
✅ Enterキー/Spaceキーで操作可能
✅ Escキーでモーダル・ドロップダウンを閉じられる
✅ 矢印キーでリスト・メニュー項目を移動可能
✅ フォーカストラップが適切に機能（モーダル内など）
```

### スクリーンリーダー対応

```markdown
✅ セマンティックHTMLを使用（button, nav, main, article等）
✅ aria-label / aria-labelledby で要素を適切にラベリング
✅ aria-describedby で補足説明を提供
✅ role属性が適切に設定
✅ aria-live で動的コンテンツの変更を通知
✅ 画像にalt属性を設定（装飾画像はalt=""）
✅ フォームの各入力欄にlabelを関連付け
```

### カラーコントラスト

```markdown
✅ 通常テキスト: 最低4.5:1
✅ 大きなテキスト（18px以上 or 14px太字以上）: 最低3:1
✅ UIコンポーネント（ボタン境界線等）: 最低3:1
✅ アクティブ状態・フォーカス状態の視覚的フィードバック
✅ 色だけに依存しない情報伝達（アイコン・テキストも併用）
```

### エラーハンドリング

```markdown
✅ エラーメッセージが具体的
✅ エラー発生箇所が明確（aria-invalid, role="alert"）
✅ エラー修正方法を提示
✅ エラーフィールドに自動フォーカス
```

## テスト戦略

### アクセシビリティテスト

```typescript
// __tests__/accessibility.test.tsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import { DadsButton } from '@/components/ui/dads-button';

expect.extend(toHaveNoViolations);

describe('DadsButton Accessibility', () => {
  it('should not have accessibility violations', async () => {
    const { container } = render(<DadsButton>送信</DadsButton>);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  it('should have proper ARIA attributes when loading', () => {
    const { getByRole } = render(<DadsButton isLoading>送信中</DadsButton>);
    const button = getByRole('button');
    expect(button).toHaveAttribute('aria-busy', 'true');
    expect(button).toBeDisabled();
  });
});
```

### キーボードナビゲーションテスト

```typescript
// __tests__/keyboard-navigation.test.tsx
import { render } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { DadsInput } from '@/components/ui/dads-input';

describe('DadsInput Keyboard Navigation', () => {
  it('should be focusable with Tab key', async () => {
    const user = userEvent.setup();
    const { getByLabelText } = render(<DadsInput label="名前" />);

    await user.tab();
    expect(getByLabelText('名前')).toHaveFocus();
  });

  it('should support keyboard input', async () => {
    const user = userEvent.setup();
    const { getByLabelText } = render(<DadsInput label="名前" />);
    const input = getByLabelText('名前');

    await user.type(input, 'テスト太郎');
    expect(input).toHaveValue('テスト太郎');
  });
});
```

## AI Assistant Instructions

このスキルが有効化された時、以下の手順に従ってください：

### 1. 要件の確認

```markdown
最初に以下を確認してください：
- [ ] 実装するコンポーネントの種類（ボタン、フォーム、ナビゲーション等）
- [ ] 必要なアクセシビリティレベル（通常はWCAG 2.2 レベルAA）
- [ ] 対象ブラウザ・デバイス
- [ ] 既存のプロジェクト構成（shadcn/ui使用の有無等）
```

### 2. 実装ワークフロー

1. **コンポーネント設計**
   - セマンティックHTMLの選択
   - ARIA属性の計画
   - キーボードナビゲーション仕様の定義

2. **スタイリング実装**
   - DADSデザイントークンを使用
   - Tailwind CSSでユーティリティクラス適用
   - レスポンシブデザイン対応

3. **アクセシビリティ実装**
   - ARIA属性の追加
   - キーボードイベントハンドラー実装
   - フォーカス管理

4. **テスト作成**
   - ユニットテスト（動作確認）
   - アクセシビリティテスト（jest-axe）
   - キーボードナビゲーションテスト

5. **ドキュメント作成**
   - Propsインターフェースの説明
   - 使用例の提供
   - アクセシビリティノートの記載

### 3. 常に実施すること (Always)

```markdown
✅ セマンティックHTMLを優先的に使用する
✅ ARIA属性を適切に設定する（role, aria-label, aria-describedby等）
✅ キーボードナビゲーションを完全にサポートする
✅ フォーカス表示を明確にする（focus-visible）
✅ カラーコントラスト比を検証する（4.5:1以上）
✅ エラーメッセージを具体的かつ明確にする
✅ ローディング状態をaria-busyで示す
✅ 無効状態をdisabled + aria-disabledで示す
✅ TypeScript strictモードでの型安全性を確保する
✅ jest-axeでアクセシビリティテストを実行する
✅ 日本語UIに最適化する（フォント、文字サイズ等）
```

### 4. 絶対にしないこと (Never)

```markdown
❌ divやspanで本来セマンティックな要素を代用しない（buttonの代わりにdiv等）
❌ 色だけで情報を伝達しない（アイコン・テキストも併用）
❌ aria属性を乱用しない（セマンティックHTMLで十分な場合は不要）
❌ tabIndexに正の値を使用しない（0, -1のみ使用）
❌ フォーカス表示を完全に削除しない（outline: noneのみは危険）
❌ アニメーションを強制しない（prefers-reduced-motionを考慮）
❌ 固定のpxサイズを多用しない（rem/emを優先）
❌ アクセシビリティテストをスキップしない
❌ 英語UIの直訳をしない（日本語として自然な表現を使用）
```

### 5. コード生成時のテンプレート

新しいコンポーネントを生成する際は、以下の構造を使用してください：

```typescript
// 1. Import statements
import { forwardRef } from 'react';
import { cn } from '@/lib/utils';

// 2. Props interface with JSDoc
export interface ComponentNameProps extends React.HTMLAttributes<HTMLElement> {
  /**
   * コンポーネントの説明
   */
  propName?: string;
}

// 3. Component implementation with forwardRef
export const ComponentName = forwardRef<HTMLElement, ComponentNameProps>(
  ({ className, ...props }, ref) => {
    return (
      <element
        ref={ref}
        className={cn('base-styles', className)}
        // ARIA attributes
        role="..."
        aria-label="..."
        {...props}
      >
        {/* Content */}
      </element>
    );
  }
);

// 4. Display name for debugging
ComponentName.displayName = 'ComponentName';
```

### 6. レビュー観点

実装後、以下の観点でセルフレビューを実施してください：

```markdown
**機能性**
- [ ] 仕様通りに動作する
- [ ] エッジケースを考慮している
- [ ] エラーハンドリングが適切

**アクセシビリティ**
- [ ] キーボードのみで操作可能
- [ ] スクリーンリーダーで内容を理解可能
- [ ] カラーコントラストが十分
- [ ] ARIA属性が適切
- [ ] jest-axeテストが全てパス

**パフォーマンス**
- [ ] 不要な再レンダリングがない
- [ ] メモ化が適切に使用されている
- [ ] バンドルサイズへの影響が最小限

**コード品質**
- [ ] TypeScript strictモードでエラーなし
- [ ] ESLintルールに準拠
- [ ] テストカバレッジ80%以上
- [ ] ドキュメントが充実
```

## 参考リソース

### 公式ドキュメント

- **DADS公式サイト**: https://design.digital.go.jp/dads/
- **アクセシビリティガイドライン**: https://design.digital.go.jp/dads/guidance/accessibility/
- **ウェブアクセシビリティ方針**: https://design.digital.go.jp/dads/webaccessibility/
- **コンポーネント一覧**: https://design.digital.go.jp/dads/components/
- **基本デザイン**: https://design.digital.go.jp/dads/foundations/

### WCAG 2.2 リソース

- **WCAG 2.2 (日本語訳)**: https://waic.jp/translations/WCAG22/
- **アクセシビリティサポーテッドなHTML**: https://waic.jp/docs/as/
- **達成基準チェックリスト**: https://waic.jp/docs/WCAG22/quickref/

### ツール

- **jest-axe**: アクセシビリティテストライブラリ
- **axe DevTools**: ブラウザ拡張機能（Chrome/Firefox）
- **WAVE**: アクセシビリティ評価ツール
- **Lighthouse**: 総合的な品質評価（アクセシビリティ含む）

## トラブルシューティング

### 問題: フォーカスリングが表示されない

**解決策**:
```css
/* focus-visibleを使用（キーボード操作時のみ表示） */
.element:focus-visible {
  outline: 2px solid #0057A7;
  outline-offset: 2px;
}

/* Tailwindの場合 */
className="focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-[#0057A7] focus-visible:ring-offset-2"
```

### 問題: スクリーンリーダーが要素を読み上げない

**解決策**:
```tsx
// セマンティックHTMLを使用
<button>送信</button>  // ✅ Good

// ARIA属性で補完
<div role="button" tabIndex={0} aria-label="送信">送信</div>  // ⚠️ 最終手段
```

### 問題: カラーコントラストが不足

**解決策**:
```typescript
// DADSカラートークンを使用すれば自動的に準拠
const textOnPrimary = 'text-white';  // #FFFFFF on #0057A7 = 8.59:1 ✅
const textOnLight = 'text-[#212529]';  // #212529 on #F8F9FA = 11.88:1 ✅

// カスタムカラーの場合は検証ツールで確認
// https://webaim.org/resources/contrastchecker/
```

### 問題: モーダルからフォーカスが抜ける

**解決策**:
```typescript
// React Hook Formの場合
import { useEffect, useRef } from 'react';
import { useFocusTrap } from '@/hooks/use-focus-trap';

export function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef<HTMLDivElement>(null);

  useFocusTrap(modalRef, isOpen);

  useEffect(() => {
    if (isOpen) {
      // 最初のフォーカス可能要素にフォーカス
      const firstFocusable = modalRef.current?.querySelector(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      (firstFocusable as HTMLElement)?.focus();
    }
  }, [isOpen]);

  return (
    <div ref={modalRef} role="dialog" aria-modal="true">
      {children}
    </div>
  );
}
```

## まとめ

このスキルを使用することで、デジタル庁デザインシステム(DADS)に準拠した高品質でアクセシブルなUIコンポーネントを効率的に実装できます。

**重要なポイント**:
1. アクセシビリティを最優先に設計・実装する
2. DADSデザイントークンを忠実に使用する
3. セマンティックHTMLとARIA属性を適切に組み合わせる
4. キーボードナビゲーションを完全にサポートする
5. jest-axeで自動テストを実施する
6. 日本語UIとして自然な表現を心がける

これらの原則に従うことで、「誰一人取り残されない、人に優しいデジタル化」を実現するWebサービスを構築できます。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sooom1627) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

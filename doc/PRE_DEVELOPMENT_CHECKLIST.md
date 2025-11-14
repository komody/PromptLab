# 開発前チェックリスト

開発を始める前に確認・準備すべき項目をまとめました。

## 📋 必須項目

### 1. プロジェクト名の統一
- [ ] **プロジェクト名を決定**: `PromptLab` または `command-lab` で統一
- [ ] リポジトリ名と一致させる
- [ ] ドメイン取得の検討（将来的に必要になる場合）

**現在の状態**: リポジトリ名は `PromptLab`、ドキュメント内では `command-lab` も使用されている

---

### 2. パッケージマネージャーの選択
- [ ] **npm** / **yarn** / **pnpm** のいずれかを選択
- [ ] チーム開発の場合は統一

**推奨**: `pnpm`（高速、ディスク容量節約）または `npm`（標準的）

---

### 3. Node.jsバージョンの固定
- [ ] `.nvmrc` ファイルを作成してNode.jsバージョンを固定
- [ ] `package.json` の `engines` フィールドで指定

**推奨**: Node.js 18.x または 20.x（LTS版）

```bash
# .nvmrc の例
18.20.0
```

```json
// package.json
{
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0"
  }
}
```

---

### 4. コード品質ツールの設定
- [ ] **ESLint** の設定（Next.js標準設定を使用）
- [ ] **Prettier** の設定（コードフォーマット統一）
- [ ] **EditorConfig** の設定（エディタ設定統一）

**推奨設定**:
```bash
npm install -D eslint eslint-config-next prettier
```

---

### 5. Git設定
- [ ] **ブランチ戦略**を決定（例: `main` / `develop` / `feature/*`）
- [ ] **コミット規約**を決定（例: Conventional Commits）
- [ ] `.gitignore` の確認（既に作成済み）

**推奨**: 
- メインブランチ: `main` または `master`
- コミット規約: `feat:`, `fix:`, `docs:`, `style:`, `refactor:`, `test:`, `chore:`

---

### 6. 環境変数の管理
- [ ] `.env.example` ファイルを作成（テンプレート）
- [ ] `.env.local` を `.gitignore` に追加（確認済み）
- [ ] 必要な環境変数をリストアップ

**必要な環境変数**:
```env
# .env.example
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

---

### 7. 型定義の設計
- [ ] 主要な型定義を事前に設計
- [ ] `types/` ディレクトリの構造を決める

**主要な型**:
- `Command` / `User` / `Category` / `Tag` など

---

## 🔧 推奨項目

### 8. 開発ツールの追加
- [ ] **Husky**（Git hooks）の設定
  - コミット前にLint/Formatチェック
- [ ] **lint-staged**（変更ファイルのみLint）
- [ ] **commitlint**（コミットメッセージの検証）

**セットアップ例**:
```bash
npm install -D husky lint-staged @commitlint/cli @commitlint/config-conventional
```

---

### 9. テスト戦略
- [ ] テストフレームワークの選択（**Vitest** 推奨）
- [ ] テストカバレッジの目標設定
- [ ] E2Eテストの検討（**Playwright** など）

**推奨**: 
- ユニットテスト: Vitest
- E2Eテスト: Playwright（必要に応じて）

---

### 10. CI/CD設定
- [ ] **GitHub Actions** のワークフロー作成
  - Lint/Testの自動実行
  - ビルドチェック
- [ ] Vercelとの連携確認

**基本的なワークフロー**:
- PR作成時にLint/Test実行
- `main`ブランチにマージ時に自動デプロイ

---

### 11. ドキュメント整備
- [ ] **README.md** の作成（プロジェクトルート）
- [ ] **CONTRIBUTING.md** の作成（コントリビューションガイド）
- [ ] **LICENSE** の選択（MIT推奨）

---

### 12. プロジェクト構造の設計
- [ ] ディレクトリ構造を決定
- [ ] コンポーネント設計方針を決める
  - アトミックデザイン / 機能別 / その他

**推奨構造**:
```
promptlab/
├── app/                    # Next.js App Router
│   ├── (auth)/            # 認証関連
│   ├── (main)/            # メイン機能
│   └── api/               # API Routes
├── components/            # Reactコンポーネント
│   ├── ui/                # shadcn/uiコンポーネント
│   └── ...                # 機能コンポーネント
├── lib/                   # ユーティリティ
│   ├── supabase/          # Supabaseクライアント
│   └── utils.ts           # ヘルパー関数
├── types/                 # TypeScript型定義
├── hooks/                 # カスタムフック
└── public/                # 静的ファイル
```

---

### 13. 状態管理の選択
- [ ] 状態管理ライブラリの選択
  - **React Context**（軽量）
  - **Zustand**（軽量、シンプル）
  - **TanStack Query**（サーバー状態管理）
  - **Redux**（大規模向け）

**推奨**: 
- サーバー状態: TanStack Query (React Query)
- クライアント状態: Zustand または React Context

---

### 14. エラーハンドリング戦略
- [ ] エラーバウンダリーの設計
- [ ] エラーロギングツールの選択（**Sentry** など）
- [ ] ユーザーフレンドリーなエラーメッセージの設計

---

### 15. パフォーマンス最適化
- [ ] 画像最適化（Next.js Image）
- [ ] コード分割戦略
- [ ] バンドルサイズの監視

---

### 16. アクセシビリティ（a11y）
- [ ] セマンティックHTMLの使用
- [ ] ARIA属性の適切な使用
- [ ] キーボードナビゲーション対応
- [ ] スクリーンリーダー対応

---

### 17. 国際化（i18n）の検討
- [ ] 多言語対応が必要か検討
- [ ] 必要に応じて `next-intl` などの導入

**現時点**: 日本語のみで開始、将来的に英語対応を検討

---

### 18. アナリティクス設定
- [ ] アクセス解析ツールの選択
  - **Vercel Analytics**（Vercel使用時）
  - **Google Analytics**
  - **Plausible**（プライバシー重視）

---

### 19. モニタリング・ロギング
- [ ] エラートラッキング（**Sentry** など）
- [ ] パフォーマンス監視
- [ ] ユーザー行動分析

---

### 20. セキュリティ設定
- [ ] Content Security Policy (CSP) の設定
- [ ] セキュリティヘッダーの確認
- [ ] 依存関係の脆弱性チェック（`npm audit`）

---

## 🚀 開発開始前の最終確認

### Supabase設定
- [ ] Supabaseアカウント作成
- [ ] プロジェクト作成
- [ ] データベーススキーマ作成（SQL実行）
- [ ] APIキーの取得

### Vercel設定（デプロイ時）
- [ ] Vercelアカウント作成
- [ ] GitHubリポジトリ連携
- [ ] 環境変数の設定

---

## 📝 チェックリストの使い方

1. **必須項目（1-7）**: 開発開始前に必ず完了
2. **推奨項目（8-20）**: プロジェクトの規模に応じて段階的に導入
3. **開発開始前の最終確認**: 実際にコードを書き始める前に確認

---

## 🎯 優先順位

### 高優先度（すぐに必要）
1. プロジェクト名の統一
2. Node.jsバージョンの固定
3. 環境変数の管理
4. コード品質ツール（ESLint, Prettier）
5. Git設定

### 中優先度（開発中に追加）
6. テスト戦略
7. CI/CD設定
8. エラーハンドリング
9. ドキュメント整備

### 低優先度（必要に応じて）
10. 国際化
11. アナリティクス
12. モニタリング

---

## 📚 参考リソース

- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [shadcn/ui](https://ui.shadcn.com)

---

**準備が整ったら開発を開始しましょう！🚀**


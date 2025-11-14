# コマンドラボ（Cursor/Claude Code版）

CursorやClaude Codeで使用するコマンド・プロンプトを管理・共有するプラットフォーム

## 📋 概要

AIコーディングアシスタント（Cursor、Claude Code）で使用するコマンドやプロンプトを：
- 📝 作成・管理
- 🔍 検索・発見
- ⭐ お気に入り登録
- 🔗 共有

## 🎯 主な機能

### コア機能
- ✅ コマンド/プロンプトの作成・編集・削除
- ✅ カテゴリ別管理（Cursor用、Claude Code用など）
- ✅ タグ機能
- ✅ 全文検索
- ✅ ワンクリックコピー

### 共有機能
- ✅ 公開/非公開設定
- ✅ いいね機能
- ✅ ブックマーク機能
- ✅ シェアリンク生成

### ユーザー機能
- ✅ アカウント登録・ログイン
- ✅ マイコマンド管理
- ✅ プロファイル設定

## 🛠️ 技術スタック

### 推奨構成
- **フロントエンド**: Next.js 14 (App Router) + TypeScript + Tailwind CSS
- **バックエンド**: Next.js API Routes
- **データベース**: Supabase (PostgreSQL)
- **認証**: Supabase Auth
- **UI**: shadcn/ui
- **デプロイ**: Vercel

### 代替案
- **軽量版**: Vite + React + Supabase
- **フルスタック**: T3 Stack (Next.js + tRPC + Prisma)

詳細は [TECH_STACK.md](./TECH_STACK.md) を参照

## 🚀 セットアップ

詳細なセットアップ手順は [SETUP_GUIDE.md](./SETUP_GUIDE.md) を参照

### クイックスタート

```bash
# プロジェクト作成
npx create-next-app@latest command-lab --typescript --tailwind --app

# 依存関係インストール
npm install @supabase/supabase-js @supabase/auth-helpers-nextjs

# 開発サーバー起動
npm run dev
```

## 📦 デプロイ

### 推奨: Vercel

1. GitHubにリポジトリをプッシュ
2. [Vercel](https://vercel.com)でアカウント作成
3. プロジェクトをインポート
4. 環境変数を設定
5. デプロイ完了！

詳細は [DEPLOYMENT.md](./DEPLOYMENT.md) を参照

## 💰 コスト見積もり

### 無料プラン（個人プロジェクト）
- **Vercel**: 無料（100GB/月帯域幅）
- **Supabase**: 無料（500MB DB、2GB ストレージ）
- **合計**: **$0/月**

### 成長時
- **Vercel Pro**: $20/月
- **Supabase Pro**: $25/月
- **合計**: **$45/月**

## 📁 プロジェクト構造

```
command-lab/
├── app/                    # Next.js App Router
│   ├── (auth)/            # 認証関連ページ
│   ├── (main)/            # メインページ
│   └── api/               # API Routes
├── components/             # Reactコンポーネント
│   ├── ui/                # UIコンポーネント
│   └── ...                # 機能コンポーネント
├── lib/                   # ユーティリティ
│   ├── supabase/          # Supabaseクライアント
│   └── utils.ts           # ヘルパー関数
└── public/                # 静的ファイル
```

## 🎨 UIデザイン

- **デザインシステム**: shadcn/ui
- **スタイリング**: Tailwind CSS
- **アイコン**: Lucide React
- **レスポンシブ**: モバイルファースト

## 🔒 セキュリティ

- ✅ Row Level Security (RLS) によるデータ保護
- ✅ 認証トークンベースのアクセス制御
- ✅ HTTPS強制
- ✅ 入力値検証（Zod）
- ✅ SQLインジェクション対策（ORM使用）

## 📈 今後の拡張予定

- [ ] コメント機能
- [ ] コレクション機能
- [ ] エクスポート/インポート機能
- [ ] API提供
- [ ] ブラウザ拡張機能
- [ ] VS Code拡張機能

## 🤝 コントリビューション

プルリクエストを歓迎します！

1. フォーク
2. フィーチャーブランチ作成 (`git checkout -b feature/AmazingFeature`)
3. コミット (`git commit -m 'Add some AmazingFeature'`)
4. プッシュ (`git push origin feature/AmazingFeature`)
5. プルリクエスト作成

## 📄 ライセンス

MIT License

## 📞 お問い合わせ

質問や提案は Issue でお願いします。

---

**Happy Coding! 🚀**


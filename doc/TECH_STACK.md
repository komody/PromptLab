# コマンドラボ（Cursor/Claude Code版）技術選定

## 概要
CursorやClaude Codeで使用するコマンド・プロンプトを管理・共有するプラットフォーム

## 技術スタック提案

### オプション1: モダンWebアプリ（推奨）

#### フロントエンド
- **Next.js 14** (App Router)
  - React 18
  - TypeScript
  - Tailwind CSS
  - shadcn/ui（UIコンポーネント）
  - 理由: SSR/SSG対応、Vercelデプロイが簡単、SEO対応

#### バックエンド
- **Next.js API Routes** (フロントエンドと統合)
  - または **tRPC** (型安全なAPI)
- **データベース**: 
  - **Supabase** (PostgreSQL + 認証 + ストレージ)
  - または **Firebase** (Firestore + Auth)
  - または **PlanetScale** (MySQL互換、サーバーレス)

#### 認証
- **NextAuth.js** (Supabase/Firebaseと統合可能)
- **Supabase Auth** (Supabase使用時)

#### デプロイ
- **Vercel** (Next.jsに最適化、無料プランあり)
- **Netlify** (代替案)

---

### オプション2: 軽量SPA

#### フロントエンド
- **Vite + React + TypeScript**
  - Tailwind CSS
  - React Router

#### バックエンド
- **Supabase** (BaaSとして使用)
  - PostgreSQL
  - 認証
  - リアルタイム機能

#### デプロイ
- **Netlify** または **Vercel** (フロントエンド)
- **Supabase** (バックエンド)

---

### オプション3: フルスタックフレームワーク

#### フレームワーク
- **T3 Stack** (Next.js + tRPC + Prisma + NextAuth)
  - 型安全なフルスタック開発
  - Prisma ORM
  - tRPCでエンドツーエンドの型安全性

#### データベース
- **PostgreSQL** (Supabase/Neon/Railway)

#### デプロイ
- **Vercel** (フロントエンド)
- **Railway** または **Render** (データベース)

---

## 機能要件

### コア機能
1. **コマンド/プロンプトの作成・編集・削除**
2. **カテゴリ・タグ管理** (Cursor用、Claude Code用など)
3. **検索・フィルタリング**
4. **お気に入り機能**
5. **コピーボタン** (ワンクリックでコピー)

### 共有機能
1. **公開/非公開設定**
2. **シェアリンク生成**
3. **いいね・ブックマーク機能**
4. **コメント機能** (オプション)

### ユーザー機能
1. **アカウント登録・ログイン**
2. **マイコマンド管理**
3. **コレクション作成**

---

## 推奨構成（オプション1）

```
frontend/
  ├── app/              # Next.js App Router
  ├── components/       # Reactコンポーネント
  ├── lib/             # ユーティリティ
  └── types/           # TypeScript型定義

backend/
  ├── api/             # API Routes
  └── db/              # データベーススキーマ

public/                # 静的ファイル
```

---

## データベーススキーマ（例）

```sql
-- ユーザー
users (
  id, email, name, avatar_url, created_at
)

-- コマンド
commands (
  id, user_id, title, content, description,
  category, tags[], is_public, likes_count,
  created_at, updated_at
)

-- いいね
likes (
  id, user_id, command_id, created_at
)

-- ブックマーク
bookmarks (
  id, user_id, command_id, created_at
)
```

---

## 開発ツール

- **ESLint + Prettier** (コード品質)
- **Husky** (Git hooks)
- **Vitest** または **Jest** (テスト)
- **Storybook** (コンポーネント開発、オプション)


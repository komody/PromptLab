# コマンドラボ本家の技術スタック調査

## 調査方法

### 1. ブラウザの開発者ツールで確認

実際のサイト（commandlab.io など）にアクセスして、以下を確認：

#### Chrome DevTools での確認手順
1. **F12** または **右クリック > 検証** で開発者ツールを開く
2. **Network** タブで読み込まれているファイルを確認
   - `.js` ファイル名からフレームワークを推測
   - `react`, `vue`, `angular` などのキーワード
   - `next`, `nuxt`, `svelte` などのフレームワーク名
3. **Sources** タブでソースコード構造を確認
4. **Elements** タブでHTML構造を確認
   - `data-reactroot` → React使用
   - `ng-app` → Angular使用
   - `v-` 属性 → Vue.js使用

#### 確認ポイント
- **JavaScriptバンドル名**: `_next/static/` → Next.js
- **CSSフレームワーク**: Tailwind CSSのクラス名、Bootstrapのクラス名
- **APIエンドポイント**: `/api/` → バックエンド構造の推測
- **認証**: Auth0、Firebase、SupabaseなどのログインUI

### 2. 公開情報の確認

- **GitHub**: オープンソースの場合、リポジトリで確認
- **ブログ/記事**: 開発者ブログや技術記事
- **Twitter/X**: 開発者の投稿
- **Aboutページ**: 技術スタックが記載されている場合あり

### 3. 一般的な推測

コマンドラボのような開発者向けツールでよく使われる技術：

#### フロントエンド
- **React + Next.js** (最も可能性が高い)
  - モダンな開発者ツールでよく採用
  - SSR/SSGでSEO対応
  - Vercelデプロイが簡単
- **Vue.js + Nuxt.js** (次点)
- **Svelte/SvelteKit** (軽量で高速)

#### スタイリング
- **Tailwind CSS** (最近のトレンド)
- **CSS Modules**
- **styled-components**

#### バックエンド
- **Node.js + Express** または **Next.js API Routes**
- **Python + Django/FastAPI**
- **Ruby on Rails**
- **Go** (パフォーマンス重視の場合)

#### データベース
- **PostgreSQL** (リレーショナルDB)
- **MongoDB** (NoSQL)
- **Supabase** (BaaS)

#### 認証
- **NextAuth.js**
- **Auth0**
- **Firebase Auth**
- **Supabase Auth**

#### デプロイ
- **Vercel** (Next.js使用時)
- **Netlify**
- **Railway**
- **AWS/GCP/Azure**

## 実際の確認コマンド

### curlでHTMLを取得
```bash
curl -s https://commandlab.io | grep -i "react\|vue\|angular\|next\|nuxt"
```

### バンドルファイルを確認
```bash
# JavaScriptファイルの内容を確認
curl -s https://commandlab.io/_next/static/chunks/main.js | head -100
```

## 推測される構成（一般的なパターン）

### パターン1: モダンな構成（最も可能性が高い）
```
フロントエンド: Next.js 13+ (App Router) + React + TypeScript
スタイリング: Tailwind CSS
バックエンド: Next.js API Routes
データベース: PostgreSQL (Supabase/Railway)
認証: NextAuth.js または Supabase Auth
デプロイ: Vercel
```

### パターン2: 軽量構成
```
フロントエンド: Vite + React + TypeScript
スタイリング: Tailwind CSS
バックエンド: Supabase (BaaS)
認証: Supabase Auth
デプロイ: Netlify
```

### パターン3: フルスタックフレームワーク
```
フレームワーク: T3 Stack (Next.js + tRPC + Prisma)
データベース: PostgreSQL
認証: NextAuth.js
デプロイ: Vercel + Railway
```

## 確認すべきURL

実際に確認する際は以下をチェック：

1. **メインサイト**: `https://commandlab.io` または `https://command-lab.io`
2. **GitHub**: `https://github.com/commandlab` または類似
3. **ドキュメント**: `/docs` や `/about` ページ
4. **API**: `/api` エンドポイント

## 参考: 類似サービスの技術スタック

### GitHub Gist
- フロントエンド: React
- バックエンド: Ruby on Rails

### CodePen
- フロントエンド: React
- バックエンド: Node.js

### JSFiddle
- フロントエンド: jQuery/Vanilla JS
- バックエンド: PHP

## まとめ

**公式情報がない場合の推奨アプローチ**:

1. **実際のサイトを確認**（開発者ツール）
2. **公開情報を探す**（GitHub、ブログ）
3. **類似サービスを参考にする**
4. **モダンな技術スタックを採用**（Next.js + Supabase など）

実際にサイトを確認して、具体的な技術スタックが判明したら、このドキュメントを更新してください。


# シンプル構成：フロントエンド + データベースのみ

## 概要

問題（コマンド/プロンプト）を出題・表示するだけのシンプルなサービス。
バックエンドAPIは不要。フロントエンドから直接データベースにアクセス。

## アーキテクチャ

```
[ユーザー]
    ↓
[フロントエンド (Next.js/React)]
    ↓
[Supabase (PostgreSQL + REST API自動生成)]
```

**バックエンドサーバー不要！**

## 技術スタック

### フロントエンド
- **Next.js 14** (App Router) + TypeScript
- **Tailwind CSS**
- **shadcn/ui** (UIコンポーネント)

### データベース
- **Supabase** (PostgreSQL)
  - REST API自動生成
  - Row Level Security (RLS) でアクセス制御
  - 認証機能（必要に応じて）

### デプロイ
- **Vercel** (フロントエンド)
- **Supabase** (データベース)

## データフロー

### 問題の取得
```typescript
// フロントエンドから直接Supabaseを呼び出し
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(url, key);

// 全問題取得
const { data: problems } = await supabase
  .from('problems')
  .select('*')
  .eq('category', 'cursor');

// 検索
const { data: results } = await supabase
  .from('problems')
  .select('*')
  .textSearch('title', searchQuery);
```

### 問題の追加（管理者のみ）
```typescript
// 管理者が問題を追加
const { data } = await supabase
  .from('problems')
  .insert({
    title: '問題タイトル',
    content: '問題内容',
    category: 'cursor',
    tags: ['tag1', 'tag2']
  });
```

## データベーススキーマ

### シンプルな構成
```sql
-- 問題テーブル
CREATE TABLE problems (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  description TEXT,
  category TEXT, -- 'cursor', 'claude-code', 'general'
  tags TEXT[],
  difficulty TEXT, -- 'easy', 'medium', 'hard'
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- インデックス（検索高速化）
CREATE INDEX idx_problems_category ON problems(category);
CREATE INDEX idx_problems_tags ON problems USING GIN(tags);
CREATE INDEX idx_problems_created_at ON problems(created_at DESC);

-- Row Level Security（全員が閲覧可能）
ALTER TABLE problems ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Problems are viewable by everyone"
  ON problems FOR SELECT USING (true);

-- 管理者のみ追加可能（必要に応じて）
CREATE POLICY "Only admins can insert"
  ON problems FOR INSERT 
  WITH CHECK (auth.jwt() ->> 'role' = 'admin');
```

## 実装例

### 1. Supabaseクライアント設定

`lib/supabase.ts`:
```typescript
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!;
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

### 2. 問題一覧ページ

`app/page.tsx`:
```typescript
import { supabase } from '@/lib/supabase';
import ProblemList from '@/components/ProblemList';

export default async function HomePage() {
  const { data: problems } = await supabase
    .from('problems')
    .select('*')
    .order('created_at', { ascending: false });

  return (
    <div className="container mx-auto p-4">
      <h1 className="text-3xl font-bold mb-6">コマンドラボ</h1>
      <ProblemList problems={problems || []} />
    </div>
  );
}
```

### 3. 検索機能

`components/SearchBar.tsx`:
```typescript
'use client';

import { useState } from 'react';
import { supabase } from '@/lib/supabase';

export default function SearchBar() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  const handleSearch = async (e: React.FormEvent) => {
    e.preventDefault();
    
    const { data } = await supabase
      .from('problems')
      .select('*')
      .or(`title.ilike.%${query}%,content.ilike.%${query}%`)
      .limit(20);

    setResults(data || []);
  };

  return (
    <form onSubmit={handleSearch}>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="問題を検索..."
        className="w-full p-2 border rounded"
      />
    </form>
  );
}
```

### 4. カテゴリフィルタ

`components/CategoryFilter.tsx`:
```typescript
'use client';

import { supabase } from '@/lib/supabase';
import { useState, useEffect } from 'react';

export default function CategoryFilter({ onFilter }: { onFilter: (category: string) => void }) {
  const [categories, setCategories] = useState<string[]>([]);

  useEffect(() => {
    // カテゴリ一覧を取得
    const fetchCategories = async () => {
      const { data } = await supabase
        .from('problems')
        .select('category')
        .not('category', 'is', null);
      
      const uniqueCategories = [...new Set(data?.map(p => p.category) || [])];
      setCategories(uniqueCategories);
    };

    fetchCategories();
  }, []);

  return (
    <div className="flex gap-2">
      <button onClick={() => onFilter('all')}>すべて</button>
      {categories.map(cat => (
        <button key={cat} onClick={() => onFilter(cat)}>
          {cat}
        </button>
      ))}
    </div>
  );
}
```

## 機能一覧

### 実装する機能
- ✅ 問題一覧表示
- ✅ カテゴリ別フィルタ
- ✅ 検索機能
- ✅ タグフィルタ
- ✅ 問題詳細表示
- ✅ ワンクリックコピー

### 不要な機能（シンプルに保つ）
- ❌ ユーザー認証（公開データのみ）
- ❌ いいね・コメント（問題出題のみ）
- ❌ ユーザー登録
- ❌ 個人データ保存

## プロジェクト構造

```
command-lab/
├── app/
│   ├── page.tsx              # ホーム（問題一覧）
│   ├── problems/
│   │   └── [id]/
│   │       └── page.tsx      # 問題詳細
│   └── layout.tsx
├── components/
│   ├── ProblemCard.tsx
│   ├── ProblemList.tsx
│   ├── SearchBar.tsx
│   └── CategoryFilter.tsx
├── lib/
│   └── supabase.ts           # Supabaseクライアント
└── types/
    └── problem.ts
```

## セットアップ手順

### 1. Next.jsプロジェクト作成
```bash
npx create-next-app@latest command-lab --typescript --tailwind --app
cd command-lab
```

### 2. Supabase設定
```bash
npm install @supabase/supabase-js
```

`.env.local`:
```env
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

### 3. データベーススキーマ作成
Supabase Dashboard > SQL Editorで上記のSQLを実行

### 4. 開発開始
```bash
npm run dev
```

## コスト

**完全無料で開始可能**:
- **Vercel**: 無料プラン（100GB/月）
- **Supabase**: 無料プラン（500MB DB、2GB ストレージ）

**問題数が少ない場合**: 無料プランで十分

## メリット

✅ **シンプル**: バックエンドコード不要  
✅ **高速**: CDN + データベース直接接続  
✅ **無料**: 小規模なら完全無料  
✅ **スケーラブル**: 必要に応じてプランアップ可能  
✅ **保守が簡単**: フロントエンドとDBのみ  

## デプロイ

### Vercel
1. GitHubにプッシュ
2. Vercelでインポート
3. 環境変数を設定
4. デプロイ完了

**バックエンドサーバー不要！**

## まとめ

**この構成で実現できること**:
- 問題の出題・表示
- 検索・フィルタ
- カテゴリ別表示
- タグ機能

**不要なもの**:
- バックエンドサーバー
- API開発
- サーバー管理
- 複雑な認証システム

**シンプルで効率的な構成です！**


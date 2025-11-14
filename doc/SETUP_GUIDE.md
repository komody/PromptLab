# セットアップガイド

## クイックスタート（Next.js + Supabase）

### 1. プロジェクト作成

```bash
# Next.jsプロジェクト作成
npx create-next-app@latest command-lab --typescript --tailwind --app

cd command-lab

# 必要なパッケージインストール
npm install @supabase/supabase-js @supabase/auth-helpers-nextjs
npm install lucide-react clsx tailwind-merge
npm install date-fns
npm install zod react-hook-form @hookform/resolvers
```

### 2. Supabaseセットアップ

1. [supabase.com](https://supabase.com)でアカウント作成
2. 新しいプロジェクト作成
3. Settings > API から以下を取得:
   - Project URL
   - anon/public key

### 3. 環境変数設定

`.env.local`ファイルを作成:

```env
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

### 4. Supabaseデータベーススキーマ

Supabase SQL Editorで実行:

```sql
-- ユーザープロファイル拡張
CREATE TABLE profiles (
  id UUID REFERENCES auth.users PRIMARY KEY,
  username TEXT UNIQUE,
  avatar_url TEXT,
  bio TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- コマンド/プロンプト
CREATE TABLE commands (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  description TEXT,
  category TEXT, -- 'cursor', 'claude-code', 'general'
  tags TEXT[], -- 配列型
  is_public BOOLEAN DEFAULT false,
  likes_count INTEGER DEFAULT 0,
  views_count INTEGER DEFAULT 0,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- いいね
CREATE TABLE likes (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  command_id UUID REFERENCES commands(id) ON DELETE CASCADE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  UNIQUE(user_id, command_id)
);

-- ブックマーク
CREATE TABLE bookmarks (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  command_id UUID REFERENCES commands(id) ON DELETE CASCADE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  UNIQUE(user_id, command_id)
);

-- インデックス作成（パフォーマンス向上）
CREATE INDEX idx_commands_user_id ON commands(user_id);
CREATE INDEX idx_commands_category ON commands(category);
CREATE INDEX idx_commands_is_public ON commands(is_public);
CREATE INDEX idx_commands_created_at ON commands(created_at DESC);
CREATE INDEX idx_likes_command_id ON likes(command_id);
CREATE INDEX idx_bookmarks_user_id ON bookmarks(user_id);

-- Row Level Security (RLS) ポリシー
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE commands ENABLE ROW LEVEL SECURITY;
ALTER TABLE likes ENABLE ROW LEVEL SECURITY;
ALTER TABLE bookmarks ENABLE ROW LEVEL SECURITY;

-- プロファイル: 全員が閲覧可能、自分のみ更新可能
CREATE POLICY "Profiles are viewable by everyone"
  ON profiles FOR SELECT USING (true);

CREATE POLICY "Users can update own profile"
  ON profiles FOR UPDATE USING (auth.uid() = id);

-- コマンド: 公開されているものは全員閲覧可能、自分のものは編集可能
CREATE POLICY "Public commands are viewable by everyone"
  ON commands FOR SELECT USING (is_public = true OR auth.uid() = user_id);

CREATE POLICY "Users can create own commands"
  ON commands FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own commands"
  ON commands FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own commands"
  ON commands FOR DELETE USING (auth.uid() = user_id);

-- いいね: 全員が閲覧可能、自分のみ作成・削除可能
CREATE POLICY "Likes are viewable by everyone"
  ON likes FOR SELECT USING (true);

CREATE POLICY "Users can create own likes"
  ON likes FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete own likes"
  ON likes FOR DELETE USING (auth.uid() = user_id);

-- ブックマーク: 自分のみ閲覧・作成・削除可能
CREATE POLICY "Users can view own bookmarks"
  ON bookmarks FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can create own bookmarks"
  ON bookmarks FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete own bookmarks"
  ON bookmarks FOR DELETE USING (auth.uid() = user_id);

-- いいね数とビュー数を自動更新する関数
CREATE OR REPLACE FUNCTION update_command_stats()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'INSERT' THEN
    UPDATE commands SET likes_count = likes_count + 1 WHERE id = NEW.command_id;
  ELSIF TG_OP = 'DELETE' THEN
    UPDATE commands SET likes_count = likes_count - 1 WHERE id = OLD.command_id;
  END IF;
  RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_likes_count
  AFTER INSERT OR DELETE ON likes
  FOR EACH ROW EXECUTE FUNCTION update_command_stats();
```

### 5. プロジェクト構造

```
command-lab/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── signup/
│   ├── (main)/
│   │   ├── page.tsx          # ホーム（公開コマンド一覧）
│   │   ├── commands/
│   │   │   ├── [id]/
│   │   │   └── new/
│   │   └── profile/
│   ├── api/
│   │   └── commands/
│   ├── layout.tsx
│   └── globals.css
├── components/
│   ├── ui/                   # shadcn/uiコンポーネント
│   ├── CommandCard.tsx
│   ├── CommandEditor.tsx
│   ├── SearchBar.tsx
│   └── CategoryFilter.tsx
├── lib/
│   ├── supabase/
│   │   ├── client.ts
│   │   └── server.ts
│   ├── utils.ts
│   └── types.ts
└── public/
```

### 6. Supabaseクライアント設定

`lib/supabase/client.ts`:

```typescript
import { createClientComponentClient } from '@supabase/auth-helpers-nextjs'
import { createClient } from '@supabase/supabase-js'

export const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)
```

`lib/supabase/server.ts`:

```typescript
import { createServerComponentClient } from '@supabase/auth-helpers-nextjs'
import { cookies } from 'next/headers'

export const createServerSupabaseClient = () => {
  return createServerComponentClient({ cookies })
}
```

### 7. 開発サーバー起動

```bash
npm run dev
```

http://localhost:3000 でアクセス可能

---

## 次のステップ

1. **UIコンポーネントライブラリ導入**
   ```bash
   npx shadcn-ui@latest init
   npx shadcn-ui@latest add button card input textarea
   ```

2. **認証機能実装**
   - ログイン/サインアップページ
   - プロファイル管理

3. **コマンドCRUD機能**
   - 作成・編集・削除
   - 一覧表示・検索

4. **共有機能**
   - 公開/非公開設定
   - いいね・ブックマーク

5. **デプロイ**
   - GitHubにプッシュ
   - Vercelでデプロイ

---

## 参考リソース

- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [shadcn/ui](https://ui.shadcn.com)
- [Tailwind CSS](https://tailwindcss.com)


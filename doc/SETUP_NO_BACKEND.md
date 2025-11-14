# バックエンドなし版セットアップガイド

## 概要

LocalStorageを使用したシンプルな構成。バックエンド不要で完全無料で運用可能。

## 技術スタック

- **フロントエンド**: Vite + React + TypeScript
- **スタイリング**: Tailwind CSS
- **データ保存**: LocalStorage
- **デプロイ**: GitHub Pages / Netlify / Vercel（静的ホスティング）

## セットアップ手順

### 1. プロジェクト作成

```bash
# ViteでReact + TypeScriptプロジェクト作成
npm create vite@latest command-lab -- --template react-ts

cd command-lab

# 依存関係インストール
npm install

# Tailwind CSSセットアップ
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### 2. Tailwind CSS設定

`tailwind.config.js`:
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

`src/index.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 3. プロジェクト構造

```
command-lab/
├── src/
│   ├── components/
│   │   ├── CommandCard.tsx
│   │   ├── CommandEditor.tsx
│   │   ├── CommandList.tsx
│   │   ├── SearchBar.tsx
│   │   ├── CategoryFilter.tsx
│   │   └── ExportImport.tsx
│   ├── hooks/
│   │   └── useCommands.ts
│   ├── lib/
│   │   ├── storage.ts
│   │   ├── share.ts
│   │   └── export.ts
│   ├── types/
│   │   └── command.ts
│   ├── App.tsx
│   └── main.tsx
├── public/
├── index.html
└── package.json
```

### 4. 型定義

`src/types/command.ts`:
```typescript
export type Category = 'cursor' | 'claude-code' | 'general';

export interface Command {
  id: string;
  title: string;
  content: string;
  description?: string;
  category: Category;
  tags: string[];
  createdAt: string;
  updatedAt: string;
}
```

### 5. LocalStorage操作

`src/lib/storage.ts`:
```typescript
import { Command } from '../types/command';

const STORAGE_KEY = 'command-lab-commands';

export const saveCommands = (commands: Command[]): void => {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(commands));
  } catch (error) {
    console.error('Failed to save commands:', error);
  }
};

export const loadCommands = (): Command[] => {
  try {
    const data = localStorage.getItem(STORAGE_KEY);
    return data ? JSON.parse(data) : [];
  } catch (error) {
    console.error('Failed to load commands:', error);
    return [];
  }
};

export const addCommand = (
  command: Omit<Command, 'id' | 'createdAt' | 'updatedAt'>
): Command => {
  const commands = loadCommands();
  const newCommand: Command = {
    ...command,
    id: crypto.randomUUID(),
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };
  commands.push(newCommand);
  saveCommands(commands);
  return newCommand;
};

export const updateCommand = (
  id: string,
  updates: Partial<Command>
): Command | null => {
  const commands = loadCommands();
  const index = commands.findIndex((c) => c.id === id);
  if (index === -1) return null;

  commands[index] = {
    ...commands[index],
    ...updates,
    updatedAt: new Date().toISOString(),
  };
  saveCommands(commands);
  return commands[index];
};

export const deleteCommand = (id: string): boolean => {
  const commands = loadCommands();
  const filtered = commands.filter((c) => c.id !== id);
  if (filtered.length === commands.length) return false;
  saveCommands(filtered);
  return true;
};
```

### 6. 共有機能

`src/lib/share.ts`:
```typescript
import { Command } from '../types/command';

export const shareCommand = (command: Command): string => {
  // コマンドをBase64エンコードしてURLに埋め込む
  const encoded = btoa(JSON.stringify(command));
  const url = `${window.location.origin}${window.location.pathname}?share=${encoded}`;
  return url;
};

export const loadSharedCommand = (): Command | null => {
  const params = new URLSearchParams(window.location.search);
  const encoded = params.get('share');
  if (!encoded) return null;

  try {
    const command = JSON.parse(atob(encoded)) as Command;
    return command;
  } catch (error) {
    console.error('Failed to decode shared command:', error);
    return null;
  }
};
```

### 7. エクスポート/インポート

`src/lib/export.ts`:
```typescript
import { Command } from '../types/command';
import { loadCommands, saveCommands } from './storage';

export const exportCommands = (): void => {
  const commands = loadCommands();
  const dataStr = JSON.stringify(commands, null, 2);
  const blob = new Blob([dataStr], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `commands-${new Date().toISOString().split('T')[0]}.json`;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

export const importCommands = (file: File): Promise<void> => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (e) => {
      try {
        const commands = JSON.parse(e.target?.result as string) as Command[];
        // バリデーション（簡易版）
        if (Array.isArray(commands)) {
          saveCommands(commands);
          resolve();
        } else {
          reject(new Error('Invalid file format'));
        }
      } catch (error) {
        reject(error);
      }
    };
    reader.onerror = reject;
    reader.readAsText(file);
  });
};
```

### 8. カスタムフック

`src/hooks/useCommands.ts`:
```typescript
import { useState, useEffect } from 'react';
import { Command } from '../types/command';
import {
  loadCommands,
  addCommand as addCommandStorage,
  updateCommand as updateCommandStorage,
  deleteCommand as deleteCommandStorage,
} from '../lib/storage';

export const useCommands = () => {
  const [commands, setCommands] = useState<Command[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setCommands(loadCommands());
    setLoading(false);
  }, []);

  const addCommand = (
    command: Omit<Command, 'id' | 'createdAt' | 'updatedAt'>
  ) => {
    const newCommand = addCommandStorage(command);
    setCommands((prev) => [...prev, newCommand]);
    return newCommand;
  };

  const updateCommand = (id: string, updates: Partial<Command>) => {
    const updated = updateCommandStorage(id, updates);
    if (updated) {
      setCommands((prev) => prev.map((c) => (c.id === id ? updated : c)));
    }
    return updated;
  };

  const deleteCommand = (id: string) => {
    if (deleteCommandStorage(id)) {
      setCommands((prev) => prev.filter((c) => c.id !== id));
      return true;
    }
    return false;
  };

  return { commands, loading, addCommand, updateCommand, deleteCommand };
};
```

### 9. 開発サーバー起動

```bash
npm run dev
```

http://localhost:5173 でアクセス可能

## デプロイ

### GitHub Pages

1. `vite.config.ts`に設定追加:
```typescript
export default defineConfig({
  base: '/command-lab/', // リポジトリ名
  // ...
});
```

2. GitHub Actionsで自動デプロイ:
`.github/workflows/deploy.yml`:
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm install
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

### Netlify

1. Netlifyアカウント作成
2. GitHubリポジトリを接続
3. ビルド設定:
   - Build command: `npm run build`
   - Publish directory: `dist`
4. デプロイ完了

### Vercel

1. Vercelアカウント作成
2. GitHubリポジトリをインポート
3. Framework preset: Vite
4. デプロイ完了

## 機能一覧

- ✅ コマンドの作成・編集・削除
- ✅ カテゴリ別フィルタリング
- ✅ タグ機能
- ✅ 検索機能
- ✅ LocalStorageで自動保存
- ✅ ファイルエクスポート/インポート
- ✅ URL共有（短いコマンドのみ）
- ✅ ワンクリックコピー

## コスト

**完全無料**: $0/月
- GitHub Pages: 無料
- Netlify: 無料プランあり
- Vercel: 無料プランあり

## 制限事項

- LocalStorageの容量制限: 約5-10MB（ブラウザ依存）
- URL共有の長さ制限: 約2,000文字（ブラウザ依存）
- データはローカルのみ（クラウド同期なし）
- ユーザー認証なし

## 次のステップ

必要に応じて以下を追加可能：
- Service Worker（オフライン対応）
- IndexedDB（大容量データ対応）
- バックエンド追加（Supabase等）


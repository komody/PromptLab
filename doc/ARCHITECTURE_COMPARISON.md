# アーキテクチャ比較：バックエンドあり vs なし

## バックエンドなしの構成（推奨：シンプル版）

### 技術スタック
- **フロントエンド**: Next.js (Static Export) または Vite + React
- **データ保存**: LocalStorage / IndexedDB
- **共有**: URLパラメータ（Base64エンコード）またはファイルエクスポート
- **デプロイ**: GitHub Pages / Netlify / Vercel（静的ホスティング）

### メリット
✅ **コスト**: 完全無料（静的ホスティングのみ）  
✅ **シンプル**: バックエンド管理不要  
✅ **高速**: CDN配信で高速  
✅ **プライバシー**: データはローカルに保存  
✅ **オフライン対応**: Service Workerで可能  

### デメリット
❌ **共有機能が限定的**: URLパラメータの長さ制限  
❌ **ユーザー認証なし**: 個人データの同期不可  
❌ **いいね・コメント不可**: ソーシャル機能なし  
❌ **検索機能が限定的**: ローカルデータのみ  

### 実装例

#### データ保存（LocalStorage）
```typescript
// コマンドの保存
const saveCommand = (command: Command) => {
  const commands = getCommands();
  commands.push(command);
  localStorage.setItem('commands', JSON.stringify(commands));
};

// コマンドの読み込み
const getCommands = (): Command[] => {
  const data = localStorage.getItem('commands');
  return data ? JSON.parse(data) : [];
};
```

#### 共有機能（URLパラメータ）
```typescript
// コマンドをURLにエンコード
const shareCommand = (command: Command) => {
  const encoded = btoa(JSON.stringify(command));
  const url = `${window.location.origin}/share?c=${encoded}`;
  navigator.clipboard.writeText(url);
};

// URLからコマンドを復元
const loadSharedCommand = () => {
  const params = new URLSearchParams(window.location.search);
  const encoded = params.get('c');
  if (encoded) {
    return JSON.parse(atob(encoded));
  }
  return null;
};
```

#### ファイルエクスポート/インポート
```typescript
// エクスポート
const exportCommands = () => {
  const commands = getCommands();
  const blob = new Blob([JSON.stringify(commands, null, 2)], {
    type: 'application/json'
  });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'commands.json';
  a.click();
};

// インポート
const importCommands = (file: File) => {
  const reader = new FileReader();
  reader.onload = (e) => {
    const commands = JSON.parse(e.target?.result as string);
    localStorage.setItem('commands', JSON.stringify(commands));
  };
  reader.readAsText(file);
};
```

---

## バックエンドありの構成（高度な機能が必要な場合）

### 技術スタック
- **フロントエンド**: Next.js
- **バックエンド**: Next.js API Routes + Supabase
- **データベース**: Supabase (PostgreSQL)
- **認証**: Supabase Auth

### メリット
✅ **共有機能**: 永続的なURL、シェアリンク  
✅ **ユーザー認証**: アカウント管理、データ同期  
✅ **ソーシャル機能**: いいね、コメント、フォロー  
✅ **検索機能**: 全文検索、フィルタリング  
✅ **統計**: ビュー数、人気コマンドなど  

### デメリット
❌ **コスト**: 無料プランに制限あり  
❌ **複雑性**: バックエンド管理が必要  
❌ **セットアップ**: 初期設定がやや複雑  

---

## 推奨：段階的アプローチ

### Phase 1: バックエンドなし（MVP）
- ✅ コマンドの作成・編集・削除
- ✅ LocalStorageで保存
- ✅ ファイルエクスポート/インポート
- ✅ URL共有（短いコマンドのみ）
- ✅ カテゴリ・タグ機能
- ✅ 検索機能（ローカル）

**技術スタック**:
- Vite + React + TypeScript
- Tailwind CSS
- LocalStorage
- GitHub Pages / Netlify

**開発期間**: 1-2週間  
**コスト**: $0/月

### Phase 2: バックエンド追加（必要に応じて）
- ✅ ユーザー認証
- ✅ クラウド同期
- ✅ 永続的なシェアリンク
- ✅ いいね・ブックマーク機能
- ✅ 公開/非公開設定

**技術スタック**:
- Next.js + Supabase
- Vercel

**開発期間**: 追加2-3週間  
**コスト**: $0-25/月（無料プランから開始可能）

---

## バックエンドなしの完全な実装例

### プロジェクト構成
```
command-lab/
├── src/
│   ├── components/
│   │   ├── CommandEditor.tsx
│   │   ├── CommandList.tsx
│   │   ├── SearchBar.tsx
│   │   └── ExportImport.tsx
│   ├── hooks/
│   │   └── useLocalStorage.ts
│   ├── lib/
│   │   ├── storage.ts      # LocalStorage操作
│   │   ├── share.ts        # URL共有
│   │   └── export.ts      # ファイルエクスポート
│   ├── types/
│   │   └── command.ts
│   └── App.tsx
├── public/
└── package.json
```

### データ構造
```typescript
interface Command {
  id: string;
  title: string;
  content: string;
  description?: string;
  category: 'cursor' | 'claude-code' | 'general';
  tags: string[];
  createdAt: string;
  updatedAt: string;
}
```

### LocalStorage操作
```typescript
// lib/storage.ts
export const STORAGE_KEY = 'command-lab-commands';

export const saveCommands = (commands: Command[]): void => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(commands));
};

export const loadCommands = (): Command[] => {
  const data = localStorage.getItem(STORAGE_KEY);
  return data ? JSON.parse(data) : [];
};

export const addCommand = (command: Omit<Command, 'id' | 'createdAt' | 'updatedAt'>): Command => {
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

export const updateCommand = (id: string, updates: Partial<Command>): Command | null => {
  const commands = loadCommands();
  const index = commands.findIndex(c => c.id === id);
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
  const filtered = commands.filter(c => c.id !== id);
  if (filtered.length === commands.length) return false;
  saveCommands(filtered);
  return true;
};
```

### カスタムフック
```typescript
// hooks/useLocalStorage.ts
import { useState, useEffect } from 'react';
import { loadCommands, saveCommands, Command } from '../lib/storage';

export const useCommands = () => {
  const [commands, setCommands] = useState<Command[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setCommands(loadCommands());
    setLoading(false);
  }, []);

  const addCommand = (command: Omit<Command, 'id' | 'createdAt' | 'updatedAt'>) => {
    const newCommand = addCommand(command);
    setCommands(prev => [...prev, newCommand]);
    return newCommand;
  };

  const updateCommand = (id: string, updates: Partial<Command>) => {
    const updated = updateCommand(id, updates);
    if (updated) {
      setCommands(prev => prev.map(c => c.id === id ? updated : c));
    }
    return updated;
  };

  const deleteCommand = (id: string) => {
    if (deleteCommand(id)) {
      setCommands(prev => prev.filter(c => c.id !== id));
      return true;
    }
    return false;
  };

  return { commands, loading, addCommand, updateCommand, deleteCommand };
};
```

---

## まとめ

### バックエンドなしで十分な場合
- ✅ 個人利用がメイン
- ✅ 共有は限定的でOK
- ✅ シンプルに始めたい
- ✅ コストを抑えたい

### バックエンドが必要な場合
- ✅ 多くのユーザーに共有したい
- ✅ ソーシャル機能が必要
- ✅ データのクラウド同期が必要
- ✅ 統計・分析機能が必要

**推奨**: まずはバックエンドなしでMVPを作成し、必要に応じて段階的にバックエンドを追加するアプローチが効率的です。


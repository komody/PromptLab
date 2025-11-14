# プロジェクト移行ガイド

## 現在の状況

- **現在のプロジェクト**: `/Users/maeharatakaya/Desktop/aria`
- **作成したドキュメント**: コマンドラボ関連の設計ドキュメント
- **新しいプロジェクト**: コマンドラボの実装プロジェクト

## 移行方法の選択肢

### オプション1: 新しいプロジェクトフォルダを作成（推奨）

**メリット**:
- ✅ 現在のプロジェクト（aria）はそのまま残る
- ✅ 新しいプロジェクトをクリーンに始められる
- ✅ プロジェクトが分離されて管理しやすい

**手順**:
1. 新しいプロジェクトフォルダを作成
   ```bash
   cd ~/Desktop
   mkdir promptlab  # または選んだプロジェクト名
   cd promptlab
   ```

2. 必要なドキュメントをコピー
   ```bash
   # コマンドラボ関連のドキュメントをコピー
   cp ../aria/SIMPLE_ARCHITECTURE.md .
   cp ../aria/SETUP_NO_BACKEND.md .
   cp ../aria/MONETIZATION.md .
   cp ../aria/SECURITY.md .
   cp ../aria/DEPLOYMENT.md .
   cp ../aria/PROJECT_NAMES.md .
   ```

3. 新しいプロジェクトで開発開始
   ```bash
   npm create vite@latest . -- --template react-ts
   # または
   npx create-next-app@latest . --typescript --tailwind --app
   ```

---

### オプション2: 現在のプロジェクトをそのまま使用

**メリット**:
- ✅ ドキュメントが既に同じ場所にある
- ✅ 移行作業が不要

**デメリット**:
- ❌ プロジェクト名（aria）と内容が一致しない
- ❌ 既存のファイル（index.html, js/index.js等）と混在する可能性

**手順**:
1. 既存のファイルを整理（必要に応じて）
2. 新しいプロジェクトのファイルを作成
3. ドキュメントはそのまま使用

---

### オプション3: ドキュメントを別フォルダに整理

**メリット**:
- ✅ ドキュメントとコードを分離
- ✅ 現在のプロジェクトもそのまま残る

**手順**:
1. ドキュメントフォルダを作成
   ```bash
   mkdir ~/Desktop/command-lab-docs
   ```

2. ドキュメントを移動
   ```bash
   mv ~/Desktop/aria/*.md ~/Desktop/command-lab-docs/
   ```

3. 新しいプロジェクトを作成
   ```bash
   mkdir ~/Desktop/promptlab
   cd ~/Desktop/promptlab
   ```

---

## 推奨：オプション1（新しいプロジェクトフォルダ）

### 完全なセットアップ手順

#### 1. 新しいプロジェクトフォルダ作成
```bash
cd ~/Desktop
mkdir promptlab
cd promptlab
```

#### 2. ドキュメントをコピー
```bash
# 必要なドキュメントをコピー
cp ../aria/SIMPLE_ARCHITECTURE.md .
cp ../aria/SETUP_NO_BACKEND.md .
cp ../aria/MONETIZATION.md .
cp ../aria/SECURITY.md .
cp ../aria/DEPLOYMENT.md .
cp ../aria/PROJECT_NAMES.md .
cp ../aria/NO_SERVER_MANAGEMENT.md .
```

#### 3. READMEを作成
```bash
# README.mdを作成
cat > README.md << 'EOF'
# PromptLab

Cursor/Claude Code用のコマンド・プロンプト管理ツール

## ドキュメント

- [シンプル構成](./SIMPLE_ARCHITECTURE.md)
- [セットアップガイド](./SETUP_NO_BACKEND.md)
- [マネタイズ戦略](./MONETIZATION.md)
- [セキュリティ](./SECURITY.md)
- [デプロイ方法](./DEPLOYMENT.md)
EOF
```

#### 4. プロジェクト初期化
```bash
# Next.jsプロジェクト作成（例）
npx create-next-app@latest . --typescript --tailwind --app

# または Vite + React
npm create vite@latest . -- --template react-ts
```

#### 5. Gitリポジトリ初期化
```bash
git init
git add .
git commit -m "Initial commit: PromptLab project setup"
```

---

## ドキュメント整理

### コマンドラボ関連（新しいプロジェクトにコピー）
- ✅ `SIMPLE_ARCHITECTURE.md` - シンプル構成
- ✅ `SETUP_NO_BACKEND.md` - セットアップガイド
- ✅ `MONETIZATION.md` - マネタイズ戦略
- ✅ `SECURITY.md` - セキュリティ
- ✅ `DEPLOYMENT.md` - デプロイ方法
- ✅ `PROJECT_NAMES.md` - プロジェクト名候補
- ✅ `NO_SERVER_MANAGEMENT.md` - サーバー管理不要の説明

### ariaプロジェクト関連（現在のプロジェクトに残す）
- `index.html` - 既存のHTML
- `js/index.js` - 既存のJavaScript
- `css/style.css` - 既存のCSS
- `img/` - 既存の画像

### 参考資料（どちらかに残す）
- `ARCHITECTURE_COMPARISON.md` - アーキテクチャ比較
- `BACKEND_LANGUAGES.md` - バックエンド言語比較
- `COMMAND_LAB_ANALYSIS.md` - コマンドラボ分析
- `TECH_STACK.md` - 技術スタック（古い版）
- `SETUP_GUIDE.md` - セットアップガイド（古い版）

---

## 新しいプロジェクト構造

```
promptlab/
├── README.md
├── package.json
├── tsconfig.json
├── .env.local
├── docs/                          # ドキュメントフォルダ（オプション）
│   ├── SIMPLE_ARCHITECTURE.md
│   ├── SETUP_NO_BACKEND.md
│   ├── MONETIZATION.md
│   └── ...
├── src/                           # または app/（Next.js）
│   ├── components/
│   ├── lib/
│   └── ...
└── public/
```

---

## 次のステップ

### 新しいプロジェクトで始める場合

1. **プロジェクトフォルダ作成**
   ```bash
   mkdir ~/Desktop/promptlab
   cd ~/Desktop/promptlab
   ```

2. **ドキュメントコピー**
   ```bash
   cp ../aria/SIMPLE_ARCHITECTURE.md .
   cp ../aria/SETUP_NO_BACKEND.md .
   # ... 他のドキュメントも
   ```

3. **プロジェクト初期化**
   ```bash
   npx create-next-app@latest . --typescript --tailwind --app
   ```

4. **開発開始**
   ```bash
   npm run dev
   ```

### 現在のプロジェクトを続ける場合

1. **既存ファイルの整理**（必要に応じて）
2. **新しいプロジェクトファイルを作成**
3. **ドキュメントはそのまま使用**

---

## 注意点

### Git管理
- 新しいプロジェクトでGitリポジトリを初期化する場合、既存のariaプロジェクトとは別のリポジトリとして管理
- ドキュメントの履歴を保持したい場合は、Gitの履歴もコピー可能

### ドキュメントの更新
- 新しいプロジェクトで開発を進めながら、ドキュメントも更新
- 必要に応じてariaプロジェクトのドキュメントも更新（参考資料として）

---

## まとめ

**推奨**: **オプション1（新しいプロジェクトフォルダ）**

1. 新しいプロジェクトフォルダを作成
2. 必要なドキュメントをコピー
3. プロジェクトを初期化
4. 開発開始

**これで、ariaプロジェクトはそのまま残り、新しいプロジェクトをクリーンに始められます！**


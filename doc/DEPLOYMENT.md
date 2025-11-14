# 公開方法ガイド

## 公開オプション比較

### 1. Vercel（推奨：Next.js使用時）

#### メリット
- Next.jsに最適化
- 無料プランあり（個人プロジェクトに十分）
- 自動デプロイ（GitHub連携）
- 高速なCDN配信
- 環境変数の簡単な管理

#### デプロイ手順
1. GitHubにリポジトリをプッシュ
2. Vercelアカウント作成（GitHub連携）
3. プロジェクトをインポート
4. 環境変数を設定
5. デプロイ（自動）

#### コスト
- **無料**: 100GB帯域幅/月、無制限のデプロイ
- **Pro**: $20/月（チーム機能、より多くの帯域幅）

---

### 2. Netlify

#### メリット
- 簡単なデプロイ
- 無料プランあり
- フォーム機能内蔵
- スプリットテスト機能

#### デプロイ手順
1. GitHubにリポジトリをプッシュ
2. Netlifyアカウント作成
3. 「New site from Git」を選択
4. ビルド設定を入力
5. デプロイ

#### コスト
- **無料**: 100GB帯域幅/月、300分ビルド時間/月
- **Pro**: $19/月

---

### 3. Firebase Hosting

#### メリット
- Firebaseサービスと統合しやすい
- 無料プランあり
- 高速なCDN
- カスタムドメイン対応

#### デプロイ手順
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

#### コスト
- **無料**: 10GBストレージ、360MB/日の転送
- **Blaze**: 従量課金（無料枠あり）

---

### 4. GitHub Pages（静的サイトのみ）

#### メリット
- 完全無料
- GitHubと統合
- 簡単なセットアップ

#### デメリット
- サーバーサイド機能不可
- カスタムドメイン設定がやや複雑

#### デプロイ手順
1. リポジトリのSettings > Pages
2. Sourceを選択（GitHub Actions推奨）
3. 自動デプロイ設定

---

## データベース・バックエンド

### Supabase（推奨）

#### メリット
- PostgreSQL（本格的なRDB）
- 認証機能内蔵
- リアルタイム機能
- ストレージ機能
- 無料プランあり

#### セットアップ
1. [supabase.com](https://supabase.com)でアカウント作成
2. プロジェクト作成
3. APIキーとURLを取得
4. 環境変数に設定

#### コスト
- **無料**: 500MBデータベース、2GBストレージ
- **Pro**: $25/月

---

### Firebase

#### メリット
- Googleのインフラ
- 認証・ストレージ・関数が統合
- 無料プランあり

#### コスト
- **無料**: 1GBストレージ、10GB/月転送
- **Blaze**: 従量課金

---

### PlanetScale

#### メリット
- MySQL互換
- ブランチ機能（Gitライク）
- サーバーレス
- 無料プランあり

#### コスト
- **無料**: 1データベース、5GBストレージ
- **Scaler**: $29/月

---

## 推奨構成

### 小規模・個人プロジェクト
```
フロントエンド: Vercel (無料)
バックエンド: Supabase (無料)
合計コスト: $0/月
```

### 中規模・成長プロジェクト
```
フロントエンド: Vercel Pro ($20/月)
バックエンド: Supabase Pro ($25/月)
合計コスト: $45/月
```

---

## デプロイチェックリスト

### 事前準備
- [ ] 環境変数の設定（APIキー、データベースURLなど）
- [ ] 本番環境用の設定ファイル
- [ ] エラーハンドリングの実装
- [ ] ログ設定
- [ ] セキュリティ設定（CORS、認証など）

### デプロイ後
- [ ] 動作確認（全機能テスト）
- [ ] パフォーマンステスト
- [ ] SEO設定（メタタグ、sitemap）
- [ ] カスタムドメイン設定（オプション）
- [ ] アナリティクス設定（Google Analytics等）
- [ ] エラートラッキング（Sentry等）

---

## ドメイン取得（オプション）

### 推奨レジストラ
- **Namecheap**: 安価、管理が簡単
- **Google Domains**: Googleサービスと統合
- **お名前.com**: 日本語サポート

### 価格目安
- `.com`: 約1,000-1,500円/年
- `.dev`: 約2,000円/年
- `.io`: 約3,000円/年

---

## CI/CD設定

### GitHub Actions例

```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm install
      - run: npm run build
      - run: npm run deploy
```

---

## モニタリング・分析

### 推奨ツール
- **Vercel Analytics**: Vercel使用時
- **Google Analytics**: ユーザー行動分析
- **Sentry**: エラートラッキング
- **Uptime Robot**: ダウンタイム監視（無料）

---

## セキュリティ

### 必須設定
- [ ] HTTPS有効化（自動）
- [ ] 環境変数の暗号化
- [ ] CORS設定
- [ ] レート制限（API保護）
- [ ] 入力値検証
- [ ] SQLインジェクション対策（ORM使用で自動）

---

## バックアップ

### データベースバックアップ
- **Supabase**: 自動バックアップ（Proプラン）
- **Firebase**: 手動エクスポート
- **PlanetScale**: 自動バックアップ

### 推奨頻度
- 開発中: 週1回
- 本番: 日次（自動）


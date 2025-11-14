# セキュリティについて

## Vercel + Supabaseのセキュリティ

### 自動で対応されるセキュリティ

#### Vercel
- ✅ **SSL/TLS証明書**: 自動発行・更新（Let's Encrypt）
- ✅ **HTTPS強制**: すべての通信が暗号化
- ✅ **DDoS対策**: 自動的に保護
- ✅ **CDN配信**: グローバルに分散、攻撃を分散
- ✅ **ファイアウォール**: 自動設定
- ✅ **セキュリティヘッダー**: 自動設定（CSP、XSS保護など）

#### Supabase
- ✅ **データベース暗号化**: 保存時・転送時の暗号化
- ✅ **Row Level Security (RLS)**: 行レベルでのアクセス制御
- ✅ **API認証**: JWTトークンベース
- ✅ **自動バックアップ**: 日次バックアップ
- ✅ **SQLインジェクション対策**: パラメータ化クエリ
- ✅ **レート制限**: API呼び出しの制限

## 自分で設定すべきセキュリティ

### 1. 環境変数の管理
```typescript
// .env.local（Gitにコミットしない）
NEXT_PUBLIC_SUPABASE_URL=your-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-key

// Vercelのダッシュボードで設定
// Settings > Environment Variables
```

### 2. Row Level Security (RLS) ポリシー
```sql
-- 全員が閲覧可能、管理者のみ追加可能
CREATE POLICY "Problems are viewable by everyone"
  ON problems FOR SELECT USING (true);

CREATE POLICY "Only admins can insert"
  ON problems FOR INSERT 
  WITH CHECK (auth.jwt() ->> 'role' = 'admin');
```

### 3. 入力値検証
```typescript
import { z } from 'zod';

const problemSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(1).max(10000),
  category: z.enum(['cursor', 'claude-code', 'general']),
});

// 使用例
const validated = problemSchema.parse(userInput);
```

### 4. CORS設定
```typescript
// Next.js API Routesで設定
export async function GET(request: Request) {
  return new Response(JSON.stringify(data), {
    headers: {
      'Access-Control-Allow-Origin': 'https://yourdomain.com',
      'Access-Control-Allow-Methods': 'GET, POST',
    },
  });
}
```

## セキュリティチェックリスト

### 開発時
- [ ] 環境変数を`.env.local`に設定（Gitにコミットしない）
- [ ] 入力値検証を実装（Zodなど）
- [ ] RLSポリシーを適切に設定
- [ ] SQLインジェクション対策（Supabaseが自動対応）

### デプロイ時
- [ ] Vercelで環境変数を設定
- [ ] SupabaseでAPIキーを確認
- [ ] HTTPSが有効か確認
- [ ] セキュリティヘッダーを確認

### 運用時
- [ ] 定期的に依存関係を更新
- [ ] ログを確認（異常なアクセスがないか）
- [ ] バックアップが正常に動作しているか確認

## セキュリティの強み

### マネージドサービスの利点
1. **専門チームが管理**: Vercel/Supabaseのセキュリティチームが24時間監視
2. **自動パッチ適用**: セキュリティ脆弱性が発見されても自動対応
3. **コンプライアンス**: SOC 2、ISO 27001などの認証取得済み
4. **監査ログ**: すべてのアクセスが記録される

### 自分でサーバーを管理する場合との比較

| 項目 | 自分で管理 | Vercel + Supabase |
|------|-----------|-------------------|
| SSL証明書 | 手動設定・更新 | ✅ 自動 |
| セキュリティパッチ | 手動適用 | ✅ 自動 |
| DDoS対策 | 自分で設定 | ✅ 自動 |
| 監視 | 自分で設定 | ✅ 自動 |
| バックアップ | 自分で設定 | ✅ 自動 |

## まとめ

**Vercel + Supabaseは企業レベルのセキュリティを提供**:
- ✅ SSL/TLS自動
- ✅ DDoS対策自動
- ✅ データベース暗号化
- ✅ アクセス制御（RLS）
- ✅ 自動バックアップ

**自分でやること**:
- ✅ 環境変数の適切な管理
- ✅ RLSポリシーの設定
- ✅ 入力値検証

**セキュリティ面でも安心して使えます！**


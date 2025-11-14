# マネタイズ戦略

## 概要

コマンドラボのような開発者向けツールのマネタイズ方法をまとめます。

## マネタイズのルート

### 1. フリーミアムモデル（推奨）

#### 基本機能：無料
- ✅ 問題の閲覧
- ✅ 基本的な検索
- ✅ カテゴリフィルタ
- ✅ ワンクリックコピー

#### プレミアム機能：有料
- 💎 **高度な検索**: 全文検索、正規表現検索
- 💎 **問題の保存**: お気に入り、コレクション機能
- 💎 **エクスポート機能**: PDF、Markdownエクスポート
- 💎 **APIアクセス**: プログラムから問題を取得
- 💎 **広告非表示**: 広告フリー体験
- 💎 **優先サポート**: サポート優先対応

#### 料金設定例
- **無料**: 基本機能
- **Pro**: $5-10/月（個人開発者向け）
- **Team**: $20-30/月（チーム向け、複数ユーザー）

#### 実装方法
```typescript
// ユーザーのサブスクリプション状態を確認
const { data: subscription } = await supabase
  .from('subscriptions')
  .select('*')
  .eq('user_id', userId)
  .single();

if (subscription?.plan === 'pro') {
  // プレミアム機能を有効化
}
```

---

### 2. 広告モデル

#### 無料で利用可能、広告で収益化

**広告ネットワーク**:
- **Google AdSense**: 最も一般的、審査必要
- **Carbon Ads**: 開発者向け広告、質が高い
- **BuySellAds**: 直接広告主と取引

#### 実装例
```typescript
// 広告コンポーネント
export function AdBanner() {
  return (
    <div className="ad-container">
      {/* Google AdSense */}
      <ins className="adsbygoogle"
        style={{ display: 'block' }}
        data-ad-client="ca-pub-XXXXX"
        data-ad-slot="XXXXX"
        data-ad-format="auto" />
    </div>
  );
}
```

#### 収益見込み
- **月間PV 10,000**: $10-50/月
- **月間PV 100,000**: $100-500/月
- **月間PV 1,000,000**: $1,000-5,000/月

---

### 3. スポンサーシップ

#### GitHub Sponsors / Open Collective

**メリット**:
- ✅ 開発者コミュニティから直接支援
- ✅ 透明性が高い
- ✅ 税務処理が簡単（GitHub Sponsors）

**実装**:
- GitHubリポジトリにSponsorボタンを追加
- サイトにスポンサー一覧を表示

#### 企業スポンサー
- 開発ツール関連企業と提携
- バナー広告やコンテンツマーケティング

---

### 4. アフィリエイト

#### 関連サービスの紹介

**対象サービス**:
- **Cursor**: アフィリエイトプログラム（あれば）
- **Claude Code**: アフィリエイトプログラム（あれば）
- **Vercel**: 紹介プログラム
- **Supabase**: 紹介プログラム
- **開発ツール**: 各種開発ツールの紹介

**実装**:
```typescript
// アフィリエイトリンク
<a href="https://vercel.com/?ref=commandlab">
  Vercelでデプロイ
</a>
```

---

### 5. データ販売（匿名化）

#### 統計データの販売

**販売可能なデータ**:
- 人気カテゴリの統計
- 検索トレンド
- 地域別の利用状況（匿名化）

**注意点**:
- ✅ プライバシーポリシーに明記
- ✅ 個人情報は含めない
- ✅ GDPR準拠

---

### 6. 企業向けライセンス

#### 企業向け機能

**機能例**:
- カスタムブランディング
- 社内専用問題の追加
- 利用状況レポート
- 優先サポート
- SLA保証

**料金**:
- **Enterprise**: $100-500/月（企業規模による）

---

### 7. コンテンツ販売

#### プレミアム問題セット

**販売内容**:
- 高度な問題集
- 業界別問題集（Web開発、データサイエンスなど）
- 動画解説付き問題

**料金**:
- 問題セット: $10-50/セット
- サブスクリプション: $10-20/月（全問題アクセス）

---

## 推奨：段階的マネタイズ戦略

### Phase 1: 無料で開始（0-3ヶ月）
- ✅ ユーザー獲得
- ✅ フィードバック収集
- ✅ 機能改善

**収益**: $0

### Phase 2: 広告追加（3-6ヶ月）
- ✅ Google AdSense導入
- ✅ ユーザー数が増えてきたら

**収益**: $50-500/月（ユーザー数による）

### Phase 3: フリーミアム導入（6-12ヶ月）
- ✅ プレミアム機能を追加
- ✅ サブスクリプション開始

**収益**: $100-1,000/月（変換率による）

### Phase 4: 企業向け展開（12ヶ月以降）
- ✅ 企業向け機能追加
- ✅ 営業活動

**収益**: $1,000-10,000/月（企業数による）

---

## 実装例：サブスクリプション機能

### 1. Stripe統合

```bash
npm install @stripe/stripe-js stripe
```

### 2. サブスクリプションテーブル

```sql
CREATE TABLE subscriptions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users NOT NULL,
  plan TEXT NOT NULL, -- 'free', 'pro', 'team'
  stripe_subscription_id TEXT,
  status TEXT, -- 'active', 'canceled', 'past_due'
  current_period_end TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### 3. Stripe Webhook

```typescript
// app/api/webhooks/stripe/route.ts
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(request: Request) {
  const body = await request.text();
  const signature = request.headers.get('stripe-signature')!;

  const event = stripe.webhooks.constructEvent(
    body,
    signature,
    process.env.STRIPE_WEBHOOK_SECRET!
  );

  // サブスクリプション更新処理
  if (event.type === 'customer.subscription.updated') {
    // データベースを更新
  }

  return new Response(JSON.stringify({ received: true }));
}
```

### 4. プレミアム機能の制限

```typescript
// ユーザーのプランを確認
export async function checkPremiumAccess(userId: string): Promise<boolean> {
  const { data } = await supabase
    .from('subscriptions')
    .select('plan, status')
    .eq('user_id', userId)
    .eq('status', 'active')
    .single();

  return data?.plan !== 'free';
}

// 使用例
const isPremium = await checkPremiumAccess(userId);
if (!isPremium) {
  // 無料ユーザー向けの制限
  return <UpgradePrompt />;
}
```

---

## 収益予測

### 保守的な見積もり

| 月間ユーザー数 | 広告収益 | プレミアム変換率5% | 合計収益 |
|--------------|---------|------------------|---------|
| 1,000 | $10 | $25 (5人×$5) | $35/月 |
| 10,000 | $100 | $250 (50人×$5) | $350/月 |
| 100,000 | $1,000 | $2,500 (500人×$5) | $3,500/月 |
| 1,000,000 | $10,000 | $25,000 (5,000人×$5) | $35,000/月 |

### 楽観的な見積もり

| 月間ユーザー数 | 広告収益 | プレミアム変換率10% | 合計収益 |
|--------------|---------|-------------------|---------|
| 1,000 | $20 | $50 (10人×$5) | $70/月 |
| 10,000 | $200 | $500 (100人×$5) | $700/月 |
| 100,000 | $2,000 | $5,000 (1,000人×$5) | $7,000/月 |
| 1,000,000 | $20,000 | $50,000 (10,000人×$5) | $70,000/月 |

---

## マネタイズのベストプラクティス

### ✅ やるべきこと
1. **価値提供を最優先**: まず良いサービスを作る
2. **段階的に導入**: いきなり有料化しない
3. **透明性**: 料金体系を明確に
4. **ユーザーフィードバック**: 有料機能の要望を聞く
5. **A/Bテスト**: 料金設定をテスト

### ❌ 避けるべきこと
1. **過度な広告**: ユーザー体験を損なう
2. **突然の有料化**: 既存ユーザーを失う
3. **不透明な料金**: 信頼を失う
4. **機能制限の過剰**: 無料ユーザーも満足させる

---

## まとめ

### 推奨マネタイズ戦略

1. **開始**: 完全無料でユーザー獲得
2. **3-6ヶ月後**: 広告追加（収益化開始）
3. **6-12ヶ月後**: フリーミアム導入（サブスクリプション）
4. **12ヶ月以降**: 企業向け展開

### 収益源の優先順位

1. **サブスクリプション**（最も安定）
2. **広告**（初期から可能）
3. **企業向けライセンス**（高単価）
4. **アフィリエイト**（追加収益）

**段階的にマネタイズを進めることで、ユーザーを失わずに収益化できます！**


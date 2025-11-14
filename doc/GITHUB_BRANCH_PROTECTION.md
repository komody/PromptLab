# GitHubブランチ保護設定ガイド

`main`（または`master`）ブランチを保護して、直接プッシュを禁止し、プルリクエスト（PR）を必須にする設定手順です。

## 🛡️ ブランチ保護設定手順

### 1. GitHubリポジトリにアクセス

1. [GitHub](https://github.com)にログイン
2. `komody/PromptLab`リポジトリを開く

### 2. ブランチ保護ルールの設定

1. リポジトリの **Settings** タブをクリック
2. 左サイドバーから **Branches** を選択
3. **Add rule** ボタンをクリック

### 3. 保護ルールの詳細設定

#### Branch name pattern
```
main
```
または既存のブランチ名（`master`など）

#### 必須設定項目

✅ **Require a pull request before merging**
- ✅ Require approvals: `1`（必要に応じて変更）
- ✅ Dismiss stale pull request approvals when new commits are pushed: ✅ チェック

✅ **Require status checks to pass before merging**
- （CI/CDを設定したら、ここでチェックを追加）

✅ **Require conversation resolution before merging**
- PRのコメントが解決されるまでマージ不可

✅ **Require linear history**
- マージコミットを必須にする（オプション）

✅ **Include administrators**
- 管理者もこのルールに従う（推奨）

#### オプション設定

✅ **Restrict who can push to matching branches**
- 特定のユーザー/チームのみプッシュ可能（チーム開発の場合）

✅ **Allow force pushes**
- ❌ チェックしない（通常は禁止）

✅ **Allow deletions**
- ❌ チェックしない（通常は禁止）

### 4. 設定の保存

1. **Create** ボタンをクリック
2. 設定が適用されます

---

## ✅ 設定後の動作確認

### 直接プッシュが禁止されているか確認

```bash
# 直接プッシュを試みる（エラーになるはず）
git push origin main
```

**期待される結果**:
```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: At least 1 approving review is required by reviewers with write access.
```

### PR経由でのマージが可能か確認

1. 機能ブランチを作成
2. 変更をコミット・プッシュ
3. PRを作成
4. PR経由でマージできることを確認

---

## 🔧 既存の`master`ブランチを`main`にリネームする場合

### ローカルでリネーム

```bash
# ローカルブランチをリネーム
git branch -m master main

# リモートのmasterを削除
git push origin --delete master

# 新しいmainブランチをプッシュ
git push origin main

# アップストリームを設定
git push --set-upstream origin main
```

### GitHubでデフォルトブランチを変更

1. Settings > Branches
2. Default branch セクションで **Switch to another branch** をクリック
3. `main`を選択
4. **Update** をクリック

---

## 📋 設定チェックリスト

- [ ] ブランチ保護ルールを作成
- [ ] PR必須設定を有効化
- [ ] 承認必須設定を有効化（必要に応じて）
- [ ] 管理者もルールに従う設定を有効化
- [ ] 直接プッシュが禁止されていることを確認
- [ ] PR経由でマージできることを確認

---

## 🎯 推奨設定（このプロジェクト用）

### 最小限の設定（個人プロジェクト）

```
✅ Require a pull request before merging
   - Require approvals: 0（自分だけの場合）
✅ Include administrators
```

### チーム開発の場合

```
✅ Require a pull request before merging
   - Require approvals: 1
   - Dismiss stale approvals: ✅
✅ Require conversation resolution before merging
✅ Include administrators
```

---

**設定完了後、安全に開発を進められます！🛡️**


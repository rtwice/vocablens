# VocabLens 📖

英語の論文・技術書をPDFや画像で読みながら、単語をクリックするだけで意味が調べられるツールです。調べた単語はリストに保存でき、CSVで書き出せます。

---

## デモ

1. PDFまたは画像をドロップ
2. 気になる単語をクリック → 候補リストが表示
3. 単語を選ぶ → 日本語の意味・用例がポップアップ
4. 「+ 保存」でリストに追加 → CSVでエクスポート

---

## 必要なもの

- **Anthropic APIキー**（単語の意味を調べるため）
- **Supabaseアカウント**（単語リスト・読書進捗の保存・複数デバイス同期）※任意

---

## セットアップ

### 1. Anthropic APIキーを取得

1. [console.anthropic.com](https://console.anthropic.com) にアクセス
2. アカウント作成 → ログイン
3. 左メニュー **API Keys** → **Create Key**
4. 表示されたキー（`sk-ant-...`）をコピー

> 💡 APIの利用は従量課金です。単語1回あたり約0.1〜0.3円程度が目安です。

---

### 2. アプリを開く

#### A. GitHub Pages（推奨）
[vocablens.github.io](https://rtwice.github.io/vocablens/) をブラウザで開く

#### B. ローカルで使う
```bash
git clone https://github.com/rtwice/vocablens.git
cd vocablens
# index.html をブラウザで直接開く
```

---

### 3. APIキーをセット

アプリ上部の入力欄に `sk-ant-...` を貼り付けて **「保存」** をクリック。

---

### 4. Supabaseをセットアップ（任意・複数デバイス同期に必要）

複数デバイスで単語リストや読書進捗を共有したい場合のみ必要です。

#### 4-1. プロジェクト作成
1. [supabase.com](https://supabase.com) でアカウント作成
2. **New project** → 名前・パスワードを設定

#### 4-2. テーブル作成
**SQL Editor** → **New query** に以下を貼り付けて **Run**：

```sql
-- 単語リスト
create table vocab (
  id uuid default gen_random_uuid() primary key,
  word text not null,
  definition text,
  example text,
  filename text default '',
  created_at timestamp default now()
);
alter table vocab enable row level security;
create policy "public access" on vocab for all using (true);

-- 読書進捗
create table progress (
  id uuid default gen_random_uuid() primary key,
  filename text not null,
  page int not null,
  total int not null,
  updated_at timestamp default now()
);
alter table progress enable row level security;
create policy "public access" on progress for all using (true);
```

#### 4-3. 接続情報を取得
**Settings** → **API** から以下をメモ：
- `Project URL`（例：`https://xxxx.supabase.co`）
- `anon public` キー

#### 4-4. アプリのコードを編集
`index.html` の以下の部分を書き換える：

```javascript
var SB_URL = 'https://あなたのProjectURL';
var SB_KEY = 'あなたのanonキー';
```

---

## 使い方

| 操作 | 内容 |
|------|------|
| PDFをドロップ | 複数ページのPDFに対応 |
| 画像をドロップ | JPG・PNG・WEBPに対応 |
| 単語をクリック | 候補リストが表示される |
| 候補を選ぶ | 意味・発音・用例がポップアップ |
| 「+ 保存」 | 単語リストに追加（Supabaseに保存） |
| 「CSV」ボタン | 単語・意味・用例をCSV出力 |
| ズームスライダー | ページの表示サイズを調整 |
| 検索欄 | 手動で単語を入力して調べる |

---

## 対応ファイル

- テキストPDF（コピペできるもの）→ 高精度で単語を認識
- スキャンPDF・画像 → AIが画像から単語を認識

---

## 技術スタック

- **フロントエンド**: HTML / CSS / Vanilla JS
- **PDF描画**: [PDF.js](https://mozilla.github.io/pdf.js/)
- **単語検索**: [Anthropic Claude API](https://anthropic.com)
- **データ保存**: [Supabase](https://supabase.com)（任意）

---

## ライセンス

MIT License — 自由に使用・改変・再配布できます。

---

## 作者

[@rtwice](https://twitter.com/rtwice)

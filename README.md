# どうぶつえん掲示板

動物園テーマの、カラフルでポップな家族向け掲示板です。お名前と好きな動物スタンプを選んで、コルクボードにひとことを貼り出せます。

## 公開ページ

GitHub Pages: https://michukyr2.github.io/zoo-bulletin-board/

## 投稿の保存について（Supabase連携）

このページは [Supabase](https://supabase.com) をバックエンドにした「みんなの掲示板」です。`index.html` 内の以下2行に自分のSupabaseプロジェクトの値を入れてください（Project Settings → API で確認できます）。

```js
var SUPABASE_URL = "YOUR_SUPABASE_URL";
var SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";
```

`anon` / `public` キーはブラウザに埋め込む前提の公開キーです。`service_role`キー（秘密鍵）は絶対に使わないでください。

Supabase側で、以下のSQLを一度実行してテーブルとポリシーを作成します（SQL Editorで実行）:

```sql
create table if not exists public.messages (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  body text not null,
  created_at timestamptz not null default now()
);

alter table public.messages enable row level security;

create policy "Allow public read" on public.messages
  for select using (true);

create policy "Allow public insert" on public.messages
  for insert with check (true);

alter publication supabase_realtime add table public.messages;
```

- ログイン不要で、誰でも投稿（insert）と閲覧（select）ができます。
- 投稿は新しい順に一覧表示され、他の人の投稿もリアルタイムで反映されます。
- **`SUPABASE_URL` / `SUPABASE_ANON_KEY` がプレースホルダのままの場合**は自動的にオフラインモードになり、投稿はそのブラウザの `localStorage` だけに保存されます（他の人とは共有されません）。

## ローカルで開く

```bash
open index.html
```

またはローカルサーバーで:

```bash
python3 -m http.server 8000
```

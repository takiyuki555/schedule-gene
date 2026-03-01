# 旅のしおり — セットアップガイド

## 構成

```
travel-planner/
└── index.html   ← これだけでOK。GitHub Pagesで公開できます
```

---

## 1. Firebase プロジェクトを作る

1. [https://console.firebase.google.com/](https://console.firebase.google.com/) にアクセス
2. **「プロジェクトを追加」** をクリック
3. プロジェクト名を入力（例: `tabi-no-shiori`）→ Google アナリティクスは不要なのでオフでOK

---

## 2. Realtime Database を有効にする

1. 左メニュー →「構築」→「**Realtime Database**」
2. 「データベースを作成」をクリック
3. ロケーション: **asia-southeast1**（シンガポール）を選択
4. セキュリティルール: **「テストモードで開始」** を選択 → 有効にする

> ⚠️ テストモードは30日で期限切れになります。後述のルール設定で恒久化してください。

---

## 3. ウェブアプリを登録して設定値を取得する

1. Firebase コンソール左上の歯車アイコン →「**プロジェクトの設定**」
2. 「マイアプリ」セクション →「**</>（ウェブ）**」アイコンをクリック
3. アプリのニックネームを入力（なんでもOK）→「アプリを登録」
4. 表示される `firebaseConfig` をコピーしておく

```js
// こういうものが表示されます
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "tabi-no-shiori.firebaseapp.com",
  databaseURL: "https://tabi-no-shiori-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "tabi-no-shiori",
  storageBucket: "tabi-no-shiori.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};

// 実際出たやつ
const firebaseConfig = {
  apiKey: "AIzaSyA8zno2z8K5JfpBI3tWEYFU4ffBn4N10ug",
  authDomain: "schedule-gene.firebaseapp.com",
  databaseURL: "https://schedule-gene-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "schedule-gene",
  storageBucket: "schedule-gene.firebasestorage.app",
  messagingSenderId: "22637246336",
  appId: "1:22637246336:web:a13c51712ad9a84ac9f365",
  measurementId: "G-Q8J7JGRYNB"
};
```

---

## 4. index.html に設定値を貼り付ける

`index.html` の先頭付近にある `firebaseConfig` を書き換えます：

```js
// ↓↓↓ ここを自分のFirebase設定に書き換える ↓↓↓
const firebaseConfig = {
  apiKey:            "AIzaSy...",          // ← コピーした値に書き換え
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  databaseURL:       "https://YOUR_PROJECT-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId:         "YOUR_PROJECT",
  storageBucket:     "YOUR_PROJECT.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abcdef"
};
// ↑↑↑ ここまで ↑↑↑
```

> ⚠️ `databaseURL` は必須です。Realtime Database のコンソールページ上部に表示されているURLをコピーしてください。

---

## 5. Realtime Database のセキュリティルールを設定する

Firebase コンソール →「Realtime Database」→「**ルール**」タブ

以下のルールを貼り付けて「公開」してください：

```json
{
  "rules": {
    "itinerary": {
      ".read": true,
      ".write": true
    }
  }
}
```

> 解説: 読み取りは誰でもOK（観覧者用）、書き込みも誰でもOK。  
> 編集キーによるアクセス制限はアプリ側で行っているため、これで十分です。

---

## 6. GitHub Pages で公開する

```bash
# 1. GitHubリポジトリを作成（例: tabi-no-shiori）

# 2. ファイルをプッシュ
git init
git add index.html README.md
git commit -m "旅のしおり 初回"
git branch -M main
git remote add origin https://github.com/あなたのID/tabi-no-shiori.git
git push -u origin main

# 3. GitHub → Settings → Pages
#    Source: Deploy from a branch
#    Branch: main / (root)
#    → Save
```

公開URL: `https://あなたのID.github.io/tabi-no-shiori/`

---

## 7. 共有リンクを作る

アプリを開いたら、画面上部の **「🔗 共有リンクを作成」** ボタンをクリック。

| リンク種別 | 用途 | 例 |
|---|---|---|
| **編集者リンク** | 旅程を編集できる | `https://.../?mode=edit&key=ABC123` |
| **観覧者リンク** | 閲覧のみ（編集不可） | `https://.../?mode=view` |

1. 「自動生成」ボタンで編集キーを発行
2. 「キーを保存してリンク更新」をクリック
3. 編集者には編集リンクを、その他のメンバーには観覧リンクを共有

---

## データの流れ

```
編集者が変更
    ↓
Firebase Realtime Database に即時保存
    ↓
観覧者のブラウザがリアルタイムで受信・表示
```

Firebase の無料枠（Spark プラン）で十分動きます：
- 同時接続: 100接続
- ストレージ: 1GB
- 転送量: 10GB/月

---

## トラブルシューティング

| 症状 | 対処 |
|---|---|
| バナーに「⚠ デモモード」と表示される | `firebaseConfig` の書き換えが済んでいない |
| データが保存されない | `databaseURL` が間違っている可能性。Realtime DatabaseのコンソールURLを確認 |
| 観覧者に変更が反映されない | Realtime Databaseのルールで `.read: true` になっているか確認 |
| 編集キーが通らない | 最初にページを開いたブラウザでキーを設定・保存してから共有する |

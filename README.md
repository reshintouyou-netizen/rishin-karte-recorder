# rishin-karte-recorder

りしんカルテの録音ページ(`record.html`)を GitHub Pages で配信するためのリポジトリです。

## なぜこのページが必要か

Google Apps Script の Webアプリはネストした iframe(`script.google.com` → `*.googleusercontent.com`)の中で動きますが、2026年前半から Google がこの iframe の `allow` 属性からマイク・カメラを除外したため、アプリ内から `getUserMedia()` を呼ぶと許可ダイアログすら出ずに失敗します(Google 公式トラブルシューティングの "Permissions policy violation" 参照)。

公式に推奨されている回避策は「マイクを使う機能を Apps Script の外のドメインでホストし、新しいウィンドウで開く」ことです。このページはその回避策の実装で、カルテ画面からポップアップとして開かれ、録音した音声チャンクを `postMessage` で呼び出し元へ返します。

## 仕組み

```
カルテ画面(Apps Script) --window.open--> record.html(このページ)
record.html: マイクで録音(24kbpsモノラル・1分ごとのチャンク・90分で自動停止)
record.html --postMessage(音声チャンク)--> カルテ画面
カルテ画面: 認証済みセッションで Google Drive へアップロード・カルテに紐づけ
```

## ⚠️ このリポジトリに含まれないもの(公開リポジトリのため厳守)

- 患者情報(氏名・ID を含め一切なし。このページは患者情報を受け取りすらしません)
- API キー・スプレッドシート ID・Apps Script の URL
- 録音データ(音声はメモリ上で呼び出し元へ渡すだけで、このページはどこにも保存・送信しません)

## ファイル

| ファイル | 内容 |
|---|---|
| `record.html` | 録音ページ本体(外部ライブラリなし・素の JS) |

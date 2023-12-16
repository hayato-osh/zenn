---
title: "GitHub 上で LGTM 画像をペーストできたら嬉しくない？"
emoji: "✅"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["chrome", "github", "plasmo", "extension"]
publication_name: "yumemi_inc"
published: true
---

この記事は[株式会社ゆめみの23卒 Advent Calendar 2023](https://qiita.com/advent-calendar/2023/yumemi-23-graduation) 16日目の記事です。

[![YUMEMI New Grad Advent Calendar 2023](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/672609/6d8a7098-9aef-a19a-54fe-83a2d493e033.png)](https://qiita.com/advent-calendar/2023/yumemi-23-graduation)


GitHub で PR を Approve する際に、LGTM 画像を貼る人は多いのではないでしょうか。
その時、任意の LGTM 画像系のサービスから画像をコピーして、PR のコメント欄に貼り付けていませんか？

正直面倒ですよね。

それ、GitHub 上でできますよ。

https://chromewebstore.google.com/detail/copy-lgtm/lakeepohjegichaoakaaambeaipdcdmf?hl=ja

## Copy LGTM について
Copy LGTM は、GitHub 上で LGTM 画像をペーストできる Chrome 拡張です。

https://www.youtube.com/watch?v=Ajc8MXrewhY

拡張機能を入れると動画のように、Review Changes ボタンの横に Copy LGTM ボタンが追加されます。
このボタンを押すことで自動的に textarea にランダムに治安の良い LGTM 画像がペーストされます。

## 使用技術
plasmo というブラウザ拡張機能に特化したフレームワークを使っています。
`manifest.json` をあまり意識せずに、React, TypeScript で開発できるのが特徴です。（Svelte や Vue のサポートもしているようです）

https://www.plasmo.com/
plasmo の詳細についてはここでは割愛しますが、簡単に知りたい方は以下の記事がおすすめです。
https://zenn.dev/nado1001/articles/plasmo-browser-extension

## こだわり
### 治安の良さ
LGTM 画像系のサービスでは著作権的にアウトまたはグレーな画像を使用して作られた画像が多い印象がありました。
そのような画像を案件先の他社のプロダクトのリポジトリの PR に貼るのは、少し気が引けるところがありました。
そこで、Copy LGTM では著作権フリーな画像を採用し、ランダムに 20枚の画像を用意しています。

### 面倒くささからの開放
従来のサービスでは Approve のたびに別サイトに遷移して画像をコピーし、PR のコメント欄に貼り付ける必要がありました。

LGTM 画像を貼ることは本質的なことではないのに、面倒くさい作業を強いられているのはおかしいですよね？（それに気づいて！）

> 「ほな、LGTM 画像別に貼らんでええやん」

そのような回答は受け付けておりません。
LGTM 画像つきの Approve はノーコメントの Approve に比べ、穏やかな、優しい Approve な印象を受けませんか？

僕はそう思います。

### 全テーマ対応
GitHub ではテーマ設定が可能ですが、この拡張機能では全テーマに対応しています。
GitHub 側で定義された CSS 変数を使っているため、GitHub 側のテーマが変化しても、変数名が変わらない限り対応しています。（筆者が一番のユーザーなので変数名が変わった際にはすぐに対応します。）

また、ボタン押下時にペーストできたことを表示するツールチップも GitHub 側のスタイルを踏襲し、また、使用しているテーマに合わせて色が変わります。

## 今後
少し今後についても触れておきたいと思います。

先日 v2 をリリースした際に、ボタン押下時に自動的にラジオボタンの Approve が選択されてほしいという要望をいただきました。

この機能について拡張機能でそこまで実装して良いものかどうか、また、当初のスコープとはズレてしまうのではと、悩んだのですが、オプトインという形で実装予定です。
https://github.com/hayato-osh/copy-lgtm/issues/41

また、任意の LGTM 画像を貼り付けたいという要望もいただきました。
この機能に関しては当初リリース時の機能として考えていたのですが、色々あって含めなかった機能になります。

そのため、この機能に関しては plasmo の [Storage API](https://docs.plasmo.com/framework/storage) での実装を検討しています。
https://github.com/hayato-osh/copy-lgtm/issues/42

## まとめ
今回は筆者が開発した Copy LGTM について紹介しました。
便利なことは確かなので、ぜひ以下からインストールして使ってみてください。

https://chromewebstore.google.com/detail/copy-lgtm/lakeepohjegichaoakaaambeaipdcdmf?hl=ja

---
title: "SNSのアクセシブルな投稿について考える"
emoji: "🦽"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["accessibility", "アクセシビリティ"]
publication_name: "yumemi_inc"
published: true
---

## アクセシブルな投稿
1 年ほど前に X では画像の投稿時に、代替テキストをつけられる機能がリリースされました。この機能によって、音声読み上げソフトを使用するユーザーは、よりアクセシブルなコンテンツを享受できるようになりました。[^1]

では、投稿者は代替テキストをつければアクセシビリティの高いコンテンツを提供したと言えるのでしょうか。

アクセシビリティについて語る記事では、コーディングや、UI/UX の文脈で語られることが多いように感じます。しかし、SNS などユーザー投稿型サービスにおいても、各ユーザー一人ひとりが意識して取り組むべきものであると思っています。

## 画像
先述の通り、X では画像の投稿時に、代替テキストをつけられます。画像の投稿の際は代替テキストをつけることを心がけましょう。
X のヘルプセンターでは良い画像の説明を作成する方法が紹介されています。
https://help.twitter.com/ja/using-twitter/write-image-descriptions
また、代替テキストがない場合にリマインドする機能があるため、こちらの設定も有効にしておくとよいでしょう。
https://help.twitter.com/ja/using-twitter/set-image-description-reminder

## 動画コンテンツ<!-- textlint-disable ja-technical-writing/no-doubled-joshi -->
動画コンテンツは、映像と音声の 2 つの構成要素を持つことがほとんどです。そのため視覚や聴覚の障害をもった方々への一部コンテンツ及び全ての提供が難しいです。<!-- textlint-disable ja-technical-writing/no-doubled-joshi -->

X では、動画のアップロード時、キャプションファイル (.srt) をアップロードできるようです。（今回の記事を書いている際に知りました）
https://help.twitter.com/ja/using-twitter/upload-caption-srt-file
キャプションを利用することで聴覚障害者へのサポートや、騒音環境にいるユーザーにも提供できます。

## 文字コード
最近 Twitter が X に改名し、そのロゴが、Unicode の「U+1D54F」であることが話題になっていました。ユーザー名や投稿にも Unicode の特殊な文字が使用できますが、スクリーンリーダーで正しく読み上げられない場合があります。
<!-- textlint-disable ja-technical-writing/sentence-length -->
例えば、X のロゴの U+1D54F は、macOS の VoiceOver で日本語で読み上げを行ったところ、「えっくす」ではなく「Mathematical Double-Struck Capital X ...(以降聞き取れず)」と英語で長々と読み上げられます。
<!-- textlint-disable ja-technical-writing/sentence-length -->
また、フロントエンドにおけるテスト戦略の Testing Trophy を考案した Kent C. Dodds 氏が以下のようなツイートをしています。
https://twitter.com/kentcdodds/status/1083073242330361856
文中にこのような文字列を混ぜている場合、スクリーンリーダーを使用するユーザーは、意図しない形で読み上げられてしまったり、聞き取れない、そもそも読み上げられない可能性があるため、可能な限り使用を控えるのがよいでしょう。

## アニメーションGIF
X ではアニメーション GIF にも代替テキストを提供できます。GIF 使用時には代替テキストをつけるようにしましょう。

ただし、動画にも言えることですが、画像とは異なり、アニメーションの中に過度な光の点滅等がある場合は注意が必要です。見る方によっては光過敏性発作などの症状が出る可能性があるため、本当に必要な場合にのみ使用するようにし、注意喚起を行うなどの配慮をしましょう。

## 過度な絵文字
絵文字は表現を豊かにする一方で、過度な使用や適切な文脈での使用ではない場合、意図せず伝わることがあります。たとえば、以下の私の投稿を見てください。
https://twitter.com/iampigmon/status/1507011156183252994
この投稿はiOS の音声読み上げソフト VoiceOver では以下のように読み上げられます。

「たこぴーさいこう さんたこ」

macOS では以下のように読み上げられます。
<!-- textlint-disable ja-technical-writing/ja-no-successive-word -->
「たこぴーさいこう たこたこたこ」
<!-- textlint-disable ja-technical-writing/ja-no-successive-word -->
このように、絵文字が連続している場合、スクリーンリーダーで読み上げでも繰り返されることがあります。そもそも同じ絵文字を複数回使用する意味がない場合がほとんどであるため、適切な文脈での使用を心がけ、過度な使用は避けましょう。

## 最後に
ここまでアクセシブルな投稿について述べてきましたが、執筆者本人はアクセシビリティについて学習中であるため、間違った記述があれば、コメントで指摘いただけると幸いです。
他にも、X ではアクセシビリティ機能のページにおいて、『ツイート作成者にできること』としていくつかの項目が列挙されています。
これらを参考に、アクセシブルな投稿を心がけていきましょう。

https://help.twitter.com/ja/using-twitter/accessibility-features


[^1]: 訂正：1年前にリリースされたのはALTバッジのリリースで、代替テキストを付ける機能は以前からあったようです。
https://accessible-usable.net/2022/05/entry_220506.html

---
title: "CSS Modules をハッピーにする"
emoji: "🌈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["css", "cssmodules", "react", "typescript"]
publication_name: "yumemi_inc"
published: true
published_at: "2023-12-03 09:00"
---
この記事は[株式会社ゆめみの23卒 Advent Calendar 2023](https://qiita.com/advent-calendar/2023/yumemi-23-graduation)3日目の記事です。

![YUMEMI New Grad Advent Calendar 2023](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/672609/6d8a7098-9aef-a19a-54fe-83a2d493e033.png)
React × TypeScript 開発において、CSS フレームワーク選定する際に CSS Modules が候補に上がると思います。その際のネガティブな意見として開発者体験が他のフレームワークと比べて良くないというのがあるのではないでしょうか。

例えば存在しないクラス名の指定が可能であったり、クラス名の定義元を参照しようとしても `global.d.ts` に遷移してしまうといったことがあります。

そこで今回は CSS Modules での開発者体験をハッピーにする happy-css-modules の紹介と、その導入について紹介します。

## happy-css-modules とは
`Typed, definition jumpable CSS Modules. Moreover, easy!` をテーマにしたライブラリでクラス名から直接 CSS Modules 側の定義場所にコードジャンプできるようになります。
https://github.com/mizdra/happy-css-modules

このライブラリについては開発者の mizdra さん本人によるこちらの記事で詳しく紹介されていますので、本記事では割愛させていただきます。

他の類似ライブラリとの比較もされているので、ご覧ください。
https://www.mizdra.net/entry/2022/11/14/102506

## 導入
導入はとてもシンプルで
```
npm i -D happy-css-modules
```
 でインストールし、
 ```
 npx hcm 'src/**/*.module.{css,scss,less}'
```
を実行するだけです。
これだけで存在しないクラスを指定すると型エラーになり、またクラス名から直接 `.module.css` ファイルへのコードジャンプが可能になります。

上記コマンドにもある通り `Less` や `Sass` にも対応しています。

## Tips
ここからは happy-css-modules を実際のプロダクトで使う際の Tips を紹介します。

### ファイルをネストする
happy-css-modules によって生成された `.module.css.d.ts` と `.module.css.d.ts.map` はコードジャンプが可能になる一方で弊害もあります。

`.module.css` と同じ階層に生成されるため、直接参照することのない無駄なファイルが増えてしまいます。`.tsx` や `.stories.tsx` などのファイルとコロケーションしている場合は特に気になるところだと思います。

そこで、VSCode ユーザーは `Explorer file nesting` という機能を使ってファイルをネストすることができます。
```json:.vscode/settings.json
{
    "explorer.fileNesting.enabled": true,
    "explorer.fileNesting.patterns": {
        "*.module.css": "*.module.css.d.ts, *.module.css.d.ts.map"
    }
}
```
この設定によって、直接参照することのないファイルを隠すことができます。

![スクリーンショット：Explorer file nesting により Button.module.css.d.ts と Button.module.css.d.ts.map が Button.module.css の下にネストされている](/images/make-css-modules-happy/explorer-file-nesting.png)

### CLI
`hcm` コマンドで型定義ファイルを生成できますが、このままだと新しいファイルを作成するたびにコマンドを実行する必要があります。

そこで、`hcm` コマンドの watch オプションを使ってファイルの変更を検知して自動で型定義ファイルを生成するようにします。
dev サーバーを立ち上げる際に以下の npm scripts を実行するようにしておくと良いでしょう。
```json:package.json
{
    "scripts": {
        "hoge": "hcm src/**/*.module.css --watch"
    }
}
```
`--watch` 以外にも各種オプションがありますので、詳しくは `hcm --help` で確認してください。

### 生成されたファイルを ignore する
好みの話になりますが、今回の場合生成されるファイルは直接開発時に使うものではなく、watch している場合自動で生成され、開発者間で差異が出るものでもないので、 git の管理対象から外しても良いと言えます。

そのため、各種 ignore ファイルに追記しておくと良いでしょう。
```gitignore:.gitignore
# happy-css-modules
*.module.css.d.ts
*.module.css.d.ts.map
```

しかし、ignore した際に弊害として、他の開発メンバーによって`.module.css` ファイルのディレクトリを変更された場合、その変更が自身のローカル環境には反映されず、module.css は存在しないにも関わらず、`.module.css.d.ts` と `.module.css.d.ts.map` ファイルが残ってしまうという問題があります。

そのため、`.module.css` ファイルが存在しない場合は `.module.css.d.ts` と `.module.css.d.ts.map` も削除するようにすると良いでしょう。

以下は Chat GPT に生成してもらった(一部加筆修正済み) Node.js スクリプトです。
(Node.js に詳しくないのでよりよいコードの修正案をGitHubでお待ちしております。)
```js:scripts/remove-hcm.ts
const fs = require("fs");
const path = require("path");

// ディレクトリ内のファイルを再帰的に探索する関数
function deleteFilesInDirectory(startPath: string, filter: RegExp): void {
  if (!fs.existsSync(startPath)) {
    console.log("Directory does not exist: ", startPath);
    return;
  }

  const files = fs.readdirSync(startPath);
  for (let i = 0; i < files.length; i++) {
    const filename = path.join(startPath, files[i] ?? "");
    const stat = fs.lstatSync(filename);
    if (stat.isDirectory()) {
      deleteFilesInDirectory(filename, filter); // 再帰的にサブディレクトリを探索

      // サブディレクトリが空の場合、削除
      if (!fs.readdirSync(filename).length) fs.rmdirSync(filename);
    } else if (filter.test(filename)) {
      console.log("Deleting file: ", filename);
      fs.unlinkSync(filename); // ファイルを削除
    }
  }
}

// ここで対象のディレクトリとフィルターを設定
const targetDirectory = "./src"; // 探索するディレクトリのパス
const fileExtensionRegex = /\.module\.css\.d\.ts(\.map)?$/; // 削除するファイルの拡張子の正規表現

// 関数を実行して指定した拡張子のファイルを削除
deleteFilesInDirectory(targetDirectory, fileExtensionRegex);

```

## まとめ
今回は CSS Modules での開発者体験をハッピーにする happy-css-modules の紹介と、その導入について紹介しました。
CSS Modules をすでに採用しているプロジェクトにも導入しやすいと思いますので、検討してはいかがでしょうか。

# ツールのまとめ

Kyoto Dev Circle で使っているツール・ライブラリ・設定の説明です。「これ何だっけ？」と思ったときに見てください。

- 新しいツールを入れたら、Claude が追記します。気づいたことやつまずいたことがあれば、Slack で長谷川に伝えてください。
- バージョンは、そのツールを入れたときのものです。今のバージョンは、各リポジトリの package.json で確かめてください。
- Git・GitHub・VS Code・Slack など Phase 0 で覚えた道具の使い方は、[Git/GitHub はじめてガイド](https://github.com/kyoto-dev-circle/self-intro/blob/main/docs/git-guide.md) を見てください。

## 目次

| ツール | ひとことで | 最初に使った場所 |
| --- | --- | --- |
| [Node.js](#nodejs) | ブラウザの外で JavaScript を動かす | mini-games #1 |
| [npm](#npm) | ツールのインストールとコマンドの実行 | mini-games #1 |
| [Vite](#vite) | 開発用サーバーと、公開用のビルド | mini-games #1 |
| [ESLint](#eslint) | バグになりやすい書き方を指摘する | mini-games #2 |
| [Prettier](#prettier) | コードの見た目を自動でそろえる | mini-games #2 |
| [VS Code の設定と拡張機能](#vs-code-の設定と拡張機能) | 保存したときの自動整形、エラーの表示 | mini-games #2 |

---

## Node.js

**何をするものか**
JavaScript を、ブラウザの外（自分の PC）で動かすためのソフトウェアです。JavaScript はもともとブラウザの中で動く言語ですが、Node.js があると、ターミナルから JavaScript のプログラムを実行できます。Vite・ESLint・Prettier などの開発用ツールも、JavaScript で作られていて Node.js の上で動いています。

**なぜ入れたか**
このあとのツール（npm・Vite・ESLint など）を動かす土台として必要なため。

**入れ方**
公式サイト（https://nodejs.org/）から **LTS 版**（長期サポート版。安定していて長く使える版）をインストールします。2人で大きいバージョン（例：24.x の 24）をそろえます。

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `node -v` | Node.js のバージョンを表示する（入っているかの確認） |

**最初に使った場所**：mini-games #1

---

## npm

**何をするものか**
Node.js と一緒に入る、**ツールやライブラリを管理する道具**です（Node Package Manager の略）。主に2つのことをします。

1. **インストール**：世界中の人が公開しているツール（パッケージ）を、プロジェクトに入れる
2. **コマンドの実行**：package.json に書いた「よく使うコマンド」を短い名前で実行する

**関係するファイル**

| ファイル | 中身 | コミットするか |
| --- | --- | --- |
| `package.json` | プロジェクトの名前、使っているツールの一覧（devDependencies）、コマンドの一覧（scripts） | する |
| `package-lock.json` | 実際に入ったツールの正確なバージョンの記録。2人が同じバージョンを入れるために使う | する（手で書き換えない） |
| `node_modules/` | インストールしたツールの本体。とても大きい | **しない**（.gitignore に入っている） |

`node_modules/` は `npm install` でいつでも作り直せるので、Git には入れません。

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `npm -v` | npm のバージョンを表示する |
| `npm install` | package.json と package-lock.json をもとに、必要なツールを全部入れる。**clone した直後や、相方の PR がマージされて package.json が変わったあと**に実行する |
| `npm install -D ツール名` | 開発用のツールを新しく追加する。`-D` は「開発のときだけ使う」（devDependencies に入る） |
| `npm install -D --save-exact ツール名` | バージョンを固定して追加する（Prettier で使った） |
| `npm ci` | package-lock.json のとおりに、きっちり同じものを入れる。CI（自動チェック）で使う |
| `npm run` | 使えるコマンド（scripts）の一覧を表示する |
| `npm run 名前` | scripts に書いたコマンドを実行する（例：`npm run dev`） |

**つまずいたこと**

- `npm error Missing script: "lint"`：package.json の `"scripts"` に `lint` を書いていない、または保存していない。`npm run` で一覧を出して確かめる。

**最初に使った場所**：mini-games #1

---

## Vite

**何をするものか**
読み方は「ヴィート」。Web サイトを作るときの2つの仕事をしてくれるツールです。

1. **開発用サーバー**（`npm run dev`）：自分の PC の中で、作っているページをブラウザで見られるようにする。ファイルを保存すると、ブラウザの表示がすぐ変わる
2. **ビルド**（`npm run build`）：公開用に、ファイルをまとめて小さくし、`dist/` フォルダに出力する

**なぜ入れたか**
ファイルを保存するたびにブラウザで確認でき、公開用のファイルも1つのコマンドで作れるため。

**関係するファイル**

| ファイル | 中身 |
| --- | --- |
| `index.html` | 最初に表示されるページ |
| `src/main.js`、`src/style.css` | ページで動く JavaScript と見た目 |
| `vite.config.js` | Vite の設定。`base: '/mini-games/'` を書いている |
| `dist/` | `npm run build` で作られる公開用のファイル。コミットしない |

`base` は、公開URLが `https://kyoto-dev-circle.github.io/mini-games/` のように `/mini-games/` の下になるために必要な設定です。書かないと、公開したときに CSS や JS が読み込まれません。

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `npm run dev` | 開発用サーバーを起動する。`http://localhost:5173/mini-games/` を開く。止めるときはターミナルで **Ctrl + C** |
| `npm run build` | `dist/` に公開用のファイルを作る |
| `npm run preview` | ビルドした `dist/` を、公開したときと同じ形で確かめる |

**つまずいたこと**

- Vite 8 の見本（Vanilla）では、見本の画像が `public/` ではなく `src/assets/` に入る。見本を消すときは `src/assets/` ごと消す。
- `npm run build` が `UNRESOLVED_IMPORT` で失敗した：main.js を保存していなかった。エディターで変えても、**Ctrl + S で保存しないとファイルは変わらない**（タブの ● は未保存の印）。

**最初に使った場所**：mini-games #1（バージョン 8.3.6）

---

## ESLint

**何をするものか**
読み方は「イーエスリント」。JavaScript のコードを読んで、**バグになりやすい書き方を見つけて教えてくれる**ツールです。たとえば次のようなものを見つけます。

- 作ったのに一度も使っていない変数（`no-unused-vars`）
- どこにも定義されていないものを使っている（`no-undef`）

**なぜ入れたか**
人が見落としやすいミスを、レビューの前に機械で見つけるため。#4 の CI（自動チェック）でも使う。

**関係するファイル**：`eslint.config.js`

```js
import js from '@eslint/js';
import globals from 'globals';
import eslintConfigPrettier from 'eslint-config-prettier/flat';
import { defineConfig, globalIgnores } from 'eslint/config';

export default defineConfig([
  globalIgnores(['dist/']),
  {
    files: ['**/*.js'],
    plugins: { js },
    extends: ['js/recommended'],
    languageOptions: { globals: globals.browser },
  },
  eslintConfigPrettier,
]);
```

| 行 | 意味 |
| --- | --- |
| `globalIgnores(['dist/'])` | `dist/` はビルドで作られたファイルなのでチェックしない |
| `files: ['**/*.js']` | すべての `.js` ファイルをチェックする |
| `extends: ['js/recommended']` | ESLint がおすすめするルールのセットを使う |
| `globals: globals.browser` | 「このコードはブラウザで動く」と教える。これがないと、`document` や `window`（ブラウザが用意しているもの）を「定義されていない」と指摘してしまう |
| `eslintConfigPrettier` | Prettier とぶつかるルールをオフにする。後に書いた設定が優先されるので、**一番最後**に置く |

一緒に入れたもの：

| パッケージ | 役割 |
| --- | --- |
| `eslint` | ESLint の本体 |
| `@eslint/js` | おすすめのルールのセット（`js/recommended`） |
| `globals` | 「ブラウザにはこういうものがある」という一覧 |
| `eslint-config-prettier` | Prettier とぶつかるルールをオフにする設定 |

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `npm run lint` | すべてのファイルをチェックする。何も出なければ OK |

**エラーの読み方**

```
C:\Users\...\src\main.js         ← どのファイルか
  3:7  error  'a' is assigned a value but never used  no-unused-vars
  ↑行:列        ↑何がおかしいか                        ↑ルール名
```

VS Code で **Ctrl + G** を押して行番号を入れると、その行に移動できます。

**豆知識**
ESLint 9 から、設定ファイルが `eslint.config.js`（flat config という書き方）になりました。ネットで調べるときは「eslint flat config」で検索します。古い記事の `.eslintrc` という書き方は使えません。

**最初に使った場所**：mini-games #2（バージョン 10.14.0）

---

## Prettier

**何をするものか**
読み方は「プリティア」。コードの**見た目を自動でそろえる**ツールです（フォーマッターといいます）。`'` と `"` のどちらを使うか、字下げ（インデント）の幅、行末の `;`、1行の長さなどを、決まったルールで直します。JavaScript だけでなく、HTML・CSS・JSON・Markdown も整形します。

**なぜ入れたか**
2人で書くと、人によってクセが出る。Prettier でそろえておけば、レビューで「インデントがずれてる」のような指摘をしなくて済み、中身のことだけを話せる。

**ESLint との違い**

| | ESLint | Prettier |
| --- | --- | --- |
| 見るもの | 中身（バグになりそうか） | 見た目（書き方のそろい方） |
| 直すか | 教えてくれる（自分で直す） | 自動で直す |

**関係するファイル**

| ファイル | 中身 |
| --- | --- |
| `.prettierrc` | 整形のルール。`"singleQuote": true`（文字列は `'`）、`"semi": true`（文の最後に `;`） |
| `.prettierignore` | 整形しないファイル。`dist` と `package-lock.json` |

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `npm run format` | すべてのファイルを整形する（**ファイルが書き換わる**） |
| `npm run format:check` | 整形が必要かを確かめるだけ（ファイルは書き換えない）。CI ではこちらを使う。OK なら `All matched files use Prettier code style!` |

**決めたこと**

- Prettier はバージョンが変わると整形の結果が変わることがあるので、`--save-exact` でバージョンを固定している（package.json で `^` が付いていない）。
- 初めて `npm run format` をしたときの変更は、`style:` の別のコミットにした。設定の変更と見た目だけの変更を分けて、レビューしやすくするため。
- Markdown の表も整形され、列の幅がそろう（`| --- |` が長くなる）。中身は変わっていない。

**最初に使った場所**：mini-games #2（バージョン 3.9.10）

---

## VS Code の設定と拡張機能

**何をするものか**
リポジトリの中の `.vscode/settings.json` に書いた設定は、そのフォルダを VS Code で開いたときだけ使われます。リポジトリに入れておくと、clone した2人が同じ設定で作業できます。

**関係するファイル**：`.vscode/settings.json`

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true
}
```

| 行 | 意味 |
| --- | --- |
| `editor.defaultFormatter` | 整形に Prettier の拡張機能を使う |
| `editor.formatOnSave` | **Ctrl + S で保存したときに自動で整形する** |

**入れておく拡張機能**（VS Code 左の「拡張機能」で検索してインストール）

| 拡張機能 | 役割 |
| --- | --- |
| Prettier - Code formatter | 保存したときの自動整形に使う |
| ESLint | ESLint の指摘を、エディターの中で波線で表示する（`npm run lint` を実行しなくても分かる） |
| GitHub Pull Requests | VS Code から PR を出したり見たりする（Phase 0 で導入） |

**つまずいたこと**

- Vite が作った `.gitignore` に `.vscode/*` が書かれていて、settings.json がコミットされなかった。`.gitignore` に `!.vscode/settings.json`（先頭の `!` は「これは無視しない」）を足して解決した。

**最初に使った場所**：mini-games #2

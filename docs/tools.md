# ツールのまとめ

Kyoto Dev Circle で使っているツール・ライブラリ・設定の説明です。「これ何だっけ？」と思ったときに見てください。

- 新しいツールを入れたら、Claude が追記します。
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
| [Vitest](#vitest) | ロジックが正しく動くかを自動で確かめる（単体テスト） | mini-games #3 |
| [GitHub Actions](#github-actions) | PR ごとに lint・テスト・ビルドを GitHub 上で自動で実行する（CI） | mini-games #4 |
| [GitHub Pages](#github-pages) | main にマージすると、サイトを自動で公開する（CD） | mini-games #5 |

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

Vite が作った `.gitignore` には `.vscode/*`（.vscode フォルダを Git に入れない）が書かれています。そこに `!.vscode/settings.json`（先頭の `!` は「これは無視しない」）を足して、settings.json だけはコミットされるようにしています。

**入れておく拡張機能**（VS Code 左の「拡張機能」で検索してインストール）

| 拡張機能 | 役割 |
| --- | --- |
| Prettier - Code formatter | 保存したときの自動整形に使う。**入っていないと、保存しても整形されない** |
| ESLint | ESLint の指摘を、エディターの中で波線で表示する（`npm run lint` を実行しなくても分かる） |
| GitHub Pull Requests | VS Code から PR を出したり見たりする（Phase 0 で導入） |

**改行コード（CRLF と LF）**
行の終わりを表す見えない文字には、Windows 式の **CRLF** と、Mac・Linux 式の **LF** があります。Prettier は LF にそろえます。Windows の VS Code で新しく作ったファイルは CRLF になることがあり、拡張機能「Prettier - Code formatter」が入っていないと保存しても直らず、`npm run format:check` で `[warn]` が出ます。

- 直し方：`npm run format` を実行する。または VS Code の右下の `CRLF` を押して `LF` を選ぶ
- 防ぎ方：拡張機能「Prettier - Code formatter」を入れておく（保存したときに LF に直る）

clone や pull をしただけで、ほとんど全部のファイルに `[warn]` が出るときは、Git が取り出すときに CRLF に変えています（Git for Windows の設定 `core.autocrlf` が `true`）。

1. 確かめる：`git ls-files --eol src/main.js` で `w/crlf`（手元が CRLF）と出る
2. Git の設定を変える：`git config --global core.autocrlf input`（取り出すときに CRLF に変えない）
3. `npm run format` で LF に直す
4. `git status` で modified が並んでいても、`git diff` が何も出さなければ中身は同じ。`git restore .` で消える

**最初に使った場所**：mini-games #2

---

## Vitest

**何をするものか**
読み方は「ヴィーテスト」。**テスト**（プログラムが正しく動くかを確かめるプログラム）を実行するツールです。「この関数に 1 と 2 を渡したら 3 が返るはず」のような確かめ方をコードで書いておくと、コマンド1つで全部まとめて確かめられます。関数1つずつを確かめるテストを**単体テスト**（ユニットテスト）といいます。

**なぜ入れたか**
ゲームのルール（logic.js）を変えたときに、前は動いていたところが壊れていないかを、毎回手で遊ばずに確かめるため。#4 の CI でも PR ごとに実行する。Vite と同じ仲間のツールなので、`vite.config.js` をそのまま読み込み、追加の設定なしで動く。

**関係するファイル**

| ファイル | 中身 |
| --- | --- |
| `package.json` | scripts の `test`（`vitest run`）と `test:watch`（`vitest`） |
| `〇〇.test.js` | テストファイル。テストするファイルと同じフォルダに置く（例：`games/janken/logic.js` のテストは `games/janken/logic.test.js`）。名前が `.test.js` で終わるファイルを、Vitest が自動で見つけて実行する |
| `src/lib/sample.js`、`src/lib/sample.test.js` | 書き方の見本（#8 で本物のテストができたら消す） |

**テストの書き方**

```js
import { describe, test, expect } from 'vitest';
import { add } from './sample.js';

describe('add', () => {
  test('1 と 2 を足すと 3 になる', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

| 書き方 | 意味 |
| --- | --- |
| `describe('名前', () => { ... })` | テストをまとめる箱（グループ） |
| `test('説明', () => { ... })` | テスト1個。説明は失敗したときに表示される |
| `expect(実際の値).toBe(期待する値)` | 2つが同じかを確かめる |
| `expect(実際の値).toBeCloseTo(期待する値)` | 小数を比べるときに使う。`0.1 + 0.2` はコンピュータの中では `0.30000000000000004` になり、`toBe(0.3)` だと失敗するため |

テストしやすくするため、テストする関数には画面の処理（`document` や `window`）を書かず、入力を受け取って結果を返すだけにします（mini-games の README の「logic.js には画面の処理を書かない」）。

**よく使うコマンド**

| コマンド | 意味 |
| --- | --- |
| `npm test` | テストを1回だけ実行して終わる。PR を出す前と CI ではこちら。`test` だけは `run` を付けずに打てる |
| `npm run test:watch` | 終わらずに待ち、ファイルを保存するたびにテストを実行し直す。テストを書いている最中に使う。止めるときは `q` か **Ctrl + C** |

`test:watch`（`vitest`）は終わらずに待ち続けるので、CI では使えません。CI で `test`（`vitest run`）を使うのはそのためです。

**失敗したときの読み方**

```
 FAIL  src/lib/sample.test.js > add > 1 と 2 を足すと 3 になる   ← どのファイルの、どのテストか
AssertionError: expected 3 to be 4

- Expected     ← 期待した値
+ Received     ← 実際の値

- 4
+ 3

 ❯ src/lib/sample.test.js:6:23   ← 6行目の23文字目
```

**最初に使った場所**：mini-games #3（バージョン 5.1.0）

---

## GitHub Actions

**何をするものか**
GitHub が用意したパソコン（Linux）で、決めておいたコマンドを自動で実行してくれる仕組みです。「PR が出されたとき」「main に push されたとき」などのきっかけを決めて、その手順を YAML ファイルに書きます。

- **CI**（継続的インテグレーション）：PR を出すたびに、lint・テストなどを自動で実行して、壊れていないかを確かめること
- **CD**（継続的デリバリー）：main にマージするたびに、自動で公開すること（[GitHub Pages](#github-pages) の章）

**なぜ入れたか**
手元でチェックを打ち忘れても、PR の時点で必ず同じチェックが走るようにするため。チェックが失敗した PR は、ルールセットの設定でマージできなくしている。

**関係するファイル**：`.github/workflows/ci.yml`（`.github/workflows/` に置いた YAML ファイルが、自動で読み込まれる）

```yaml
# PR を出したとき・PR に push したときに、自動でチェックを動かす
name: CI

on:
  pull_request:

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      # 1. リポジトリのファイルを取ってくる
      - uses: actions/checkout@v7

      # 2. Node.js を準備する（npm のダウンロードをキャッシュして速くする）
      - uses: actions/setup-node@v7
        with:
          node-version: 26
          cache: npm

      # 3. package-lock.json のとおりにインストールする
      - run: npm ci

      # 4. チェックを順番に実行する（1つでも失敗したら、そこで止まって赤になる）
      - run: npm run lint
      - run: npm run format:check
      - run: npm test
      - run: npm run build
```

| 書き方 | 意味 |
| --- | --- |
| `name: CI` | ワークフローの名前。PR の画面や「Actions」タブに出る |
| `on: pull_request` | いつ動くか。PR を出したとき・PR に push したとき |
| `jobs:` の下の `check` | ジョブ（ひとまとまりの仕事）の名前。**ルールセットで必須にするチェックの名前になる** |
| `runs-on: ubuntu-latest` | どのパソコンで動かすか（GitHub が用意した Linux） |
| `steps:` | 上から順番に実行する手順 |
| `uses:` | ほかの人が作った部品（アクション）を使う。`@v7` はバージョン |
| `run:` | コマンドを実行する |
| `node-version: 26` | 使う Node.js の大きいバージョン。2人の PC と同じにする |

YAML は字下げ（半角スペース2つ）で入れ子を表します。字下げがずれると動きません。Prettier が YAML も整形してくれます。

**結果の見方**

- PR の画面の下に「CI / check」が出る。黄色い丸は実行中、緑のチェックは成功、赤のバツは失敗
- 「Details」を押すと、ステップごとのログが見られる。リポジトリ上の「Actions」タブからも見られる
- 赤のバツになったら、ログで失敗したステップ（例：`Run npm run lint`）を開き、エラーを読んで手元で直し、もう一度 push する。push するとチェックがやり直される

**ルールセットとの関係**
mini-games の protect-main で「Require status checks to pass」をオンにし、`check` を必須にしています。`check` が成功していない PR は、Approve があってもマージできません。チェックの名前は、ワークフローが一度動いた後でないと選べません。

**最初に使った場所**：mini-games #4（actions/checkout v7、actions/setup-node v7、Node.js 26）

---

## GitHub Pages

**何をするものか**
GitHub が無料で用意している、Web サイトの置き場所です。`https://Organization名.github.io/リポジトリ名/` の URL でサイトを公開できます。mini-games は https://kyoto-dev-circle.github.io/mini-games/ で公開しています。

**なぜ入れたか**
main にマージするたびに、手を動かさなくても最新のサイトが公開されるようにするため（CD）。公開の作業を GitHub Actions に任せるので、公開し忘れや、手元の古いファイルを公開してしまうことがなくなる。

**設定**：リポジトリの Settings →「Pages」→「Build and deployment」の Source を「GitHub Actions」にする。

**関係するファイル**：`.github/workflows/deploy.yml`、`vite.config.js`（`base`）

```yaml
# CD：main に push されたとき（PR をマージしたとき）に、ビルドして GitHub Pages に公開する
name: Deploy

on:
  # main に push されたときに動く。PR のときは動かない（PR のチェックは ci.yml）
  push:
    branches: [main]
  # 「Actions」タブのボタンから手で動かせるようにする（やり直したいとき用）
  workflow_dispatch:

# GitHub Pages に公開するために必要な権限
permissions:
  # リポジトリの中身を読む
  contents: read
  # Pages に公開する
  pages: write
  # 公開する人が本当にこのリポジトリのワークフローかを、GitHub に証明する
  id-token: write

# 公開が2つ同時に動かないようにする（続けてマージしたら、順番に公開する）
concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  # 1つ目の仕事：ビルドして、できた dist/ を Pages 用にアップロードする
  build:
    runs-on: ubuntu-latest
    steps:
      # リポジトリのファイルを取ってくる
      - uses: actions/checkout@v7
      # Node.js を準備する（ci.yml と同じ）
      - uses: actions/setup-node@v7
        with:
          node-version: 26
          cache: npm
      # package-lock.json のとおりにインストールする
      - run: npm ci
      # 公開用のファイルを dist/ に作る
      - run: npm run build
      # dist/ を Pages 用にまとめてアップロードする
      - uses: actions/upload-pages-artifact@v5
        with:
          path: dist

  # 2つ目の仕事：アップロードしたものを公開する
  deploy:
    # build が成功してから動く
    needs: build
    runs-on: ubuntu-latest
    # 「Environments」の github-pages に、公開の記録と URL を残す
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v5
```

| 書き方 | 意味 |
| --- | --- |
| `on: push` と `branches: [main]` | main に push されたとき（PR をマージしたとき）に動く。PR のときは動かない |
| `workflow_dispatch:` | 「Actions」タブの「Run workflow」ボタンから、手で動かせるようにする |
| `permissions:` | このワークフローに許す権限。Pages に公開するには `pages: write` と `id-token: write` が必要 |
| `concurrency:` | 同じ `group` のワークフローを同時に動かさない。続けてマージしても、公開は順番に行われる |
| `jobs:` の下の `build` と `deploy` | 2つのジョブ。`build` でビルドしてアップロードし、`deploy` で公開する |
| `needs: build` | `build` が成功してから `deploy` を動かす |
| `actions/upload-pages-artifact` | `path` に書いたフォルダ（`dist`）を、Pages 用にまとめてアップロードする |
| `actions/deploy-pages` | アップロードしたものを公開する |
| `environment:` の `github-pages` | 公開の記録を残す場所。リポジトリのトップの右側「Deployments」に、公開の履歴と URL が出る |

**Vite の `base` との関係**
公開 URL はドメインの直下ではなく `/mini-games/` の下になります。`vite.config.js` に `base: '/mini-games/'` と書いておかないと、ビルドした HTML が CSS や JS をドメインの直下に探しにいき、読み込めません（真っ白なページになる）。

**結果の見方**

- main にマージすると、「Actions」タブに「Deploy」が出る。build と deploy の両方が緑のチェックになれば公開済み（1〜2分）
- Deploy は PR の画面には出ない（PR のときは動かないため）
- サイトが変わっていないように見えるときは、`Ctrl + Shift + R` で、ブラウザに残っている古いファイルを使わずに読み直す
- 真っ白なページや 404 になったときは、Deploy のログ、Pages の Source が「GitHub Actions」か、`base` を確かめる

**最初に使った場所**：mini-games #5（actions/upload-pages-artifact v5、actions/deploy-pages v5）

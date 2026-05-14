# jsdiff-es

JavaScriptのテキスト差分ライブラリ。["An O(ND) Difference Algorithm and its Variations" (Myers, 1986)](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.4.6927)で提案されたアルゴリズムに基づいています。

これはESモジュールに焦点を当てた実装です。

## 機能

*   **テキスト差分:** 文字、単語、行、文レベルでテキストを比較します。
*   **特化型差分:** CSS、JSONオブジェクト、配列の比較に特化したメソッドを含みます。
*   **パッチ処理:** unified diff形式でパッチを作成および適用します。
*   **柔軟なオプション:** 大文字・小文字の区別の無視、カスタム比較関数、ホワイトスペースの処理をサポートします。
*   **環境サポート:** Node.js、Deno、およびモダンブラウザで動作します。

## 使い方

### ブラウザ / Deno

CDNやGitHub Pagesなどのホスティングサービスから直接`jsdiff-es`をインポートできます。

```js
import { diffChars } from "https://taisukef.github.io/jsdiff-es/src/diff/character.js";

const one = 'beep boop';
const other = 'beep boob blah';

const diff = diffChars(one, other);

console.log(diff);
// [
//   { value: 'beep boo', count: 8 },
//   { value: 'p', count: 1, removed: true },
//   { value: 'b blah', count: 6, added: true }
// ]
```

### Node.js

まず、パッケージをインストールします:

```bash
npm install diff
```

その後、必要なメソッドをインポートして使用します:

```js
import { diffChars } from 'diff';
// またはCommonJSの場合: const Diff = require('diff');

const one = 'beep boop';
const other = 'beep boob blah';

const diff = diffChars(one, other);

diff.forEach((part) => {
  // 追加は緑色、削除は赤色
  // 共通部分は灰色
  const color = part.added ? 'green' :
    part.removed ? 'red' : 'grey';
  process.stderr.write(part.value[color]);
});

console.log();
```

これにより、ターミナルに色分けされた出力が生成されます。

## APIリファレンス

すべての差分メソッドは変更オブジェクトの配列を返します。

#### 変更オブジェクト

変更オブジェクトは戻り値のコアとなるもので、以下の構造を持ちます:

*   `value` (string): 変更の内容。
*   `added` (boolean | undefined): 値が新しい文字列に挿入された場合に`true`。
*   `removed` (boolean | undefined): 値が古い文字列から削除された場合に`true`。
*   `count` (number | undefined): `value`内のトークン数。

`added`も`removed`も設定されていない変更オブジェクトは、両方の文字列の共通部分です。

### テキスト差分

*   `diffChars(oldStr, newStr[, options])`: 文字単位で2つのテキストブロックを比較します。
*   `diffWords(oldStr, newStr[, options])`: 単語単位で2つのテキストブロックを比較し、ホワイトスペースを無視します。
*   `diffWordsWithSpace(oldStr, newStr[, options])`: 単語単位で2つのテキストブロックを比較し、ホワイトスペースを有意なものとして扱います。
*   `diffLines(oldStr, newStr[, options])`: 行単位で2つのテキストブロックを比較します。
*   `diffTrimmedLines(oldStr, newStr[, options])`: 行単位で2つのテキストブロックを比較し、先頭と末尾のホワイトスペースを無視します。
*   `diffSentences(oldStr, newStr[, options])`: 文単位で2つのテキストブロックを比較します。

### 特化型差分

*   `diffCss(oldStr, newStr[, options])`: CSSトークン単位で2つのテキストブロックを比較します。
*   `diffJson(oldObj, newObj[, options])`: 2つのJSONオブジェクトを比較します。入力は正規化およびプリティプリント（整形）された後、行ベースの差分が実行されます。
*   `diffArrays(oldArr, newArr[, options])`: 2つの配列を厳密な等価比較（`===`）で比較します。オプションの`comparator`関数を`options`オブジェクトに渡すことができます。

### パッチ処理

*   `createPatch(fileName, oldStr, newStr, oldHeader, newHeader[, options])`: unified diff形式のパッチを作成します。
*   `createTwoFilesPatch(oldFileName, newFileName, oldStr, newStr, oldHeader, newHeader[, options])`: 2つの別々のファイル用のunified diff形式のパッチを作成します。
*   `structuredPatch(oldFileName, newFileName, oldStr, newStr, oldHeader, newHeader, options)`: ハンク（hunk）オブジェクトの配列を含むオブジェクトを返します。
*   `applyPatch(source, patch[, options])`: unified diff形式のパッチを適用します。
*   `applyPatches(patch, options)`: 1つ以上のパッチを適用します。
*   `parsePatch(diffStr)`: パッチを構造化データオブジェクトに解析します。
*   `merge(mine, theirs, base)`: 2つのパッチを単一の結果にマージします。

### コンバーター

*   `convertChangesToXML(changes)`: 変更オブジェクトのリストをシリアライズされたXML形式に変換します。
*   `convertChangesToDMP(changes)`: 変更オブジェクトのリストをDiff-Match-Patch形式に変換します。

## ライセンス

MIT License — [LICENSE](LICENSE)を参照してください。

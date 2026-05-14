# jsdiff-es

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A JavaScript text differencing library. Based on the algorithm proposed in ["An O(ND) Difference Algorithm and its Variations" (Myers, 1986)](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.4.6927).

This is an ES module-focused implementation.

## Features

*   **Text Diffs:** Compares text by character, word, line, or sentence.
*   **Specialized Diffs:** Includes dedicated methods for comparing CSS, JSON objects, and arrays.
*   **Patching:** Create and apply patches in the unified diff format.
*   **Flexible Options:** Supports case-insensitivity, custom comparators, and whitespace handling.
*   **Environment Support:** Works in Node.js, Deno, and modern browsers.

## Usage

### Browser / Deno

You can import `jsdiff-es` directly from a CDN or a hosting service like GitHub Pages.

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

First, install the package:

```bash
npm install diff
```

Then, import and use the desired methods:

```js
import { diffChars } from 'diff';
// or for CommonJS: const Diff = require('diff');

const one = 'beep boop';
const other = 'beep boob blah';

const diff = diffChars(one, other);

diff.forEach((part) => {
  // green for additions, red for deletions
  // grey for common parts
  const color = part.added ? 'green' :
    part.removed ? 'red' : 'grey';
  process.stderr.write(part.value[color]);
});

console.log();
```

This will produce a color-coded output in the terminal.

## API Reference

All diff methods return an array of change objects.

#### Change Objects

Change objects are the core return value and have the following structure:

*   `value` (string): The content of the change.
*   `added` (boolean | undefined): `true` if the value was inserted into the new string.
*   `removed` (boolean | undefined): `true` if the value was removed from the old string.
*   `count` (number | undefined): The number of tokens in the `value`.

If a change object has neither `added` nor `removed` set, it is a common part of both strings.

### Text Differencing

*   `diffChars(oldStr, newStr[, options])`: Diffs two blocks of text, comparing character by character.
*   `diffWords(oldStr, newStr[, options])`: Diffs two blocks of text, comparing word by word, ignoring whitespace.
*   `diffWordsWithSpace(oldStr, newStr[, options])`: Diffs two blocks of text, comparing word by word, treating whitespace as significant.
*   `diffLines(oldStr, newStr[, options])`: Diffs two blocks of text, comparing line by line.
*   `diffTrimmedLines(oldStr, newStr[, options])`: Diffs two blocks of text, comparing line by line, ignoring leading and trailing whitespace.
*   `diffSentences(oldStr, newStr[, options])`: Diffs two blocks of text, comparing sentence by sentence.

### Specialized Differencing

*   `diffCss(oldStr, newStr[, options])`: Diffs two blocks of text, comparing CSS tokens.
*   `diffJson(oldObj, newObj[, options])`: Diffs two JSON objects. The input will be canonicalized and pretty-printed, and then a line-based diff is performed.
*   `diffArrays(oldArr, newArr[, options])`: Diffs two arrays, comparing each item for strict equality (`===`). An optional `comparator` function can be passed in the `options` object.

### Patching

*   `createPatch(fileName, oldStr, newStr, oldHeader, newHeader[, options])`: Creates a unified diff patch.
*   `createTwoFilesPatch(oldFileName, newFileName, oldStr, newStr, oldHeader, newHeader[, options])`: Creates a unified diff patch for two separate files.
*   `structuredPatch(oldFileName, newFileName, oldStr, newStr, oldHeader, newHeader, options)`: Returns an object with an array of hunk objects.
*   `applyPatch(source, patch[, options])`: Applies a unified diff patch.
*   `applyPatches(patch, options)`: Applies one or more patches.
*   `parsePatch(diffStr)`: Parses a patch into a structured data object.
*   `merge(mine, theirs, base)`: Merges two patches into a single result.

### Converters

*   `convertChangesToXML(changes)`: Converts a list of change objects to a serialized XML format.
*   `convertChangesToDMP(changes)`: Converts a list of change objects to the Diff-Match-Patch format.

## License

MIT License — see [LICENSE](LICENSE).
# テストの実践 ――「うるう年」問題

ここでは「うるう年」を判定するESMを作成します。
「うるう年」の判定は、通常広く使われている `date-fns` などのNPMパッケージを使用することが多いですが、ここではテストを学ぶためにあえて自分で実装します。
設計して、テストを書き、コードを書くという一連のステップでより実践的なテストとの付き合い方を学びましょう。

## 目標の決定

まず「何を作るか」明らかにしましょう。
何を作るか曖昧なまま、ただ無為にソフトウェア開発を進めるとムダを生む恐れがあります。
ムダを生まないためにできるだけ「何を作るか」を明確にしておきましょう。

「うるう年」を判定するということは、「西暦年号がうるう年ならば true を返し、そうでなければ false を返す関数」ということと決めます。

「うるう年」とは何であるかは、ここでは日本の法令を参考にして決めます。
日本の法令上の取り扱いは、明治時代に制定された「閏年ニ関スル件」によって決められています。

> 神武天皇即位紀元年数ノ四ヲ以テ整除シ得ヘキ年ヲ閏年トス但シ紀元年数ヨリ六百六十ヲ減シテ百ヲ以テ整除シ得ヘキモノノ中更ニ四ヲ以テ商ヲ整除シ得サル年ハ平年トス
>
> ―― [https://elaws.e-gov.go.jp/document?lawid=131IO0000000090](https://elaws.e-gov.go.jp/document?lawid=131IO0000000090)

「神武天皇即位紀元年数」は、通常の西暦年号でいう紀元前660年を意味します。したがって「紀元年数ヨリ六百六十ヲ減シテ」とあるのは通常の西暦年号を意味します。
端的に言うと「グレゴリオ暦法に基づいています」ということを意味します。
このままだと大変読みにくいですね。
書き換えると下記のようになります。

> 西暦年号が4で割り切れる年はうるう年。ただし、西暦年号が100で割り切れる年のうち、100で割った商が4で割り切れない年はうるう年ではない。

これを「うるう年」とします。まとめるとこうなります。

- 西暦年号が4で割り切れる年はうるう年
  - たとえば、西暦2024年、2028年、2032年は4で割り切れるので、うるう年です。
- 西暦年号が4で割り切れない年はうるう年でない
  - たとえば、西暦2021年、2022年、2023年は4で割り切れないので、うるう年ではありません。
- ただし、西暦年号が100で割り切れる年はうるう年でない
  - たとえば、西暦2100年、2200年、2300年は100で割り切れるので、うるう年ではありません。
- ただし、西暦年号が400で割り切れる年はうるう年
  - たとえば、西暦2000年、2400年、2800年は400で割り切れるので、うるう年です。

これで「何を作るか」ということが明らかになりました。
それでは、順番にテストとコードを書いていきましょう。

## 「西暦年号が4で割り切れる年はうるう年」

ファイル `isLeapYear.test.js` を作成します。
「何を作るか」ということを忘れないようにコメントに転載します。

```js
// isLeapYear.test.js
/** TODO:
西暦年号が4で割り切れる年はうるう年
  たとえば、西暦2024年、2028年、2032年は4で割り切れるので、うるう年です。
西暦年号が4で割り切れない年はうるう年でない
  たとえば、西暦2021年、2022年、2023年は4で割り切れないので、うるう年ではありません。
ただし、西暦年号が100で割り切れる年はうるう年でない
  たとえば、西暦2100年、2200年、2300年は100で割り切れるので、うるう年ではありません。
ただし、西暦年号が400で割り切れる年はうるう年
  たとえば、西暦2000年、2400年、2800年は400で割り切れるので、うるう年です。
*/
```

うるう年であることを判定するので `isLeapYear` という名前に決めました。
この名前のモジュールと関数を作成することに決めます。

テストを書いていきましょう。

```js
// isLeapYear.test.js
test("西暦年号が4で割り切れる年はうるう年", () => {
  expect(isLeapYear(2024)).toBe(true);
});
```

これをテストし、失敗することを確認します。
この失敗は、テスト環境自体の検証を行うことでもあります。

コマンド:

```bash
npm test
```

テスト結果:

```console
 FAIL  ./isLeapYear.test.js
  ✕ 西暦年号が4で割り切れる年はうるう年 (1 ms)
```

失敗しますね。
この失敗によって、次の2点を実証できました。

- 目標の「西暦年号が4で割り切れる年はうるう年」というテストが実行されること
- 未実装のテストが意図せず合格しない (fail) ということ

テスト環境の検証は、テストを行う上での重要なポイントです。

それでは、関数を実装していきましょう。
最初からすべての実装を書こうとせず、小さい変更のみで済ませるのがポイントです。

```js
// isLeapYear.js
function isLeapYear(year) {
  return year % 4 === 0;
}

export default isLeapYear;
```

ファイルを作成したら、テスト側でも `import` 文によって実装した関数を読み込みます。

```js
// isLeapYear.test.js
import isLeapYear from "./isLeapYear";

test("西暦年号が4で割り切れる年はうるう年", () => {
  expect(isLeapYear(2024)).toBe(true);
});
```

テストを実行します。

テスト結果:

```console
 PASS  ./isLeapYear.test.js
  ✓ 西暦年号が4で割り切れる年はうるう年 (1 ms)
```

これでテストは合格しました。
念の為、西暦2024年のケースだけでなくほかのケースもテストしてみましょう。

「西暦年号が4で割り切れる年はうるう年」という目標を達成したと判断したら、コメントからは消しておきます。

```js
// isLeapYear.test.js
import isLeapYear from "./isLeapYear";

test("西暦年号が4で割り切れる年はうるう年", () => {
  expect(isLeapYear(2024)).toBe(true);
  expect(isLeapYear(2028)).toBe(true);
  expect(isLeapYear(2032)).toBe(true);
});

/** TODO:
西暦年号が4で割り切れない年はうるう年でない
  たとえば、西暦2021年、2022年、2023年は4で割り切れないので、うるう年ではありません。
ただし、西暦年号が100で割り切れる年はうるう年でない
  たとえば、西暦2100年、2200年、2300年は100で割り切れるので、うるう年ではありません。
ただし、西暦年号が400で割り切れる年はうるう年
  たとえば、西暦2000年、2400年、2800年は400で割り切れるので、うるう年です。
*/
```

次の目標「西暦年号が4で割り切れない年はうるう年でない」に進めていきます。

テストを書き、実行します。

必要に応じて実装を修正します。

これらのテストも問題なく合格するようになれば、「西暦年号が4で割り切れない年はうるう年でない」という目標も達成したと判断して、コメントから消しておきます。

```js
// isLeapYear.test.js
import isLeapYear from "./isLeapYear";

test("西暦年号が4で割り切れる年はうるう年", () => {
  expect(isLeapYear(2024)).toBe(true);
  expect(isLeapYear(2028)).toBe(true);
  expect(isLeapYear(2032)).toBe(true);
});

test("西暦年号が4で割り切れない年はうるう年でない", () => {
  expect(isLeapYear(2021)).toBe(false);
  expect(isLeapYear(2022)).toBe(false);
  expect(isLeapYear(2023)).toBe(false);
});

/** TODO:
ただし、西暦年号が100で割り切れる年はうるう年でない
  たとえば、西暦2100年、2200年、2300年は100で割り切れるので、うるう年ではありません。
ただし、西暦年号が400で割り切れる年はうるう年
  たとえば、西暦2000年、2400年、2800年は400で割り切れるので、うるう年です。
*/
```

## 続きの課題

残りの目標に関しても同様に進めていきましょう。

- ただし、西暦年号が100で割り切れる年はうるう年でない
  - たとえば、西暦2100年、2200年、2300年は100で割り切れるので、うるう年ではありません。
- ただし、西暦年号が400で割り切れる年はうるう年
  - たとえば、西暦2000年、2400年、2800年は400で割り切れるので、うるう年です。
# 初めに
ReactみたいなフロントエンドFWって難しいよね。  
とくにメタ的な部分が。  

Nodeが必要だったり、複数のパッケージマネージャー、プロジェクト作成用ツール、謎のディレクトリたち（node_modules, package.json, yarn.lock)などなど

そのうえReactはただのViewライブラリなので、それ以外を補完するためのエコシステムを選んで使用する必要がある。  
ただでさえたくさん要素がうえに各種ツールの栄枯盛衰もあるので、混乱しがち。

# なぜフロントエンドの開発でバックエンド用のNodeが必要なのか
- パフォーマンス最適化のためにJavaScriptやCSSファイルを少数のファイルにまとめる（＝バンドル）
- 新しいバージョンのJavaScriptやAltJSのコードを古いバージョンのJavaScriptコンパイルして、古いブラウザでも動作可能にする
- 開発環境においてブラウザにローカルファイルを直接読み込ませるのではなく、ローカルに開発用のアプリケーションサーバを稼働させることで、動作を検証しやすくし開発の効率を高める
- テストツールを用いてユニットテストやE2Eテストを記述・実行する
- ソースコードの静的解析や自動生計を行う

# nodeコマンドの使い方
コマンドラインで`python`コマンドを実行するとREPLが実行できるように、`node`と実行することでREPLが起動できる。  
このREPLが割りと高性能らしく、REPL内で複数のコマンドを使用できたり、コマンド保管してくれたりする。  

コマンドの一覧は`.help`で確認可能。  
`.load`コマンドが便利で、ファイルを指定することで簡単に指定したファイル内の関数・メソッドなどが呼び出せる。
```
> .load ファイル名
```

# viteでプロジェクトを作成する
## vite（ヴィート）とは
`vite`は、`Vue.js`の開発者（Evan You氏）によって作られたビルドツール。  

`Vue.js`だけでなく`React`のビルドもサポートしている。

## viteがやってくれること
Reactでそれなりの規模のアプリケーションを作成する場合には、いろんな機能が必要で、
- 最新仕様のJS
- JSXを古いブラウザでも実行可能に変換するためのコンパイラ
- JS及びCSSをひとつにまとめminifyするためのバンドラ

などなど  
またこれらを導入した上で、それらが連携して動作するように複雑な設定を行う必要がある。

そのため、Reactのアプリケーションプロジェクトを作成・運用するためのツールが公式やサードパーティから提供されている。  
その一つが`vite`。 

他にも
- Webpack
- create-react-app
- esbuild

などが存在する

# viteでプロジェクトを作成する
コマンドラインで以下のコマンドを作成することで、Reactのプロジェクトを作成できる
```
$ yarn create vite hello-world --template=react-ts
```
ここではプロジェクトのテンプレートとして`react-ts`を指定することで、
`React`と`TypeScript`を利用するプロジェクトが作成される。 

コマンドが正しく完了すると`hello-world`というディレクトリ名のプロジェクトが作成される。  


# プロジェクト作成後の謎のディレクトリたち
フロントエンドのプロジェクトには、メインロジックやUIとは別のディレクトリやファイルがたくさん登場する。  

例えば以下のようなプロジェクトの場合は、`src`がメインロジックやUIが入る。  
他のディレクトリたちは何のためのものなのか。  
- hello-world/
  - index.html
  - node_modules
  - package.json
  - public
  - src
  - tsconfig.json
  - tsconfig.node.json
  - vite.config.ts
  - yarn.lock

これらは一体なんのためのものなのかを確認していく。  

## node_modules
`node_modules`はプロジェクト内で使用されるパッケージの実行ファイルが置かれるディレクトリである。  


<br>

フロントエンドのプロジェクトを初めて動かす前に、プロジェクトルートで以下のようなコマンドを実行したことがある人も多いのではないだろうか。  
```
npm install
```
```
yarn
```
```
yarn install
```

これらのコマンドは、`package.json`に記述されている依存パッケージを`node_modules`配下にインストールして、  
インストールされたパッケージのバージョン情報を、その依存関係も含めて`package-lock.json`や`yarn.lock`というファイルに出力するというもの。  

そのため実際にフロントエンドのプロジェクトを実行する前には必須のコマンドとなる。  

パッケージマネージャに`npm`をしている場合は`npm install`だし、`yarn`を使用している場合は`yarn`もしくは`yarn install`を実行する。  
ここの`yarn`コマンドは略式表記で、正確には`yarn install`を実行している。  

## package-lock.jsonとyarn.lock
`package-lock`や`yarn.lock`は、`node_modules`にインストールしたパッケージの依存情報を保存しておくためのファイル。

<br>


`node_module`内を確認してもらうとわかる通り、すごい数のディレクトリができているはず。  
前述した通り、これらはプロジェクトで使用するためのパッケージの実行ファイルが格納されており、それらのパッケージは相互に特定のバージョンに依存している。  

そのためちょっとバージョンを変更しただけでアプリケーションが動かなくなる場合がある。  

だからいつ誰がインストールしてもすべてのパッケージで完全に同じバージョンがインストールされるよう、インストールしたパッケージの依存情報を保存しておくためのファイルが`package-lock.json`や`yarn.lock`ファイルである。  

<br>

そのため、Gitリポジトリに`node_modules`は含めないが、`package-lock.json`や`yarn.lock`は必ず入れておくようにする。



## package.json
`package.json`には、プロジェクト内で必要とするパッケージの一覧が記述されている。  


# Hello JavaScript
正式名称は「ECMAScirpt」  
「JavaScript」というのは、元々NetScape社によって開発された`ECMAScript`を、`Mozilla Foundation`が引き継いでいるプロダクトとしての名前である。  
当時流行っていたJavaにあやかってこのような名前となったが、Javaとは全く持って関係の無いプログラミング言語。(マリオブラザーズとスーパーマリオくらい違う)

これまで`ECMAScript`は言語仕様が変更されたため、大きなアップデートが行われるたびに`ES5`や`ES6`のように呼ばれてきたが、  
2015年の`ES6`からエディション名ではなく年号月の仕様署名で呼ぶことが推奨され`ES 2015`と呼ばれるようになった。  
この`ES2015`では大きく言語仕様が改定され、最近のJavaScriptのコーディングはこのときに改定された内容が主流になりつつある。   


JSは登場以前のCやJavaなどのプログラミング言語では馴染みのない概念が多く登場したり、  
初期JSの安全性の低い時期もあったことから「できるだけJSは使いたくない」と言われることが多い。  
しかし近年のアップデートにより、それらはかなりカバーされているためよく好まれる言語の一つでもある。

JavaScriptの特徴
- 第一級関数とクロージャをサポートしている
- 構造体ともクラスインスタンスとも異なる、シンプルで柔軟なオブジェクト
- 表現力の高いリテラル記法

## 第一級関数
関数を値として扱うことができるようになる。  

そのため以下のように、関数の定義を変数に格納して変数を使用して関数を呼び出す事ができるようになる。
```js
const foo = () => {
  console.log("foobar");
}
foo(); // 変数を使用して呼び出し
// foobar
```

## クロージャ
関数内に関数とデータを記述し、データと関数を紐付けることで保守性の高いプログラムを記述する方法。

```js
// 関数の外に設定していた変数はなくなりました

function counter() {
  // 関数内に変数を宣言し用意する
  let count = 0;

  // 関数内にcount変数を利用する関数を定義する
  function returnCounter() {
    count = count + 1;
    console.log(count);
  }

  // リターンとして、returnCounter関数の定義を返す。あくまで『定義』。
  return returnCounter;
}

// counter()を実行して、リターンとしてreturnCounter関数の『定義』を受け取ります。
const myCounter = counter();

// myCounterに()をつけて『returnCounter()』として実行しています。
myCounter();　// 1が出力される
myCounter();　// 2が出力される
myCounter();　// 3が出力される
```

# 変数の宣言
変数の宣言するための構文として`var`が有名だが、今のJSには`const`と`let`が追加され、計以下の３つがある。
- var
- let
- const


|       | 再代入 | 再宣言 |
| ----- | ------ | ------ |
| var   | ○      | ○      |
| let   | ○      | ×      |
| const | ×      | ×      |


しかし従来からある`var`にはいくつかの安全性に欠ける問題があるため、使ってはならない。
- 再宣言および再代入が可能
- 変数の参照が巻き上げられる
- スコープ単位が関数

`const`をメインでどうしようもない場合に`let`を使用することで、安全なプログラミングが可能。

#　JSにおけるデータ型
JavaScriptではJavaと同様、データが以下の２つに大分される。
- プリミティブ型
  - インスタンスメソッドを持たないデータ
- オブジェクト型


## プリミティブ型
JSにおけるプリミティブ型は７種類
- Boolean
  - `true`及び`false`の２つの真偽値を扱うデータ型
- Number
  - 数値を扱うためのデータ型。他の多くの言語と異なり、整数も少数もNumber型になる
- Bigint
  - Numberでは扱いきれない大きな値を扱うためのデータ型。ES2020で追加された。Number型と互換性がなく、相互に代入や計算、当地比較などは行えない。
- String
  - 文字列（テキストを表す連続した文字）扱うためのデータ型
- Symbol   
  - シンボル値という固有の識別子を表現する値。ES2015から追加された。Symbol()関数を呼び出すことで動的に生成されるが、基本的に同じシンボル値をあとから表示できない。オブジェクトのプロパティキーとして使用可能。
- Null  
  - プリミティブ値`null`はなんのデータも存在しない状態を明示的に表す
- Undefined
  - プリミティブ値`undefined`は宣言のみが行われた変数や、オブジェクト内の存在しないプロパティへのアクセスに割り当てられる。他の多くの言語と異なり、`null`と明確に区分される

これらのうち`Bigint`は扱える環境が限定的だったり、`Symbol`はJSONでのパースができなかったりと不便なので活用の場面が無い。  

この内、以下の型がfalseと扱われる。
- false
- 0
- NaN
- ''(空文字)
- null
- undefined

### NaNとは
Number型でありながら、数値ではない（Not a Number）を示す値のこと。  

発生条件は以下の通り
- 0同士の除算
- NaNを含んだ演算
- その他の無効な演算および処理
```
> Math.sqrt(-1)     // -1の平方根
NaN
> Infinity * 0      // 無限大と0の乗算
NaN
> parseInt('foo')   // 数字以外の文字列を数値としてのパース
NaN
```

## プリミティブ値のリテラルとラッパーオブジェクト
プリミティブ型はインスタンスメソッドを持たない。  
しかし以下のようなコードを実行するとメソッドを使うことができる。  
そのためPythonやRubyのようにすべてのデータがオブジェクトなのではないかと思われるかもしれない。
```js
> 'Hello python'.replace("python", "JavaScript")
'Hello JavaScript'
```

しかしそうではない。  
JavaScriptでは、`プリミティブ型の値にアクセスしようとするとき、その対応するラッパーオブジェクトに変換する」という仕様が存在するからである。  

先ほどの例は、内部では以下のように変換されて実行されている。
```js
> new String('Hello python').replace("python", "JavaScript")
'Hello JavaScript'
```

replaceが実行できるのは、プリミティブ型がメソッドを持つのではなく、変換されたStringオブジェクトのインスタンスメソッドを実行していたということ。

## オブジェクト型とそのリテラル
オブジェクトの場合でもリテラルを持つ場合がある。  

- 配列リテラル
  - `[1,2,3]`の形式で記述する。`[]`で空を表す。
- オブジェクトリテラル
  - `{key: value}`の形式で記述する。キーには文字列またはシンボルが用いられる。（数値を利用すると自動的に文字列に変換される）
  - 任意のプロパティにアクセスするには`obj[key]`もしくは`obj.key`でアクセスできる。
  - `Object`オブジェクトのインスタンスとして生成される。
- 正規表現リテラル
  - `patterrn/flags`の形式で記述される。正規表現パターンでの特殊文字の使い方はほぼ共通。`RegExp`オブジェクトのインスタンスとして生成される。

# 関数の定義
JavaScriptでの関数は、第一級オブジェクトである。  
ゆえに関数を値をとして扱うことができることから、文・式のどちらでも定義可能。  

- 式
  - 評価された後に値が返るもの
- 文
  - 手続きを記述し、命令するもの

例えばif文は式ではないため、変数に入れようとすると怒られるが、
```js
> const statement = if (2===2) 'python' else 'javascript';
const statement = if (2===2) 'python' else 'javascript';
                  ^^

Uncaught SyntaxError: Unexpected token 'if'const greet
```

functionは式として扱うことができるので、関数を値として扱うことができる
```js
> const callable = function (n){
... return n*2;
... };
undefined
> callable(2) // 変数callableを関数として扱うことができる
4 
```

関数を文として扱う際は以下の通り。  
```js
function callable(n) {
  return n*2
}
```

JSでは基本的に末尾にセミコロン`;`が必要だが、`{}`ブロックで終わる場合はその限りではない。  
そのため関数式の場合は代入文なので`;`が必要だが、関数文の場合は必要ない。

## JavaScriptにおけるメソッド
多くのプログラミング言語では、オブジェクトに紐づく関数のことをメソッドと呼ぶが、  
JavaScriptの場合では異なる場合がある。

関数は第一級オブジェクトなので、オブジェクトのプロパティ値にもなれる。  
そして単にオブジェクトのプロパティとなっている関数のことを「メソッド」と呼ぶ。  

以下のようなオブジェクトがある場合、fooオブジェクトのbazプロパティはメソッドということになる。
```js
const foo = {
  bar: 'bar',
  baz: function () {
    console.log('I am `baz` method');
  },
};

foo.baz()
```

オブジェクトプロパティとしてのメソッド記法にはもう一つあって、結果は全く同じだが簡略化した記法がある。
```js
const fuu = {
  bar: 'bar',
  baz() {
    console.log('I am `baz` method');
  },
}

fuu.baz()
```

## アロー関数
JavaScriptの関数式には、`function`キーワードでの定義とは別に*アロー関数*というものがある。  
これは`ES2015`から加わったものである。

```js
// functionによる関数式
const foo = function(n) {
  return n + 1;
}

// アロー関数式
const bar = (n) => {
  return n + 1;
};

// アロー関数式、さらに省略記法
const baz = n => n + 1;

console.log(foo(1));    // 2
console.log(bar(1));    // 2
console.log(baz(1));    // 2
```

アロー関数を利用することで簡単に関数を記述することができる。  
また引数が一つだけの場合はカッコをさらに省略できるようになる。  

ただアロー関数式の省略記法は開発者の中でも意見が分かれているため注意が必要。  
最近リリースされたコードフォーマッタのPrettierでは、  
引数を一つでもかっこを省略しない設定がデフォルトになったため、かっこを省略しない記法が主流になりつつある。

## 無名関数
関数を定義する際に名前を与えない関数のことを*無名関数*という。  

名前を与える関数の場合では、以下のようにnameプロパティを参照すると関数の名前を確認できる。
```js
> function foo() {}
undefined
> foo.name
'foo'

> const fn = function bar() {};
undefined
> fn.name
'bar'
```

JavaScriptは無名関数をサポートしており、関数名を記述せずに定義できる。  
なのでnameプロパティにアクセスしても何も空の文字列が返ってくる。  
```js
> (function () {}).name
''
> (() => {}).name
''
```

無名関数は名前がないのでメモリに残らず、変数に代入されなければ定義した瞬間から存在が消える。

無名関数を変数に入れることで、メモリに残るし、変数の名前が関数の名前となる。
```js
> const foo = function () {console.log('hello')};
undefined
> foo()
hello
undefined
> foo();
hello
undefined
> foo.name
'foo'
>
> const bar = () => {console.log('hello')};
undefined
> bar()
hello
undefined
> bar.name
'bar'
```

つまり関数式による関数の定義は、無名関数を定義して変数に代入しているのと変わらない。  

他にも関数式とfunctionを利用した定義方法では、メソッドとして定義した場合については異なることがあるための注意が必要。

## 引数の表現方法
ES2015からアロー関数式と一緒に追加された、関数の引数を記述する際に用いるテクニックが２つ。

- デフォルト引数
  - 関数定義の際に引数に値を入れることで、引数が指定されなかったときの値を決める
- レストパラメータ
  - 

### デフォルト引数
デフォルト引数は、関数定義の際に引数に値を入れることで、関数呼び出し時に引数が指定されなかった場合の値を指定することができる。

```js
const foo = (n, m = 3) => n ** m;

console.log(foo(2, 2))    // 4
console.log(foo(2))       // 8
```

１回目の呼び出しはmが上書きされているため`2^2 = 4`、２回目の呼び出しはは`m`が省略されているためデフォルト引数の`3`が適用され`2^3 = 8`となる。

関数の設計として重要な引数ほど前に持ってくるべきで、デフォルト引数は重要性の低い引数なはずなので、デフォルト引数は後ろの引数から設定っするのが良い。 


### レストパラメータ
rest parametersの和訳である「残余引数」と呼ばれる場合もある。  
レスとパラメータは、最後の引数の名前に`...`をつけることで残りの引数を配列として受け取ることができる。  

```js
const showNames = (a, b, ...rest) => {
  console.log(a);
  console.log(b);
  console.log(rest);
}

showNames('A', 'B', 'C', 'D', 'E')
// A
// B
// [ 'C', 'D', 'E' ]
```

名前から第２引数以降のみ使用できそうなものだが、第一引数としてレスとパラメータを指定可能。引数すべてを配列として取得できる。  
```js
const foo = (...args) {
  console.log(args)
}

foo('A', 'B', 'C', 'D', 'E')
// [ 'A', 'B', 'C', 'D', 'E' ]
```

またレストパラメータの各要素に名前をつけて取得することも可能。しかし定義している数以上の引数が渡されたときは、溢れた引数は捨てられるので注意が必要。
```js
const sum = (i, ...[j, k, l]) => i + j + k + l;

console.log(sum(1,2,3,4));      // 10
console.log(sum(1,1,1,1,1,1));  // 4
```


# クラス
JavaScriptにおけるクラス定義は以下のように記述する。

```js
class Bird {
  // プライベートフィールド
  #className = '鳥類';

  // コンストラクタ
  constructor(name) {
    this.name = name
  }

  // メソッド
  cry = (sound) => {
    console.log(`${this.name}が「$(sound)」と鳴きました`);
  };

  introduce = () => {
    console.log(`私は$(this.#className)の$(this.name)です。`);
  };

  static explain = () => {
    console.log('これは鳥のクラスです');
  };
}

class FlyableBird extends Bird {
  // コンストラクタ
  constructor(name) {
    super(name);
  }

  // メソッド
  fly = () => {
    console.log(`$(this.name)が飛びました`);
  };
}

Bird.explain();     // これは鳥のクラスです

const penguin = new Bird('ペンギン');
penguin.introduce();  // 私は鳥類のペンギンです

const hawk = new FlyableBird('タカ');
hawk.cry('ピィィー');     //タカがピィィーと鳴きました
hawk.fly();           // タカが飛びました
```

サンプルの通り、クラスの使い方は他のプログラミング言語と似ている。しかしJavaScriptには厳密な意味でのクラスは存在せず、これらはあたかもクラスを作っているかのように見えるシンタックスシュガーである。  

実際にBirdクラスを`typeof`で調べてみると以下の通り。
```js
> .load bird-class.js
> typeof Bird
'function'
```

この`function`はコンストラクタ関数を指すもの。  
コンストラクタ関数は、プロトタイプオブジェクトを継承してオブジェクトインスタンスを生成するための独立した関数のこと。

何をいっているかわからない場合は、オブジェクト指向言語であっても、Javaのような「クラスベース」ではなく、「プロトタイプベース」であることを理解する必要がある。

## プロトタイプベース
JavaScript はオブジェクト指向の言語であるが、他のオブジェクト指向の言語、例えば Ruby などのクラスベースのオブジェクト指向の言語とは違いプロトタイプベースとなっている。  
クラスベースのオブジェクト指向は抽象クラスから具象オブジェクトを生成するのに対して、プロトタイプベースでは抽象クラスが存在せず実態のあるオブジェクトがオブジェクトを直接継承する。その、継承元になったオブジェクトをプロトタイプと表現する。

プロトタイプは他のプログラミング言語における継承を司る仕組みで、以下のような動きをする。
- すべてのオブジェクトは`prototype`というプロパティを持ち、値としてなんらかのオブジェクト（もしくはnull）を参照する
- この`prototype`プロパティで参照する、オブジェクトのことをプロトタイプという
- あるオブジェクトが自身が保持しないプロパティを命令された場合、プロトタイプを参照する
- もしプロトタイプが命令されたプロパティが持つ場合、その値を返却する
- プロトタイプが命令されたプロパティを持たず、プロトタイプがプロトタイプを持つ場合は探しに行く
- 参照しているプロトタイプがnullになるまで行う


オブジェクト型はプロトタイプとして、何らかのオブジェクトを継承する。  

オブジェクトがどんなプロトタイプを持つかは`Object.getPrototypeOf(オブジェクト)`で調べる。  
`オブジェクト.__proto__`の調べ方はは非推奨になっているため注意。
```js
console.log(Object.getPrototypeOf("hello"));    // ""
console.log(Object.getPrototypeOf([1,2,3]));    // []
console.log(Object.getPrototypeOf(123));        // 0
console.log(Object.getPrototypeOf({foo: 'bar'}));   // {}
```

```js
> Object.getPrototypeOf([1,2,3])
Object(0) []
> Object.getPrototypeOf('JavaScript')
{}
> 'JavaScript'.__proto__
{}
> (65536).__proto__
{}
> Object.getPrototypeOf(65536)
```

```js
> const getProto = Object.getPrototypeOf
```

Arrayの継承
```js
> getProto(Array)
{}
> getProto(getProto(Array))
[Object: null prototype] {}
> getProto(getProto(getProto(Array)))
null
```

Functionの継承
```js
> getProto(Function)
{}
> getProto(getProto(Function))
[Object: null prototype] {}
> getProto(getProto(getProto(Function)))
null
```

Objectの継承
```js
> getProto(Object)
{}
> getProto(getProto(Object))
[Object: null prototype] {}
> getProto(getProto(getProto(Object)))
null
```

Object.prototypeには、継承元になるための実装が入っている。  
Object.getPrototypeOf(オブジェクト型)とすることで、指定したオブジェクト型のプロトタイプが確認できる。

## コンストラクタ関数
JavaScriptでオブジェクト型のインスタンスを作成する場合、

## クラスのシンタックスシュガーを使わない記法
```js
function Bird(name) {
  const className = "鳥類"
  this.name = name

  this.cry = function(sound) {
    console.log(`私は$(className)の$(this.name)です`);
  }

  return this;
}

Bird.example = function () {
  console.log(`これは鳥のクラスです`);
}

function FlyableBird(name) {
  Bird.call(this.name);
  this.fly = function() {
    console.log(`$(this.name)が飛びました`);
  };

  return this;
}

FryableBird.prototype = Object.create(Bird.prototype);

const flyableBirdInstance = new FlyableBird('スズメ');
flyableBirdInstance.cry();
flyableBirdInstance.fly();
```

# オブジェクトの表現
ES2015から便利な記法が多くある。でも初見は呪文にしか見えないので注意。  

ルールは以下の通り
- 文字列としてキーを指定する場合はクォート省略可能
- キーの文字列に変数や式の結果を梅とhコッ無場合は`[]`で囲む
- プロパティのショートハンドが使える
  - オブジェクトに値を指定すると、変数名がキー、値がプロパティ値になること 
- 配列は分割代入が可能

```js
const foo ='dummy';
const bar = 'key';
const baz = 1024;

const obj1 = {
    foo: 4,
    'foo': 8,
    '<fuu>': 16,
    [bar]: 128,
    {`_${bar}2`}; 256,
    baz: baz / 2, 
};

console.log(obj1)   // { foo: 8, <fuu>: 16, key: 128, _key2: 256, baz: 512 }

const obj2 = { baz };
console.log(obj2)   // { baz: 1024 }
```

## 分割代入
配列であれば位置、オブジェクトであればキーに対応した変数を用意することで、位置・キーに対応した値を変数に代入してくれる仕組み。  
対応した変数が用意されていない場合は値は捨てられる。  


```js
const [a, b] = ['foo', 'bar'];
console.log(a, b);       // foo bar

const [, n] = [1, 4];
console.log(n);      // 4

const [, , i, , j, , , k] = [1, 2, 3, 4, 5, 6, 7,];
console.log(i, j, k);   // 3 5 undefined

const profile = { name: 'Kanae', age: 24, gender: 'f' };
const {name, age} = profile;
console.log(name, age);     // Kanae 24
```

また{キー: 変数名}と記述することで変数名を指定して分割代入も可能。
```js
const profile = { name: 'Kanae', age: 24, gender: 'f' };
const {name: namae, age} = profile;
console.log(namae, age);     // Kanae 24
```

他のプログラミング言語ではあまり使われないが、JavaScriptではスタイルガイドで矯正されるくらい利用が推奨される。   
例えばこんなものはだめ`const name = profile.name`。分割代入を利用する。

もしオブジェクトの分割代入の際にキーが存在しない場合のデフォルト値を指定することも可能。
以下の例では`data`を`users`変数として受け取るように記述し、もし`reponse`オブジェクトに`data`が存在しない場合は`users`はから配列となる。
```js
const response = {
    data: [
        {
            id: 1,
            name: 'Patty Rabbit',
            email: 'pretty@maple.town',
        },
        {
            id: 2,
            name: 'Rolley Cocker',
            email: 'rolley@palm.town',
        },
        {
            id: 3,
            name: 'Bobby Bear',
            email: 'bobby@maple.town',
        },
    ]
};

const { data: users = [] } = response;
console.log(users);

// [{
//   email: "pretty@maple.town",
//   id: 1,
//   name: "Patty Rabbit"
// }, {
//   email: "rolley@palm.town",
//   id: 2,
//   name: "Rolley Cocker"
// }, {
//   email: "bobby@maple.town",
//   id: 3,
//   name: "Bobby Bear"
// }]
```

## スプレッド構文
レストパラメータ同様、定義されたコレクションは、`...コレクション`とすることで中身を展開することができる。これをスプレッド構文という。  
配列のスプレッド構文はES2015から、オブジェクトはES2018から使えるようになった。

```js
const arr1 = ['a', 'b', 'c'];
const arr2 = [...arr1, 'd', 'e'];
const arr3 = ['Y', 'Z', ...arr2];

console.log(arr2);      // ["a", "b", "c", "d", "e"]
console.log(arr3);      // ["Y", "Z", "a", "b", "c", "d", "e"]

const obj1 = { a: 1, b: 2, c: 3, d: 4 };
const obj2 = {...obj1, d: 99, e: 5 };
console.log(obj2);      // { a: 1, b: 2, c: 3, d: 99, e: 5 }
```

分割代入とスプレッド構文を組み合わせることでこのような抽出も可能。

```js
const user = {
    id: 1,
    name: 'Pretty Rabbit',
    email: 'patty@maple.town',
    age: 8,
}

const { id, ...userWithoutId } = user;
console.log(id, userWithoutId);

// 1, {
//   age: 8,
//   email: "patty@maple.town",
//   name: "Pretty Rabbit"
// }
```

# オブジェクトのマージとコピー
「オブジェクト型の値は別の変数に代入しただけだと、参照渡しになって実態は共有されたまま」というのは他のプログラミング言語と同様。  

そのためオブジェクトのコピーには別の方法が必要。
よくあるのは以下の通り。
`Object.assign()`は第一引数のオブジェクトに、第2引数以降のオブジェクトの各プロパティを追加・上書きするメソッド。で最後の結果にある通り、`original`オブジェクトの中身が`assigned`と同様にものになってしまっている。  

```js
const original = { a: 1, b: 2, c: 3};

const copy = Object.assign({}, original);
console.log(copy);
console.log(copy === original);     // false → 値はおなじでもアドレスを共有しない別オブジェクト

const assigned = Object.assign(original, { c: 10, d: 50 }, { d: 100 });
console.log(assigned);          // {a: 1,b: 2,c: 10,d: 100}
console.log(original);          // {a: 1,b: 2,c: 10,d: 100}
```

そのため`Object.assign()`はあまり使用されない。

最近ではスプレッド構文を利用する。  
これによってコピー先のオブジェクトのプロパティを追加してもオリジナルに影響を及ぼさない。

```js
const original = { a: 1, b: 2, c: 3 };

const copy = { ...original };
console.log(copy)
console.log(original === copy);     // false

const assigned = { ...original, ...{ c: 10, d: 50 }, e: 100 }
console.log(original);      // { a: 1,b: 2,c: 3 }
console.log(assigned);      // { a: 1,b: 2,c: 10,d: 50,e: 100 }
```

しかし注意が必要なのはこれらのコピーはシャローコピーであり、プロパティの値が配列やオブジェクトだった場合は、それらの値までコピーしてくれるものではない。

以下のプログラムの場合は、`patty`の`address`配下のオブジェクトはシャローコピーなので、`rolley`の`address`は`patty`の`address`のアドレスを参照しているため、`rolley`配下のオブジェクトの変更でも、`patty`の`address`も変更されてしまう。

```js
const patty = {
    name: 'Patty Rabbit',
    email: 'patty@maple.town',
    address: { town: 'Maple Town' }
};

const rolley = { ...patty, name: 'Rolley Cocker' };
rolley.email = 'rolley@palm.town';
rolley.address.town = 'Palm Town';

console.log(patty);

// {
//   address: {
//     town: "Palm Town"
//   },
//   email: "patty@maple.town",
//   name: "Patty Rabbit"
// }
```

オブジェクトをまるごとコピー（ディープコピー）したいなら、すべての階層を再帰的に値をコピーする必要がある。  
オブジェクトのディープコピーは、一度JSONにパースして変更することになる。  

```js
const patty = {
    name: 'Patty Rabbit',
    email: 'patty@maple.town',
    address: { town: 'Maple Town' }
};

const rolley = JSON.parse(JSON.stringify(patty));
rolley.name = 'Rolley Cocker';
rolley.email = 'rolley@palm.town';
rolley.address.town = 'Palm Town';

console.log(patty);

// {
//   address: {
//     town: "Maple Town"
//   },
//   email: "patty@maple.town",
//   name: "Patty Rabbit"
// }
```

これは強引なやり方なので弊害も存在し、プロパティに`Date`オブジェクトや関数、`undefined`などが存在する場合はうまく動かない。  
なのでその場合は、`Lodash`というユーティリティライブラリの`cloneDeep()`を利用する。

# 式と演算子での省略構文
最近のJavaScriptは関数型プログラミングのパラダイムにも対応してきており、それがReact開発にも活用されている。  

関数型プログラミング言語は、先行する式の評価を後続の式に適用し、それを繋げていった最終的な評価値に到る式のツリーを記述することでプログラムを組み立てていくもの。  
そのため関数型プログラミング言語における式は、ほとんどの場合、値を演算するか関数を呼び出すか、またはそれの組み合わせでしか無い。

つまり、React開発では文によって手続きを書き連ねるスタイルではなく、式を羅列したスタイルが好まれている。  
そのためには演算子を利用した式の記法を理解しておくことが重要である。

## ショートサーキット(Short-Circuit Evaluation)
「短絡評価」とも呼ばれる。  

論理演算子（`&&`, `||`, `!`など）が左から右に評価され、左辺の評価を行った場合に評価が確定した場合には右辺の評価を行わないことを利用する仕組み。

これによって文ではなく、式によって条件分岐を行う事ができる。

AND演算子は左辺がtruthyな値であれば、右項を返す。左辺がfalsyな値であれば左辺を返す。  
このとき後者では、左辺がfalsyな場合は右辺を評価しない（短絡評価）  

逆にOR演算子は左辺がfalsyであれば右辺を返す。左辺がtruthyな値であれば左辺を返す。
この場合も後者は右辺を評価しない（短絡評価）  

```js
const hello = undefined || null || 0 || NaN || '' || 'Hello';
console.log(hello);

const chao = ' ' && 100 && [] && {} && 'Chao!';
console.log(chao);


true && console.log('1. ', hello);      // 1. Hello
// falseがかえるので何も出力されない
false && console.log('2, ', hello);     

// trueがかえるので出力されない
true || console.log('3. ', chao);
false || console.log('4. ', chao);      // 4. Chao!
```


## Nullish CoalescingとOptional Chaining
ヌリッシュコアレシングとオプショナルチェーン、ES2020に導入された比較的新しい短絡評価の文法。  
TypeScriptでは2019年11月にリリースされた3.7系以降で使えるようになった。

### Nullish Coalescingとは
日本語でNull合体演算子で、`||`の短絡評価のかわりに`??`を利用することで、`null`と`undefined`に限定させたもの。

`||`による短絡評価の場合は、以下のようにfalsyな値の場合に右辺がかえる。
```js
console.log('' || 'Hello');     // Hello
console.log(0 || 'Hello');      // Hello
console.log(NaN || 'Hello');      // Hello
console.log(false || 'Hello');      // Hello
console.log(null || 'Hello');      // Hello
console.log(undefined || 'Hello');      // Hello
```

が`0`や`''`など、そぐわない場合が多いため、より厳格な評価をするためにES2020に導入されたものがNullish Coalescingである。  

Nullish Coalescingの場合、使い方は短絡評価と同様であるが、左項が`null`もしくは`undefined`のときのみ右項がかえる。
```js
console.log('' ?? 'Hello');     // 
console.log(0 ?? 'Hello');      // 0
console.log(NaN ?? 'Hello');      // NaN
console.log(false ?? 'Hello');      // false
console.log(null ?? 'Hello');      // Hello
console.log(undefined ?? 'Hello');      // Hello
```

### Optional Chaining
Optional Chainingは、  
n階層のオブジェクトにアクセスする際、各キーの末尾に`?`をつけることによって、存在しないプロパティの場合は短絡して結果を返す機能。

通常オブジェクトにアクセスする場合は`.`もしくは`[]`を利用する。  
このとき以下のように、指定したキーのプロパティが存在しない場合は、1階層目であれば`undefined`を返す。そのうえで`undefined`のプロパティにアクセスしようとすると例外となる。
```js
const obj = {}
console.log(obj.foo)    // undefined
console.log(obj.foo.bar)    // Uncaught TypeError: Cannot read properties of undefined (reading 'bar')
```

そのような場合に`?`を利用することで、途中のプロパティが存在しない場合は、そこで式が短絡されて`undefined`を返してくれる様になる。
```js
const = {}
console.log(obj.foo?.bar?)  // undefined
```

またチェーンはプロパティだけではなく、`foo?.bar()?.baz()`のようにメソッドでも利用可能。

例えばオブジェクトのプロパティを参照する場合も、存在しないキーを参照してしまうとundefienedになったり、nullが返ってきてしまったりするが、NullshCoalescingを利用するとその場合の値を記述したり、条件分岐がしやすくなったりする。  
`||`よりも厳格に  

```js
const users = [
    {
        name: 'Ptty Rabbit',
        address: {
            town: 'Maple Town',
        },
    },
    {
        name: 'Rolley Cocker',
        address: {}
    },
    null,
]

for (const u of users) {
    const user = u ?? {name: '(Somebody)' };
    const town = user?.address?.town ?? '(Somewhere)';
    console.log(`${user.name} lives in ${town}`);
}

// "Ptty Rabbit lives in Maple Town"
// "Rolley Cocker lives in (Somewhere)"
// "(Somebody) lives in (Somewhere)"
```

# JavaScriptにおけるthis
JavaScriptにおける`this`は独特で、  
原理を理解せずに使っていると、一見問題なさそうな以下のプログラムもエラーとなってしまう。
```js
class Person {
    constructor(name) {
        this.name = name;
    }

    greet() {
        function diIt() {
            console.log(`Hi, I'm ${this.name}`)
        }
        doIt();
    }
}

const kato = new Person('Jin');
kato.greet();       // TypeError: Cannot read property 'name' of undefined
```

なので`this`の扱いを理解してゆく

## 原因
これの原因は`this`の扱い方が一般的なプログラミング言語と異なるからである。

Javaなどのプログラミング言語におけるthisはクラス内でインスタンスを扱うための存在であることに対し、  
JavaScriptでは**ありとあらゆるスコープでいきなり参照できるような文法で、実行コンテキストであるオブジェクトへの参照**を表す。

そのためJavaなどの言語ではクラス内でのみ`this`を利用するが、
JavaScriptでは、以下のようなプログラムでも`this`を使用する事ができる。  
```js
function test() {
    console.log(this)
}
```

このときの`this`にどのようなオブジェクトが入るかは、`test()`がどこで実行されるか、`.`が使われているかによって異なる。  

大まかには以下の通り
- 関数として呼び出される場合
- メソッドとして呼び出される場合

### 関数として呼び出される場合
JavaScriptにおけるトップレベルの実行環境は必ず何らかのグローバルオブジェクトになっている。  
処理系によって異なるが、`Node.js`の場合は`global`オブジェクト、ブラウザの場合は`Window`オブジェクト。  

そのためREPLで上記の`test()`を実行すると`global`オブジェクトを参照していることになるため、以下のような結果となる
```js
> test()
<ref *1> Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  structuredClone: [Function: structuredClone],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  atob: [Function: atob],
  btoa: [Function: btoa],
  performance: Performance {
    nodeTiming: PerformanceNodeTiming {
      name: 'node',
      entryType: 'node',
      startTime: 0,
      duration: 866.9683639999712,
      nodeStart: 26.50836499995785,
      v8Start: 60.982105000002775,
      bootstrapComplete: 148.5300489999936,
      environment: 109.34810699999798,
      loopStart: 190.5946479999693,
      loopExit: -1,
      idleTime: 616.155561
    },
    timeOrigin: 1691211071112.674
  },
  fetch: [AsyncFunction: fetch]
}
```

一方で`test()`を別のオブジェクトに結びつけてみる
```js
function test() {
    console.log(this)
}

const obj = {
    test: test
}
```

このときに`obj.test()`を呼び出すと、`.`が使われており実行コンテキストが`obj`となるので、結果は以下のようになる
```js
> obj.test()
{ test: [Function: test] }
```

`obj`を`console.log`で出力しても分かる通り、`this` = `obj`になったことがわかる。
```js
> console.log(obj)
{ test: [Function: test] }
```

逆に`test()`をオブジェクトを結びつけていても、`.`を使っていない場合は呼び出し元はグローバルなので、`this`は`global`を参照することになる  
```js
function test() {
    console.log(this)
}
const obj = {
    test: test
}

const glob = obj.test
```

```js
> glob()
<ref *1> Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  structuredClone: [Function: structuredClone],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  atob: [Function: atob],
  btoa: [Function: btoa],
  performance: Performance {
    nodeTiming: PerformanceNodeTiming {
      name: 'node',
      entryType: 'node',
      startTime: 0,
      duration: 35503.88622000022,
      nodeStart: 29.7027179999277,
      v8Start: 52.53814599988982,
      bootstrapComplete: 151.2990770000033,
      environment: 105.78859200002626,
      loopStart: 205.61696800030768,
      loopExit: -1,
      idleTime: 34908.522145
    },
    timeOrigin: 1691214232405.03
  },
  fetch: [AsyncFunction: fetch]
}
```

## 任意のthisを設定する
基本的に`this`は、`.`前のオブジェクトが`this`であると認識されるが、任意の`this`を設定する方法がある。  

それには`Function`オブジェクトの`call()`および`bind()`というプロトタイプメソッドを利用する。

### call()
`call()`メソッドは、`.`前につけるオブジェクトを指定することができる。  

以下の`test.call(obj)`は`obj.test()`と同義の結果となり、`test()`を`obj`のメソッドとして登録せずとも`this`を`obj`として認識できるようになる。
```js
function test() {
    console.log(this)
}

const obj = {}
```

```js
> test.call(obj)
{}
```

### bind()
`bind()`メソッドは、関数をもとに新しい関数を作成するためのメソッドで、  
それとともに`this`になるべきオブジェクトを設定することができる。

```js
function test() {
    console.log(this)
}

const obj = {}

const bindedNewFunction = test.bind(obj)
```

```js
> bindedNewFunction()
{}
```

## thisは使用例4パターン

1. メソッド呼び出し
2. 関数呼び出し（非Strictモード）
3. 関数呼び出し（Strictモード）
4. コンストラクタ呼び出し

### 1. メソッド呼び出し
最初にも説明した、`.`の前のオブジェクトが`this`として渡されるパターン
```js
const foo = {
    name: `foo`,
    dump() {
        console.log(this)
    }
}
```

```js
> foo.dump();
{ name: 'foo', dump: [Function: dump] }
```

### 2. ES3での関数呼び出し（非Strictモード）
上でも紹介した、`.`を使用せず関数として呼び出すパターン。  

メソッドではなく、関数として呼び出す場合にはグローバルとして呼び出されるので、実行コンテキストはグロ-バルオブジェクトとなる。

```js
function foo() {
    console.log(this)
}
```

```js
> foo()
<ref *1> Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  structuredClone: [Function: structuredClone],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  atob: [Function: atob],
  btoa: [Function: btoa],
  performance: Performance {
    nodeTiming: PerformanceNodeTiming {
      name: 'node',
      entryType: 'node',
      startTime: 0,
      duration: 4531.06009699963,
      nodeStart: 4.86290100030601,
      v8Start: 18.518626000732183,
      bootstrapComplete: 53.712284000590444,
      environment: 31.98577400110662,
      loopStart: 79.12375999987125,
      loopExit: -1,
      idleTime: 4388.420136
    },
    timeOrigin: 1691220023628.992
  },
  fetch: [AsyncFunction: fetch],
  foo: [Function: foo]
}
```

また分かりづらいがオブジェクト内での関数呼び出しも、実行コンテキストはグローバルとなる。
```js
var myObject = {
  value: 1,
  show: function() {
    console.log(this);

    function show() {
      console.log(this); 
    }
    show();
  }
};
myObject.show();
```

実行結果
```js
{ value: 1, show: [Function: show] }
<ref *1> Object [global] {
  global: [Circular *1],
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  structuredClone: [Function: structuredClone],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  atob: [Function: atob],
  btoa: [Function: btoa],
  performance: Performance {
    nodeTiming: PerformanceNodeTiming {
      name: 'node',
      entryType: 'node',
      startTime: 0,
      duration: 274939.51298400015,
      nodeStart: 4.698131000623107,
      v8Start: 20.551317000761628,
      bootstrapComplete: 54.09365000016987,
      environment: 35.30920100025833,
      loopStart: 83.06021999940276,
      loopExit: -1,
      idleTime: 274478.525961
    },
    timeOrigin: 1691220734062.003
  },
  fetch: [AsyncFunction: fetch],
  myObject: { value: 1, show: [Function: show] }
}
```

### 3. 関数呼び出し(Strictモード)
2番目の関数呼び出しのようにES3では、メソッド内で関数呼び出しを行った場合の`this`はグローバルオブジェクトとなる。  

これはコンストラクター関数を`new`を使用せずに呼び出してしまった場合に、`this`がグローバルオブジェクトとなり、意図せずグローバル変数を作成してしまう恐れがある。  
以下のように
```js
function Dog(name) {
    this.name = name
}

inu = Dog('hachi')

// inuがnameというプロパティを持つはずが、グローバル変数としてnameが定義されている
console.log(name)   // hachi
```

これはよろしくない動きということで、ES5から追加されたString（厳密）モード（古い仕様に含まれる安全ではない構文や機能を禁止するためのモード）では、関数内で呼び出された`this`は`undefined`が入るようになった。

Strictモードはファイルの先頭行や関数の最初に`use strict`と記述することで有効になる。
```js
function Dog(name) {
    'use strict';
    this.name = name
}
```

```js
> inu = Dog('hachi')
Uncaught TypeError: Cannot set properties of undefined (setting 'name')
    at Dog (REPL4:3:15)
>
> console.log(name)
Uncaught ReferenceError: name is not defined
```

クラス構文は更にそれ以降のES2015によって追加された構文のため、自動的にこのstrictモードが有効になっており、そのコンストラクタも`new`演算子を使用しないと実行できないようになっている。


# モジュールシステム
現代のプログラミング言語にはモジュールシステムが当然のようにあるが、JavaScriptの場合はそうではない。  

というのもJaaScriptnには元々モジュールシステムが存在しなかった。  
しかしモジュールシステムは確実に必要な機能で、サードパーティライブラリによって保管されたり、公式でサポートしたりといろんな歴史のなかで、複数の書き方が存在する。

- ESM(ES Module)
- CJS(CommonJS Module)

ESMの場合は`import`を利用し、CJSの場合は`require`を利用する  
やっかいなのはどちらもやりたいことは同じなのに、**サーバーサイド環境（Node.js）とブラウザ環境とで、どちらを利用するべきか天下統一されていない**こと。

主には以下の通りに使い分ける（現在はサーバーサイド環境でもESMを利用するように移行中）
| サーバーサイド環境 | ブラウザ環境 |
| ------------------ | ------------ |
| CJS                | ESM          |

## CJS(CommonJS Module)
Node.jsの誕生とともに、策定されたサーバーサイド用のモジュールシステム。  

同期的にモジュールをロードするため、サーバーサイドを実行環境とする場合はこちらを採用する場合が多い。

### 書き方
`require`によって別のモジュールを使用する。  

モジュール側1 `moon.js`
```js
const moon = {
    modifier: 'prism',
    transform() {
        console.log(`Moon ${this.modifier} power, make up!`);
    },
}

module.exports = moon;
```

モジュール側2 `venus.js`
```js
exports.transform = function() {
    console.log('Venus power, make up!')
};

const finish = function(){
    console.log('Crescent beam!')
}
```

呼び出し側
```js
const moon = require('./moon');
moon.transform();       // Moon prism power, make up!

const { transform, finish } = require('./venus.js');
transform();        // Vunes power, make up!
finish();           // Crescent baem!
```


## ESModule(ECMA Script Module)
ES2015で追加された公式のモジュールシステム。  
公式によるものなので単に「JavaScriptモジュール」と呼ぶ場合もある。

ESModuleでのモジュールのロードは非同期で行われ、モジュール内も自動的にストリクトモードが適応される。

ウェブブラウザがサポートしており、Firefoxのみ2018年で遅めのサポートとなったが、現在のモダンブラウザは基本的に実装している。

Node v8.0からNode環境でもESModuleを基本的に使うことができるようになり、ESModuleが用いられることが多くなったが、古いライブラリではESModuleに対応していない場合があるので注意が必要。

### ブラウザでの書き方
`import`と`export`によって表現する。

以下のように、`script`タグの`type`属性を`module`にすることでスクリプトがモジュールであることを宣言し、モジュールシステムを利用することができる。

```html
<!DOCTYPE html>
<html lang="en">
    <body>
        <ul id="list"></ul>
        <script type="module">
import uniq from 'https://dev.jspm.io/lodash-es/uniq.js';

const arr = [12, 2, 2, 2, 9, 5, 12, 2, 15, 8, 9, 8];
const elems = uniq(arr)
    .map(n => `<li>${n}</li>`)
    .join('');
document.getElementById('list').innerHTML = elems;
        </script>
    </body>
<html>
```

### Node環境での書き方
Node環境では基本的にCommonJSが用いられることが想定されているため、`.js`もしくは`.cjs`拡張子のファイルはCommonJSであると認識される。  
そのためNode環境でESModuleを利用する場合は`.mjs`拡張子のファイルを作成することでESModuleで扱われる。  

最近ではNode環境でもESModuleを利用する場合が増えて来たため、`package.json`に以下を追加することで、`.js`拡張子のファイルでもESModuleとして利用することができる。
```json
"type": "module"
```
このオプションにより、ディレクトリ配下に置かれた`.js`もしくは`.mjs`のJavaScriptファイルがESModuleとしてロードされるようになる。


### export/importのやり方
`export`は、外部のモジュールから変数や関数、クラスなどを使えるようにするためのもの。

やり方は2つ
- 名前付きエクスポート
- デフォルトエクスポート

#### 名前付きエクスポート
名前付きエクスポートは、変数・関数・クラスの定義の前につけることもできるし、一度宣言したものをエクスポートすることができる。  

後者の場合は`{}`が必要。
```js:module_sample.js
const ONE = 1;
const TWO = 2;

export { ONE, TWO };
export const TEN = 10;
```

名前付きエクスポートしたものは、import側で`{}`を使って識別子を指定する。
```js
import {ONE, TWO, TEN} from './module_sample.js';
```

import時に`as`を利用して異なる名前でインポートすることも可能。  
```js
import {ONE as foo, TWO as bar, TEN} from './module_sample.js';

console.log(foo, bar, TEN);    // 1 2 10
```

`as`はexport側でも利用可能。
```js

const ONE = 1;
const TWO = 2;

export { ONE as foo, TWO as bar};
export const TEN = 10;
```

import側
```js
import {foo, bar} from './module_sample.js';
```

#### デフォルトエクスポート
デフォルトエクスポートは名前付きエクスポートとは異なり、1モジュールに一つしかエクスポートできない特殊記法。

デフォルトエクスポートには`export default`を利用するが、モジュールごとに一つしか存在できない。

```js
// user.js
export default class User {
  constructor(name) {
    this.name = name
  }
}
```

デフォルトエクスポートのインポートには波括弧を利用しない。またインポートする対象に名前をつけてあげる必要がある。
```js
import Foo from './user.js';

new Foo('taro');
```

デフォルトエクスポートはインポート側で名前をつける必要があるため、エクスポート側では名前を持たない場合がある。
```js
export default class {
  constructor(name) {
    this.name = name
  }
}
```

```js
export default function() {
  console.log("Hello");
}
```

```js
export default ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
```

#### モジュールの集約
複数のモジュールをエクスポートする場合に、一つのファイルに集約してエクスポートすることも可能で、一つのエントリポイントに集約することができる。
```js
// module1.js
export const foo = "hello";

export const baz = "hogehoge";
```

```js
//module2.js
const bar = () => {
  console.log("hogehoge")
}

export {bar};
```

```js
// module3.js
export default class {
  constructor(name) {
    this.name = name
  }
}
```

集約してエクスポートする
```js
// index.js
export * from './module1.js';
export { bar } from './module2.js';
export { default as User } from './module3.js';
```

モジュールの集約は循環依存（moduleAがmoduleBに依存しかつ、moduleBがmoduleAに依存しているような状態）を回避することも可能。

# 関数型プログラミングの嬉しみ

## そもそも関数型プログラミング言語と言うのは？
関数型プログラミング言語は、参照透過的な関数を組み合わせることで解決するべき問題に対処していくプログラミング言語のこと。  

重要なのは、「関数」というと文脈的にプログラミング言語で用いる関数だと思ってしまうが、ここでの文脈は数学的な関数の意味合いが強いということ。  

`y = f(x)`的な。  

数学で言う関数がプログラミング言語の関数と何が違うというと、数学の関数は`x`が決まれば、`y`の値も常に一定であること。そういった同じ入力に対して同じ作用で同じ出力が保証されていることを**参照透過性**という。  

つまり関数型プログラミング言語は、Inが決まれば結果が必ずOutが一定である関数を組み合わせてプログラミングするスタイル。

## 他には何プログラミング言語があるのか？
大きく2つに大別される
- 命令形プログラミング(Imperative Programing)
- 宣言型プログラミング(Declarative Programing)

命令形プログラミングは、最終的な出力を得るために状態を変化させる連続しためイレブンに寄って記述されるプログラミングスタイルのことを指す。  

命令型に分類される代表的なものが「**手続き型プログラミング**（Procedural Programming)」。  
手続きというのは一連の命令文の組み合わせをまとめた、サブルーチンや関数と呼ばれるもの。グローバル変数としてまとめたデータを、複数の手続きを経て最終結果を得るプログラミング言語のこと。  
COBOLやBASIC、C言語といった古めの言語が該当する。最近のものではGoがこれに該当する。

## JavaとかPythonとかは何言語？
JavaやPythonは、オブジェクト指向プログラミングに分類される。  

厳密にはオブジェクト指向プログラミングは、命令形か宣言型かという分類からは外れたパラダイム。  
基本的に構造化されたデータとそのふるまいをカプセル化した者同士を相互に作用させて最終結果を得るスタイルのこと。  

現代では最もメジャーなパラダイムで、Java, C++, C#, Python, Rubyなどが該当する。

## 宣言型プログラミング
宣言型プログラミングは、命令型のように出力を得る方法ではなく、出力の性質・あるべき状態を宣言することでプログラムを構成する。  

代表的なものでSQLが該当する。  
SQLは「どのようにデータベースにアクセスしてデータを取得する」という手続きではなく、「どんなデータがほしいか」を宣言することで出力を得る。  

関数型プログラミングもこのような方式でプログラムをくむ。

## コードにすると？
例として1から100までの範囲における8の倍数を格納した配列を作る処理を手続き型と関数型で記述してみる。
```js
// 手続き型
{
  const list = [];

  for (let n = 1; n < 101; n += 1) {
    if (n % 8 === 0) {
      list.push(n);
    }
  }
  
  console.log(list)
}

// 関数型
{
  const range = (start, end) => [...Array(end - start)].map((_, n) => n + start);
  const list = range(1, 101).filter((n) => n % 8 === 0);
  console.log(list);
}
```

### 違いその１
手続き型は可変性(mutability)、関数型は不変性(Immutability)であること。  

関数型の良いところは、手続き型では`list`に12回の破壊的変更、nに100回の再代入が行われる一方で、関数型では再代入や破壊的変更が行われていないということ。  

for文は提携処理とはいえ繰り返しのたびに再代入が行われるし、ループブロックの中でも任意に上書き可能である。ミュータブルな変数に依存する処理が多ければ多いほど、プログラムの予測可能性が低下してバグの発生可能性が高くなる。一方で、関数型のようにイミュータブルな変数で構成すればバグの可能性を低くできる。  

ミュータブルなリソースがプログラムの中に存在していれば、それだけ副作用を生みやすい。  
一方で、イミュータビリティ、不変性を守ることでプログラムから副作用が排除される。  

さらに関数型プログラミングでは、代入自体もできるだけ控える傾向にある。最初から代入しなければ壊れることもない。メソッドチェーンや演算子で式を繋いでいき、関数もその場限りのものならできるだけ無名関数のまま使う。


### 違いそその2
手続き型は文を多用する。一方で関数型では式を組み合わせてプログラムを構築している。  

手続き型はfor文やif文を利用しているように、文という値を返さないサブルーチンの中で計算が実行されている。一方で関数型はすべてが値を返す式の組み合わせで、それが左辺から右辺へ評価されていき最終的な値が得られる。

そのおかげでコードがシンプルになる。またはじめは意味不明かもしれないが、`range`式が返す値で一区切り、`filter`式が返す値で一区切りと、それぞれの式が独立していて相互に鑑賞しないので、一度に考えることも少なくて済む。

### 違いその3
手続き型は、はじめに配列が空で、要素が一つづつ追加されていって完成に至っているように、ボトムアップ的に積み上げて言って最終成果物を完成させている。  

一方で関数型は、はじめに1から100までの整数配列を作成した上で、そこから8で割り切れるものだけを抜き出すという手続き型とは逆のアプローチをとっている。

## JavaScriptは関数型プログラミング言語なのか？
関数型プログラミングはプログラムのアプローチなので、JavaScriptを手続き型で記述することもできるし、関数型で記述することも可能。  


それには2つの言語仕様の条件がある。
- 第一級関数をサポート
- 文ではなく式が主役

そのため、最近の言語ではJavaやRubyでも関数型プログラミングっぽい事ができるが、第一級ジャン数をサポートしていないため、関数型ではない。  

またPythonやJavaScriptは第一級関数をサポートしているが、式が主役では無い。  
というのもガチの関数型プログラミング言語は`if`すら値を返す式であり、式が主役であることから、PythonやJavaScriptは第一級関数をサポートしていても関数型プログラミング言語とは言われていないのはこのため。

## 関数型のより説得力のある参考するべき文献
「プログラマが知るべき97のこと」の冒頭2番めに「関数型プログラミングを学ぶことの重要性。

いかに参照透過性が重要か、不具合を減らしデバッグを簡単にするためにどういうコードを書くべきかと言うのが語られている。

# 関数型入門その１（コレクションの反復）

## 配列
関数プログラミングの入り口はコレクションの反復処理にあり。  

Arrayオブジェクトのプロトタイプメソッドの中から、反復処理を行う主なものを紹介。

- map
  - 対象の配列の要素に対して関数の処理を実行し、新しい配列を返す
- filter
  - 与えた条件に適合する要素を抽出した新しい配列を返す
- find
  - 与えた条件に適合した最初の要素を返す。見つからなかった場合は`undefined`を返す。
- findIndex
  - findと同様の動きするかわりに適合した要素のインデックス番号を返す。見つからなかった場合は`-1`を返す。
- every
  - すべての要素が与えた条件を満たすか否かを返す
- some
  - 与えた条件を満たす要素があるかを真偽値で返す

```js
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];

console.log(
  arr.map((n) => n * 2),          // [2, 4, 6, 8, 10, 12, 14, 16, 18]
  arr.filter((n) => n % 3 === 0), // [3, 6, 9]
  arr.find((n) => n > 4),         // 5
  arr.findIndex((n) => n > 4),    // 4
  arr.every((n) => n !== 0),      // true
  arr.some((n) => n >= 10),       // false
)
```

### reduceとsort
```js
const arr = [1,2,3,4,5];

console.log(
	arr.reduce((n, m) => n + m),          // 15
  arr.sort((n, m) => n > m ? -1 : 1),   // [5, 4, 3, 2, 1]
);
```

`reduce`と`sort`は少しむずかしいが、コールバックの引数として2つ以上の引数をとる。  


`reduce`は、引数に設定した再帰的にコールバックを実行し、最終的に単一の値を得るためのメソッド。  
わかりやすい例としては「配列の全要素の合計を取得する」  

コールバックの第二引数`m`に配列の要素、第一引数`n`に前回の関数の結果が入る。一回目の実行には前回の結果が無いため`n`には配列の要素が格納される。

```js
const arr = [1,2,3,4,5]

const sum = arr.reduce((n, m) => {
  console.log(n, m);
  return n + m;
});

console.log(sum);
```

実行結果
```
1, 2
3, 3
6, 4
10, 5
15
```

また`reduce`メソッドの第二引数には`initialValue`を設定することができ、設定した場合には初回コールバックの第一引数を指定することができる。

```js
const arr = [1,2,3,4,5]

const sumWithInitial = arr.reduce((n, m) => {
  console.log(n, m);
  return n + m;
}, 0);
```

実行結果
```
0, 1
1, 2
3, 3
6, 4
10, 5
15
```

`sort`は、隣通しの要素を比較し、並び替えるためのメソッド。メソッドは比較関数というコールバックを受け取る。  

比較関数のルール
- 第一引数を第2引数より前に並べる場合は、0未満の数値を返す。
- 第一引数を第二引数よりあとに並べる場合は、0より大きい数値を返す。
- 第一引数と第二引数が同一の場合は、0を返す。

このルールさえ満たす関数を作れば良いので、数値の大小を並び替えるだけでなく、より複雑なソートも可能。

注意が必要なのは`sort`は破壊的メソッドであること  
破壊的メソッドとは、オブジェクトを新たに生成するのではなく対象となるオブジェクトを変更するメソッドのこと。
```js
const arr = [4,6,1,2]

arr.sort((n, m) => n > m ? 1 : -1);
console.log(arr)      // [1, 2, 4, 6]
```
JavaScriptでは、JavaやPythonと同様に破壊的メソッドを丸暗記するしか無いので注意。  

破壊的メソッドとして他にも
- pop()
- unshift()
- pop()
- shift()
- reverse()

などがある。

## 破壊的メソッドを破壊せずに使用するためのTips
```js
const list = [4, 8, 2, 6];

const sortedList = list.slice().sort((n, m) => n < m ? -1 : 1);
console.log(sortedList);

const reverselist = [...list].reverse();
console.log(reverseList, list);
```

また`push()`や`unshift()`を使用するかわりに`[0, ...list, 99]`のように記述することもできる。

### includes
「指定した値の要素が一つでも含まれているか」を真偽値で返す事ができる。これは条件式の活用できるので便利。
```js
const arr = [1,2,3,4,5]

console.log(arr.includes(5));   // true
console.log(arr.includes(8));   // false
```


### 繰り返し処理
JavaScriptで任意の回数だけ繰り返し処理を記述する方法。  

Arrayオブジェクトの`key()`メソッドを利用することで、イテレータという反復処理のオブジェクトを取得することができる。
これを配列に展開することで要素のインデックスが取り出すことができるので、`range`っぽい動きを実現できる。
```js
> [...Array(3)]
[ undefined, undefined, undefined ]
> [...Array(3)].map((_, n) => { console.log(`${n + 1} times`); }); 
1 times
2 times
3 times
> [...Array(3).keys()] [0,1,2]
> [...Array(3).keys()].map((n) => { console.log(`${n + 1} times`); }); 
1 times
2 times
3 times
```

## オブジェクトの反復処理
オブジェクトの反復処理のための構文として、`for...in`が使われている事が多いが、これはミュータブル変数が使用されることからあまり推奨されていない。

```js
const user = {
  id: 3,
  name: "kato jin",
  greet: () => console.log("hello"),
}

for (let key in user) {
  console.log(key, user[key]);
}
// "id", 3
// "name", "kato jin"
// "greet", () => console.log("hello")
```

そのためオブジェクトの反復処理には`Object`自身が持つメソッドを利用し、一端配列を形成するスタイルがAirbnbのガイドでは推奨されている。

具体的には、オブジェクトのキー配列を取得する場合は`Object.keys()`, オブジェクトのキーバリュー配列を取得する場合は`Object.entries()`を利用する。これらはES2017で追加された新しめのメソッド。

```js
const user = {
  id: 3,
  name: "kato jin",
  greet: () => console.log("hello"),
}

console.log(Object.keys(user));
// ["id", "name", "greet"]

console.log(Object.entries(user));
// [["id", 3], ["name", "kato jin"], ["greet", () => console.log("hello")]]

// 反復処理
Object.keys(user).map((k) => {console.log(k, user[k]);});

Object.entries(user).map(([k, v]) => {console.log(k, v); });
```

# 関数型プログラミングの要素
関数型プログラミングを理解するにあたって重要なプログラムが以下のようなもの。  
`map`に何を渡しているのか？
```js
const double = (n) => n * 2;
[1, 2, 3].map(double);
```

ここでは、`map`メソッドの引数として`double`という関数を渡している。  
関数に対して別の関数を渡すということは他のプログラミング言語ではあまり行われていない。  
これはJavaScriptが第一級関数（関数を変数に入れられること）をサポートしているから。  

引数を無名関数にて指定する場合も同様に関数を関数を渡している。  
```js
[1, 2, 3].map((n) => n * 2);
```


関数型プログラミングの定義は一言で定められるようなものではないが、関数型プログラミングのパラダイムでは、主に以下のことが行われる。
- 名前を持たないその場限りの関数（無名関数）を定義できる
- 変数やデータ構造の値として関数を設定できる（第一級関数をサポート）
- 関数に引数として関数を渡したり、その戻り値に関数を設定できる（高階関数）
- 関数に特定の引数を固定した新しい関数を作ることができる（部分適用）
- 複数の高階関数を合成してひとつの関数にできる（関数合成）

## 高階関数
高階関数とは（Higher Order Function）とは、引数に関数をとったり、戻り値として関数を返したりする関数のこと  

さっきのコードや以下のサンプルのように、関数を引数として受け取ったり、関数を戻り値として返すことができる。  
```js
const greeter = (target) => {
  const sayHello = () => {
    console.log(`Hi ${target}!`);
  };

  return sayHello;
}

const greet = greeter('Jin Kato');
greet();    // Hi Jin Kato!
```

ここでは`sayHello`関数を戻り値として返す`greeter`関数を定義しており、呼び出し側は`greeter`関数の戻り値を`greet`に格納している。  
`greet`には`sayHello`関数が格納されるので、`greet`を関数として呼び出すことができるようになる。

`return sayHello()`としてしまうと`sayHello()`が関数の呼び出しとなり、結果の`undefined`が返ることになるので注意が必要。  

### より良い書き方
関数型プログラミングでは不要な代入はできるだけ避けるようにするべきなので、代入をできるだけ省略すると以下のようなコードになる
```js
const greeter = (target) => {
  return () => {
    console.log(`Hi ${target}!`);
  };
}
```

さらにアロー関数の処理内容が`return`文のみの場合は、`{}`を省略することができるので最終的にはこうなる。
```js
const greeter = (target) => console.log(`Hi ${target}!`);
```

### Reactにも活用される
高階関数の応用として、Reactでも高階関数がよく使用される。  
例えばコンポーネントを引数にとって、コンポーネントを戻り値として返す「高階コンポーネント（HOC: Higher Order Component）」という概念。  

しかし原理は高階関数と同様なので、しっかり理解しておくことが重要。

## カリー（curry）化と部分適用

カリー化とは、~~あまり食材`うどん`に`カレー`を入れて`カレーうどん`に変化させ、食べ盛りのお子さんもうれしい主婦の味方アレンジレシピのことである~~

これは冗談で料理のカレーとは関係なく、Haskellという激むず関数型言語を開発したHaskell Curryという学者さんの名前を指す。
しかし「元の何かを異なる何かに変化させる」という観点では似たようなもので、今回は食材ではなく関数を変化させる。  

カリー化とは、「複数の引数をとる関数を、より少ない引数をとる関数に分割して入れ子にすること」である。
```js
// カリー化前
{
  const multiply = (n, m) => n * m;
  console.log(multiply(2, 4));        // 8
}

// カリー化
{
  const withMultiple = (n) => {
    return (m) => n * m;
  };
  console.log(withMultiple(2)(4));    // 8
}

// カリー化（アロー関数式版）
{
  const withMultiple = (n) => (m) => n * m;
  console.log(withMultiple(2)(4));    // 8
```

`multiply`関数は引数`n`と`m`を取り、その積を返すだけの関数だが、カリー化をした`withMultiply`関数は「`m`を引数にとり`n`との積を返す関数」を返す関数となる。  

### 部分適用
関数をカリー化することによって、部分適用が可能となる。
```js
const withMultiple = (n) => (m) => n * m;
console.log(withMultiple(3)(5));

const triple = withMultiple(3);
console.log(triple(5));
```

ここでは`withMultiple`に`3`を渡してできた関数を`triple`に格納することで、引数を3倍する関数を作成することができる。このような形でカリー化した関数の一部の引数を固定して新しい関数を作ることを「関数の部分適用」という。

## クロージャ
クロージャ（Closure)とは、日本語では関数閉包と呼ばれ、関数を関数で閉じて包むことをいう。  

閉じていない状態のプログラムの例。

```js
let COUNT = 0;

const increment = () => {
  return COUNT += 1;
};
```

単純なカウンターだが、グローバル変数`COUNT`に依存しているのがあまりよろしくない。  
なのでこれを丸ごと関数の中に入れてしまう。
```js
const counter = () => {
  let count = 0;

  const increment = () => {
    return count += 1;
  };
};
```

これだとカウンターを起動できないので、カウンターを外から使えるようにする。  
これで`count`は関数外からアクセスできないプライベート変数であり、かつメソッドにて`count`のインクリメントが行えるようになる。
```js
const counter = () => {
  let count = 0;

  const countUp = () => {
    return count += 1;
  };

  return countUp;
};

const increment = counter();
console.log(increment());     // 1
console.log(increment());     // 2
```

実用性から最終的にはこのようなコードになる。
```js
const counter = (count = 0) => (adds = 1) => count += adds;

const increment = counter();
console.log(increment());     // 1
console.log(increment());     // 2
console.log(increment(2));    // 4
```

やっていることは実質クラスと同じだが、クラスより簡潔に扱える。  
実質状態を持つことになるが、関数型プログラミングでも状態を持つことをゼロにするのは難しいので、その場合に使用する。

# 非同期処理と例外処理
現在のJSでは非同期処理への理解が不可欠である。  
というのもJSはブラウザのための言語であり、ブラウザはボタンクリックや画面遷移などイベント駆動の処理が非同期処理と相性が良いことや、非同期処理を前提とするAjaxなどの登場によることから非同期処理が多く用いられる。

理解のために重要なのは以下の通り
- Promise
- async/await

## 非同期処理の難しいところ
非同期処理の難しいところは、**非同期処理は処理が完了していなくても、次の処理を実行する**ので順序を担保する必要がある点。  

プログラミング言語は基本的に上から順に実行されていきます。  
この通り。
```js
console.log(1);
console.log(2);
console.log(3);
```

実行結果
```
1
2
3
```

上記のような同期処理では、処理が次の処理は実行されないので`1,2,3`と順に実行されるのですが、  
以下のようにもし非同期処理が入った場合は、非同期処理を実行してその処理完了を待たずに次の処理を実行してしまうので結果が異なります。  
```js
console.log(1);
// 1s後に2を出力する非同期処理
setTimeout(()=>console.log(2), 1000);
console.log(3);
```

実行結果
```
1
3
2
```

もし`console.log(3);`の実行を`setTimeout(()=>console.log(2), 1000);`の結果のあとに実行したい場合にはどのようにすれば良いのか？

## ゴリ押しコールバック
JSでは非同期処理が重要であるにも関わらず`Promise`が導入されるまでは、この非同期処理の順番を担保するための方法が用意されていなかった。  

そのため苦肉の策として非同期処理を行う関数にコールバック(関数の引数にわたす関数のこと）を渡し、メインの処理が終わったら次にコールバックを実行するような設計をすることで順番を担保していた。  
これの代表例の一つが、ファイルを開く`fs`モジュールの`readfile`関数。  

複数のファイル(file1.txt, file2.txt, file3.txt, file4.txt, file5.txt)の読み込みが完了したあとに、そのデータに対して処理を行う場合はこのようなプログラムになる。
```js
const fs = require('fs');

fs.readFile('file1.txt', (err, file1) => {
    if (err) throw err;
    fs.readFile('file2.txt', (err, file2) => {
        if (err) throw err;
        fs.readFile('file3.txt', (err, file3) => {
            if (err) throw err;
            fs.readFile('file4.txt', (err, file4) => {
                if (err) throw err;
                fs.readFile('file5.txt', (err, file5) => {
                    if (err) throw err;
                    console.log(file1 + file2 + file3 + file4 + file5);
                });
            });
        });
    });
});
```

これであれば`file1.txt` -> `file2.txt` -> `fil3.txt`... と全てのファイルを順に開いたあとに`console.log`を行うことができるのだが、まあネストし過ぎで可読性・保守性最悪。  
この状態はコールバック地獄（Callback Hell）と呼ばれた。  

昔ばなし終了

## Promise
この非同期処理の順序問題を解決するために生まれた`Promise`。  
`Promise`はES2015から導入されたJavaScirptの標準組み込みオブジェクトで、非同期処理の最終的な処理結果の値を「約束」するもの。  

この`Promise`を使うことによって、順序を結果を担保する。  
具体的には、実行したい非同期処理を`Promise`オブジェクトのインスタンス化する際のコールバックとする。   
```js
const asyncPromise = () => {
  return new Promise((resolve, reject) => {
    // 非同期処理を書く
    // 非同期処理が成功した場合はresolve
    // 失敗した場合はrejectを呼ぶ
  })
}
```

はじめの1,2,3を正しく表示するとする場合は
```js
const asyncSetTimeout = (waitTime) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(2);
      // 非同期処理が終わったことを伝える
      resolve();
    }, waitTime)
  })
}

console.log(1);

// 1s後に2を出力する非同期処理
asyncSetTimeout(1000)
.then(() => {
  console.log(3);
})
```

実行結果
```
1
2
3
```

重要なのは以下の4つ
- Promiseのインスタンス化時のコールバック内で非同期処理を記述する
- コールバックの引数として`resolve`と`reject`を受け取る（上記ではrejectを省略）
- 非同期処理が正しく終わった場合は、`resolve(結果)`、正しく終わらなかった場合は`reject(結果もしくはエラー)`を利用することで非同期処理の結果を伝える
- `Promise.then()`は必ず`resolve`もしくは`reject`が実行されたあとに処理される（順番を担保できる）

今回の例では非同期で実行される`setTimeout`関数をPromiseでラップしている。  
`setTimeout`内の`console.log(2)`が終了したら`resolve`を実行することで非同期処理が終わったことを伝える。その後`.then()`処理内で`console.log(3)`を実行することで正しく`1,2,3`と順番に出力されている。


ファイルを開くケースも`Promise`を使ってみる
```js
const fs = require('fs')

const asyncReadFile = (file) => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, (err, data) => {
      if (err) reject(err);
      resolve(data);
    })
  })
}

asyncReadFile('file1.txt')
  .then(file1 => {
      return readFilePromise('file2.txt').then(file2 => file1 + file2);
  })
  .then(result => {
      return readFilePromise('file3.txt').then(file3 => result + file3);
  })
  .then(result => {
      return readFilePromise('file4.txt').then(file4 => result + file4);
  })
  .then(result => {
      return readFilePromise('file5.txt').then(file5 => result + file5);
  })
  .then(finalResult => {
      console.log(finalResult);
  })
  .catch(err => {
      throw err;
  });
```

ここでのポイントは2つ
- `resolve()`で渡した引数は`.then()`のコールバック関数の第一引数に渡される。`reject()`は`.catch`に渡される
- `.then()`, `.catch()`はすべて戻り値としてPromiseオブジェクトを返す

今回も先程同様、`fs.readFile()`をPromiseオブジェクトを返すようにラップしている。  
`fs.readFile()`のコールバック関数で`err`があるときは`reject(err)`、`err`がない場合は取得したファイル内容`data`を`resolve(data)`とすることで`.then()`に結果を渡している。  
また`.then()`の戻り値はすべて自動的にPromiseオブジェクトとして返されるので、「ファイル取得 -> 結果を連結」の結果を次の`.then()`に渡すことで、最終的にすべてのファイルを連結し出力している。  


これによって非同期処理が複数ある場合でもかなり読みやすくなった事がわかる。

PromiseなしVer
```js
const fs = require('fs');

fs.readFile('file1.txt', (err, file1) => {
    if (err) throw err;
    fs.readFile('file2.txt', (err, file2) => {
        if (err) throw err;
        fs.readFile('file3.txt', (err, file3) => {
            if (err) throw err;
            fs.readFile('file4.txt', (err, file4) => {
                if (err) throw err;
                fs.readFile('file5.txt', (err, file5) => {
                    if (err) throw err;
                    console.log(file1 + file2 + file3 + file4 + file5);
                });
            });
        });
    });
});

PromiseありVer
```js
const fs = require('fs')

const asyncReadFile = (file) => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, (err, data) => {
      if (err) reject(err);
      resolve(data);
    })
  })
}

asyncReadFile('file1.txt')
  .then(file1 => {
      return readFilePromise('file2.txt').then(file2 => file1 + file2);
  })
  .then(result => {
      return readFilePromise('file3.txt').then(file3 => result + file3);
  })
  .then(result => {
      return readFilePromise('file4.txt').then(file4 => result + file4);
  })
  .then(result => {
      return readFilePromise('file5.txt').then(file5 => result + file5);
  })
  .then(finalResult => {
      console.log(finalResult);
  })
  .catch(err => {
      throw err;
  });
```

### Promiseの課題
しかし、Promiseにもいくつか課題があって
- 処理が少し異なっただけでPromiseを返す関数を記述する必要がある
- Promiseの`.then()`をまたぐ前提で関数を作る必要がある
などなどが

そんな折、ES2017で誕生したのが`async/await`。これらを使うことによってより簡潔に非同期処理を記述することができる！

## async/await
読み方はアシンクではなく、エイシンク・アウェイとらしい。   

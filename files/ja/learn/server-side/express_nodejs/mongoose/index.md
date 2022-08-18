---
title: 'Express チュートリアル Part 3: データベースの使用 (Mongoose を使用)'
slug: Learn/Server-side/Express_Nodejs/mongoose
translation_of: Learn/Server-side/Express_Nodejs/mongoose
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/skeleton_website", "Learn/Server-side/Express_Nodejs/routes", "Learn/Server-side/Express_Nodejs")}}</div>

<p class="summary">この記事ではデータベースと、それらを Node/Express アプリケーションで使用する方法について簡単に紹介します。続いて、<a href="https://mongoosejs.com/">Mongoose</a> を使用して<a href="/ja/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">地域図書館</a> Web サイトへのデータベースアクセスを提供する方法を説明します。 オブジェクトスキーマとモデルの宣言方法、主なフィールドタイプ、および基本的な検証について説明します。また、モデルデータにアクセスするための主な方法についても簡単に説明します。</p>

<table class="learn-box standard-table">
 <tbody>
  <tr>
   <th scope="row">前提条件:</th>
   <td><a href="/ja/docs/Learn/Server-side/Express_Nodejs/skeleton_website">Express チュートリアル Part 2: スケルトン Web サイトの作成 </a></td>
  </tr>
  <tr>
   <th scope="row">目標:</th>
   <td>Mongoose を使用して独自のモデルを設計および作成できるようになる。</td>
  </tr>
 </tbody>
</table>

<h2 id="概要">概要</h2>

<p>図書館職員は本と借り手についての情報を保存するためにローカルライブラリ Web サイトを使いますが、図書館員は本をブラウズして検索し、利用可能なコピーがあるかどうかを調べ、そしてそれらを予約または借りるために使います。情報を効率的に保存および取得するために、データベースに保存します。</p>

<p>Express アプリケーションはさまざまなデータベースを使用できます。作成、読み取り、更新、削除 (CRUD) 操作を実行するために使用できるいくつかのアプローチがあります。 このチュートリアルではいくつかの利用可能なオプションの簡単な概要を説明し、次に選択された特定のメカニズムを詳細に表示します。</p>

<h3 id="どのデータベースを使用できますか？">どのデータベースを使用できますか？</h3>

<p>Express アプリは Node でサポートされている任意のデータベースを使用できます (Express 自体はデータベース管理のための特定の追加の動作や要件を定義していません)。 PostgreSQL、MySQL、Redis、SQLite、MongoDB など、<a href="https://expressjs.com/en/guide/database-integration.html">多くの一般的なオプション</a>があります。</p>

<p>データベースを選択するときは、生産性/学習時間の曲線、パフォーマンス、複製/バックアップの容易さ、コスト、コミュニティサポートなどのことを考慮する必要があります。「最高の」データベースは1つもありませんが、ほとんどの一般的なソリューションは、ローカルライブラリのような中小規模のサイトでは十分条件を満たしているはずです。</p>

<p>オプションの詳細については、<a href="https://expressjs.com/ja/guide/database-integration.html">データベース統合</a> (Express ドキュメント) を参照してください。</p>

<h3 id="データベースを利用するための最良の方法は何ですか？">データベースを利用するための最良の方法は何ですか？</h3>

<p>データベースにインタラクティブにアプローチするには2つの方法があります。</p>

<ul>
 <li>データベースのネイティブクエリ言語 (例：SQL)を使用する</li>
 <li>オブジェクトデータモデル ("ODM")／オブジェクトリレーショナルモデル ("ORM") を使用する。ODM/ORM は Web サイトのデータを JavaScript オブジェクトとして表し、それが基になるデータベースにマッピングされます。一部の ORM は特定のデータベースに関連付けられていますが、他のデータベースはデータベースに依存しないバックエンドを提供しています</li>
</ul>

<p>SQL、またはデータベースでサポートされているクエリ言語を使用すると、最高のパフォーマンスが得られます。ODM は、変換コードを使用してオブジェクトとデータベース形式の間のマッピングを行うため、処理が遅くなることが多く、最も効率的なデータベースクエリが使用されない可能性があります (これは、ODM がさまざまなデータベースバックエンドをサポートしている場合に特に当てはまります。サポートされているデータベース機能に関して、さらに妥協する必要があります)。</p>

<p>ORM を使用する利点は、プログラマがデータベースのセマンティクスではなく JavaScript オブジェクトの観点から考え続けることができることです。これは、同じデータベースまたは異なる Web サイトで異なるデータベースを扱う必要がある場合に特に当てはまります。またデータの検証とチェックを実行するための明らかな場所を提供します。</p>

<div class="note">
<p><strong>Tip:</strong>  ODM/ORM を使用すると、多くの場合、開発と保守のコストが削減されます。ネイティブのクエリ言語に精通しているかパフォーマンスが最優先であるのでなければ、ODM の使用を積極的に検討するべきです。</p>
</div>

<h3 id="どの_ORMODM_を使うべきですか？">どの ORM/ODM を使うべきですか？</h3>

<p>NPM パッケージマネージャのサイトには、多数の ODM/ORM ソリューションがあります (サブセットの <a href="https://www.npmjs.com/browse/keyword/odm">odm</a> タグおよび <a href="https://www.npmjs.com/browse/keyword/orm">orm</a> タグを調べてください)。</p>

<p>執筆時点で一般的だったいくつかの解決策は、次のとおりです。</p>

<ul>
 <li><a href="https://www.npmjs.com/package/mongoose">Mongoose</a>: Mongoose は、非同期環境で動作するように設計された <a href="https://www.mongodb.org/">MongoDB</a> オブジェクトモデリングツールです</li>
 <li><a href="https://www.npmjs.com/package/waterline">Waterline</a>: Express ベースの <a href="http://sailsjs.com/">Sails</a> Web フレームワークから抽出された ORM。Redis、MySQL、LDAP、MongoDB、Postgres など、さまざまなデータベースにアクセスするための統一された API を提供します</li>
 <li><a href="https://www.npmjs.com/package/bookshelf">Bookshelf</a>: Promise ベースおよび従来の callback インターフェイスの両方を備え、トランザクションのサポート、熱心な/入れ子になったリレーションの読み込み、多態的な関連付け、および1対1、1対多、および多対多のリレーションのサポートを提供します。PostgreSQL、MySQL、および SQLite3 で動作します</li>
 <li><a href="https://www.npmjs.com/package/objection">Objection</a>: SQL とその基盤となるデータベースエンジン (SQLite 3、Postgres、および MySQL をサポート) の全機能を使用することを可能な限り簡単にします</li>
 <li><a href="https://www.npmjs.com/package/sequelize">Sequelize</a> は Node.js と io.js のための Promise ベースの ORM です。PostgreSQL、MySQL、MariaDB、SQLite、および MSSQL のダイアレクトをサポートし、堅実なトランザクションサポート、リレーション、リードレプリケーションなどを備えています</li>
 <li><a href="https://node-orm.readthedocs.io/en/latest/">Node ORM2</a> は NodeJS のオブジェクトリレーションマネージャです。MySQL、SQLite、Progres をサポートし、オブジェクト指向のアプローチを使用してデータベースを操作するのを助けます</li>
 <li>
  <p><a href="http://1602.github.io/jugglingdb/" rel="nofollow">JugglingDB</a> は NodeJS 用のクロスDB ORM で、最も一般的なデータベースフォーマットにアクセスするための共通インターフェイスを提供します。現在 MySQL、SQLite3、Postgres、MongoDB、Redis および js-memory-storage をサポートしています (テスト用の自己記述エンジンのみ)</p>
 </li>
</ul>

<p>原則として、解決策を選択する際には、提供されている機能と "コミュニティ活動" (ダウンロード、コントリビュート、バグレポート、ドキュメントの品質など) の両方を考慮する必要があります。この記事を書いている時点では、Mongoose は最も人気のある ODM であり、データベースに MongoDB を使用している場合は妥当な選択です。</p>

<h3 id="ローカルライブラリに_Mongoose_と_MongoDB_を使用する">ローカルライブラリに Mongoose と MongoDB を使用する</h3>

<p>ローカルライブラリの例 (およびこのトピックの残りの部分) では、<a href="https://www.npmjs.com/package/mongoose">Mongoose ODM</a> を使用してライブラリデータにアクセスします。Mongoose は、ドキュメント指向のデータモデルを使用するオープンソースの <a href="https://en.wikipedia.org/wiki/NoSQL">NoSQL</a> データベースである <a href="https://www.mongodb.com/what-is-mongodb">MongoDB</a> のフロントエンドとして機能します。MongoDB データベースの "ドキュメント" の "コレクション" は、リレーショナルデータベースの "行" の "テーブル" <a href="https://docs.mongodb.com/manual/core/databases-and-collections/#collections">に似ています</a>。</p>

<p>この ODM とデータベースの組み合わせは、Node コミュニティで非常に人気があります。これは、ドキュメントの保存とクエリのシステムが JSON に非常に似ているため、JavaScript 開発者にはよく知られているためです。</p>

<div class="note">
<p><strong>Tip:</strong> Mongoose を使用するために MongoDB を知っている必要はありませんが、<a href="http://mongoosejs.com/docs/guide.html">Mongoose のドキュメント</a>の一部は、MongoDB に慣れている方が使いやすく理解しやすいものです。</p>
</div>

<p>このチュートリアルの残りの部分では、<a href="/ja/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">ローカルライブラリ Web サイト</a>の例の Mongoose スキーマとモデルを定義してアクセスする方法を示します。</p>

<h2 id="ローカルライブラリモデルの設計">ローカルライブラリモデルの設計</h2>

<p>いきなりモデルのコーディングを始める前に、格納する必要があるデータと、さまざまなオブジェクト間の関係について検討することをお勧めします。</p>

<p>書籍に関する情報 (タイトル、概要、著者、ジャンル、ISBN) を保存する必要があること、および複数のコピーが利用可能であること (グローバルに一意の ID、利用状況など) があることを知っています。著者の名前だけではなく、著者に関するより多くの情報を格納する必要があるかもしれません。また、同じ名前または類似の名前を持つ著者が複数いる可能性があります。書籍のタイトル、著者、ジャンル、およびカテゴリに基づいて情報を並べ替えることができるようにします。</p>

<p>モデルを設計するときは、すべての "オブジェクト" (関連情報のグループ) ごとに別々のモデルを用意するのが合理的です。 この場合、明らかなオブジェクトは本、本のインスタンス、および作者です。</p>

<p>Web サイト自体に選択肢をハードコーディングするのではなく、モデルを使用して選択肢の選択肢 (たとえば選択肢のドロップダウンリストなど) を表すこともできます - すべてのオプションが事前にわかっていない場合や変更される可能性がある場合は、これをお勧めします。このタイプのモデルの明らかな候補は本のジャンルです (例：サイエンスフィクション、フランス詩など)。</p>

<p>モデルとフィールドを決めたら、それらの関係について考える必要があります。</p>

<p>そのことを念頭に置いて、以下の UML 関連図は、この場合に定義するモデルを (ボックスとして) 示しています。上記で説明したように、本のモデル (本の一般的な詳細)、本のインスタンス (システムで利用可能な本の特定の物理コピーのステータス)、および作成者のモデルを作成しました。また、値を動的に作成できるように、ジャンルのモデルを用意することにしました。<code>BookInstance:status</code> のモデルを使用しないことにしました - 許容値は変更しないと考えられるので、許容値をハードコードします。各ボックス内には、モデル名、フィールド名と型、そしてメソッドとその戻り型が表示されます。</p>

<p>この図には、モデル間の関係 (それらの多重度も含む) も示されています。多重度は、関係内に存在する可能性がある各モデルの番号 (最大および最小) を示す図上の番号です。たとえば、ボックス間の接続線は、<code>Book</code> と <code>Genre</code> が関連していることを示しています。<code>Book</code> モデルに近い数字は、ジャンルに0個以上の <font face="consolas, Liberation Mono, courier, monospace"><span style="background-color: rgba(220, 220, 220, 0.5);">Book</span></font> がある必要があることを示しており、線のもう一方の端にある<code>Genre</code> の隣の数字は、本に0個以上の関連する<code>Genre</code>があることを示しています。</p>

<div class="note">
<p><strong>メモ</strong>: 下記の <a href="#">Mongoose 入門書</a>で説明されているように、1つのモデルだけで documents/models 間の関係を定義するフィールドがあるほうがよいでしょう (他のモデルで関連する <code>_id</code> を検索することによって逆の関係を見つけることができます)。以下では、Book スキーマの Book/Genre と Book/Author の関係、および BookInstance スキーマの Book/BookInstance の関係を定義します。この選択は多少恣意的でした - 他のスキーマでも同じようにフィールドを持つことができました。</p>
</div>

<p><img alt="Mongoose Library Model  with correct cardinality" src="https://mdn.mozillademos.org/files/15645/Library%20Website%20-%20Mongoose_Express.png" style="height: 620px; width: 737px;"></p>

<div class="note">
<p><strong>メモ</strong>: 次のセクションでは、モデルの定義方法と使用方法を説明する基本的な入門書を提供します。お読みになったところで、上の図の各モデルをどのように構築するかを検討してください。</p>
</div>

<h2 id="Mongoose_入門書">Mongoose 入門書</h2>

<p>このセクションでは、Mongoose を MongoDB データベースに接続する方法、スキーマとモデルを定義する方法、そして基本的なクエリを作成する方法の概要を説明します。</p>

<div class="note">
<p><strong>メモ:</strong> この入門書は、npm の <a href="https://www.npmjs.com/package/mongoose">Mongoose クイックスタート</a>と<a href="http://mongoosejs.com/docs/guide.html">公式ドキュメント</a>に "大きく影響を受けています"。</p>
</div>

<h3 id="Mongoose_と_MongoDB_のインストール">Mongoose と MongoDB のインストール</h3>

<p>Mongoose は他の依存関係と同じようにあなたのプロジェクト (<strong>package.json</strong>) にインストールされます。つまり NPM を使用します。インストールするには、プロジェクトフォルダ内で次のコマンドを使用します。</p>

<pre class="brush: bash notranslate"><code>npm install mongoose</code>
</pre>

<p>Mongoose をインストールすると、MongoDB データベースドライバを含むすべての依存関係が追加されますが、MongoDB 自体はインストールされません。 MongoDB サーバをインストールする場合は、さまざまな OS 用の<a href="https://www.mongodb.com/download-center">インストーラをここからダウンロード</a>してローカルにインストールできます。クラウドベースの MongoDB インスタンスを使用することもできます。</p>

<div class="note">
<p><strong>メモ:</strong> このチュートリアルでは、mLab クラウドベースのDatabase as a Service <a href="https://mlab.com/plans/pricing/">サンドボックス層</a>として使用してデータベースを提供します。これは開発に適しており、オペレーティングシステムの "インストール" に依存しないため (database-as-a-service も本番データベースに使用することができる1つのアプローチです)、チュートリアルに適しています。</p>
</div>

<h3 id="MongoDB_への接続">MongoDB への接続</h3>

<p>MongooseはMongoDBデータベースへの接続を必要とします。以下のように、<code>require()</code> して <code>mongoose.connect()</code> でローカルにホストされているデータベースに接続することができます。</p>

<pre class="brush: js notranslate">//Import the mongoose module
var mongoose = require('mongoose');

//Set up default mongoose connection
var mongoDB = 'mongodb://127.0.0.1/my_database';
mongoose.connect(mongoDB);
// Get Mongoose to use the global promise library
mongoose.Promise = global.Promise;
//Get the default connection
var db = mongoose.connection;

//Bind connection to error event (to get notification of connection errors)
db.on('error', console.error.bind(console, 'MongoDB connection error:'));</pre>

<p>デフォルトの <code>Connection</code> オブジェクトは <code>mongoose.connection</code> で取得できます。接続されると、open イベントが <code>Connection</code> インスタンスで発生します。</p>

<div class="note">
<p><strong>Tip:</strong> 追加のコネクションを作成する必要がある場合は、<code>mongoose.createConnection()</code> を使用できます。 これは <code>connect()</code> と同じ形式のデータベース URI (ホスト、データベース、ポート、オプションなど) を取り、<code>Connection</code> オブジェクトを返します。</p>
</div>

<h3 id="モデルの定義と作成">モデルの定義と作成</h3>

<p>モデルは <code>Schema</code> インターフェイスを使用して定義されます。スキーマを使用すると、各ドキュメントに格納されているフィールドとその検証要件およびデフォルト値を定義できます。さらに、データ型や他のフィールドと同じように使用できるが実際にはデータベースに格納されていない仮想プロパティも扱いやすいように、静的メソッドおよびインスタンスヘルパーメソッドを定義できます。(もう少し後で説明します)。</p>

<p>その後、スキーマは <code>mongoose.model()</code> メソッドを使用してモデルに "コンパイル" されます。モデルを作成したら、それを使用して特定のタイプのオブジェクトを検索、作成、更新、および削除できます。</p>

<div class="note">
<p><strong>メモ:</strong> 各モデルは MongoDB データベース内のドキュメントのコレクションにマップされます。ドキュメントはモデル <code>Schema</code> で定義されたフィールド/スキーマタイプを含みます。</p>
</div>

<h4 id="スキーマの定義">スキーマの定義</h4>

<p>以下のコードは、単純なスキーマを定義する方法を示しています。最初に mongoose を <code>require()</code> し、次に Schema コンストラクタを使用して新しいスキーマインスタンスを作成し、コンストラクタの object パラメータで内部のさまざまなフィールドを定義します。</p>

<pre class="brush: js notranslate">//Require Mongoose
var mongoose = require('mongoose');

//Define a schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string: String,
  a_date: Date
});
</pre>

<p>上記の場合、文字列と日付の2つのフィールドしかありません。次のセクションでは、他のフィールドタイプ、検証、その他の方法について説明します。</p>

<h4 id="モデルを作成する">モデルを作成する</h4>

<p>モデルは、<code>mongoose.model()</code> メソッドを使用してスキーマから作成されます。</p>

<pre class="brush: js notranslate">// Define schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string: String,
  a_date: Date
});

<strong>// Compile model from schema
var SomeModel = mongoose.model('SomeModel', SomeModelSchema );</strong></pre>

<p>最初の引数はあなたのモデル用に作成されるコレクションの単数形の名前です (Mongoose は上記の SomeModel モデル用のデータベースコレクションを作成します)、そして2番目の引数はモデルの作成に使用したいスキーマです。</p>

<div class="note">
<p><strong>メモ:</strong> モデルクラスを定義したら、それらを使用してレコードを作成、更新、または削除し、クエリを実行してすべてのレコードまたは特定のレコードのサブセットを取得できます。これを行う方法を<a href="#">モデルの使用</a>セクションで、そしてビューを作成するときに示します。</p>
</div>

<h4 id="スキーマ型_フィールド">スキーマ型 (フィールド)</h4>

<p>スキーマには任意の数のフィールドを含めることができます。各フィールドは MongoDB に格納されているドキュメント内のフィールドを表します。一般的なフィールド型の多くとその宣言方法を示すスキーマの例を以下に示します。</p>

<pre class="brush: js notranslate">var schema = new Schema(
{
  name: <strong>String</strong>,
  binary: <strong>Buffer</strong>,
  living: <strong>Boolean</strong>,
  updated: { type: <strong>Date</strong>, default: Date.now() },
  age: { type: <strong>Number</strong>, min: 18, max: 65, required: true },
  mixed: <strong>Schema.Types.Mixed</strong>,
  _someId: <strong>Schema.Types.ObjectId</strong>,
  array: <strong>[]</strong>,
  ofString: [<strong>String</strong>], // 他の型でも配列にすることができます。
  nested: { stuff: { type: <strong>String</strong>, lowercase: true, trim: true } }
})</pre>

<p>ほとんどのスキーム型("type:"やフィールド名で記述されるもの)はその名のとおりです。例外は以下のようなものがあります:</p>

<ul>
 <li><code>ObjectId</code>: データベースのモデルを示すインスタンスです。例えば、本は著者オブジェクトを示すためにこれを使います。一つ一つにはユニークなID (<code>_id</code>) が割り当てられています。必要があれば<code>populate()</code>メソッドで関連情報を呼び出すことができます。</li>
 <li><a href="http://mongoosejs.com/docs/schematypes.html#mixed">Mixed</a>: 任意のスキーム型。</li>
 <li><font face="Consolas, Liberation Mono, Courier, monospace">[]</font>: 項目の配列。このモデルにはJavaScriptの配列オペレーション(push, pop, unshift, その他。)をオペレートすることができます。上記の例は特別な型なしに<code>String</code>オブジェクトの配列を示しています。また、他の型のオブジェクトに対しても配列で持つことはできます。</li>
</ul>

<p>このコードはフィールドを宣言する2つのやり方も示しています:</p>

<ul>
 <li>フィールドの<em>name</em> と <em>type</em>をkey-valueペアのように書く (例えば<code>name</code>, <code>binary <font face="Arial, x-locale-body, sans-serif"><span style="background-color: #ffffff;">,</span></font></code><code>living</code>のように）.</li>
 <li><code>type</code>定義するオブジェクトが続くフィールド名、およびフィールドの他のオプション。オプションには次のようなものがあります:
  <ul>
   <li>初期値.</li>
   <li>ビルドインのバリデータ (例えば最大値/最小値) それからカスタマイズしたバリデーション機能.</li>
   <li>そのヒールドが必須かどうか</li>
   <li><code>String</code> のフィールドは自動的に大文字か、小文字にするか、前後の空白を取り除く（trim）するか？ (例えば:<code>{ type: <strong>String</strong>, lowercase: true, trim: true }</code>)記載することができる）</li>
  </ul>
 </li>
</ul>

<p>もっとオプションについて見たいなら<a href="http://mongoosejs.com/docs/schematypes.html">SchemaTypes</a> (Mongoose docs)を見てみてください.</p>

<h4 id="バリデーション">バリデーション</h4>

<p>Mongooseはビルドインもしくはカスマイズしたバリデータや同期的もしくは非同期的なバリデータを提供しています。 バリデータはすべての場合において、許容範囲または値と検証失敗のエラーメッセージの両方を指定できます。</p>

<p>ビルドインのバリデータには:</p>

<ul>
 <li>すべての <a href="http://mongoosejs.com/docs/schematypes.html">SchemaTypes</a> に <a href="http://mongoosejs.com/docs/api.html#schematype_SchemaType-required">required</a> があります。 これはドキュメントを保存するために必要なフィールドを指定するために使われます。</li>
 <li><a href="http://mongoosejs.com/docs/api.html#schema-number-js">Numbers</a> に <a href="http://mongoosejs.com/docs/api.html#schema_number_SchemaNumber-min">min</a>（最小値を指定） と <a href="http://mongoosejs.com/docs/api.html#schema_number_SchemaNumber-max">max</a>（最大値を指定） バリデータがあります。</li>
 <li><a href="http://mongoosejs.com/docs/api.html#schema-string-js">Strings</a> には以下のバリデータがあります:
  <ul>
   <li><a href="http://mongoosejs.com/docs/api.html#schema_string_SchemaString-enum">enum</a>: フィールドに利用可能な値の配列を指定します。</li>
   <li><a href="http://mongoosejs.com/docs/api.html#schema_string_SchemaString-match">match</a>: マッチさせる正規表現を指定します。</li>
   <li><a href="http://mongoosejs.com/docs/api.html#schema_string_SchemaString-maxlength">maxlength</a> と <a href="http://mongoosejs.com/docs/api.html#schema_string_SchemaString-minlength">minlength</a>: 文字数の最大値と最小値を指定します。</li>
  </ul>
 </li>
</ul>

<p>以下の例（Mongooseドキュメントから少し変更しています）では、いくつかのバリデータタイプとエラーメッセージを指定する方法を示しています:</p>

<pre class="brush: js notranslate"><code>
    var breakfastSchema = new Schema({
      eggs: {
        type: Number,
        min: [6, 'Too few eggs'],
        max: 12,
        required: [true, 'Why no eggs?']
      },
      drink: {
        type: String,
        enum: ['Coffee', 'Tea', 'Water',]
      }
    });
</code></pre>

<p>詳しくは <a href="http://mongoosejs.com/docs/validation.html">Validation</a> (Mongoose docs) を見てみてください。</p>

<h4 id="Virtual_properties">Virtual properties</h4>

<p>Virtual properties are document properties that you can get and set but that do not get persisted to MongoDB. The getters are useful for formatting or combining fields, while setters are useful for de-composing a single value into multiple values for storage. The example in the documentation constructs (and deconstructs) a full name virtual property from a first and last name field, which is easier and cleaner than constructing a full name every time one is used in a template.</p>

<div class="note">
<p><strong>Note:</strong> We will use a virtual property in the library to define a unique URL for each model record using a path and the record's <code>_id</code> value.</p>
</div>

<p>For more information see <a href="http://mongoosejs.com/docs/guide.html#virtuals">Virtuals</a> (Mongoose documentation).</p>

<h4 id="Methods_and_query_helpers">Methods and query helpers</h4>

<p>A schema can also have <a href="http://mongoosejs.com/docs/guide.html#methods">instance methods</a>, <a href="http://mongoosejs.com/docs/guide.html#statics">static methods</a>, and <a href="http://mongoosejs.com/docs/guide.html#query-helpers">query helpers</a>. The instance and static methods are similar, but with the obvious difference that an instance method is associated with a particular record and has access to the current object. Query helpers allow you to extend mongoose's <a href="http://mongoosejs.com/docs/queries.html">chainable query builder API</a> (for example, allowing you to add a query "byName" in addition to the <code>find()</code>, <code>findOne()</code> and <code>findById()</code> methods).</p>

<h3 id="Using_models">Using models</h3>

<p>Once you've created a schema you can use it to create models. The model represents a collection of documents in the database that you can search, while the model's instances represent individual documents that you can save and retrieve.</p>

<p>We provide a brief overview below. For more information see: <a href="http://mongoosejs.com/docs/models.html">Models</a> (Mongoose docs).</p>

<h4 id="Creating_and_modifying_documents">Creating and modifying documents</h4>

<p>To create a record you can define an instance of the model and then call <code>save()</code>. The examples below assume SomeModel is a model (with a single field "name") that we have created from our schema.</p>

<pre class="brush: js notranslate"><code>// Create an instance of model SomeModel
var awesome_instance = new </code>SomeModel<code>({ name: 'awesome' });

// Save the new model instance, passing a callback
awesome_instance.save(function (err) {
  if (err) return handleError(err);
  // saved!
});
</code></pre>

<p>Creation of records (along with updates, deletes, and queries) are asynchronous operations — you supply a callback that is called when the operation completes. The API uses the error-first argument convention, so the first argument for the callback will always be an error value (or null). If the API returns some result, this will be provided as the second argument.</p>

<p>You can also use <code>create()</code> to define the model instance at the same time as you save it. The callback will return an error for the first argument and the newly-created model instance for the second argument.</p>

<pre class="brush: js notranslate">SomeModel<code>.create({ name: 'also_awesome' }, function (err, awesome_instance) {
  if (err) return handleError(err);
  // saved!
});</code></pre>

<p>Every model has an associated connection (this will be the default connection when you use <code>mongoose.model()</code>). You create a new connection and call <code>.model()</code> on it to create the documents on a different database.</p>

<p>You can access the fields in this new record using the dot syntax, and change the values. You have to call <code>save()</code> or <code>update()</code> to store modified values back to the database.</p>

<pre class="brush: js notranslate">// Access model field values using dot notation
console.log(<code>awesome_instance.name</code>); //should log '<code>also_awesome</code>'

// Change record by modifying the fields, then calling save().
<code>awesome_instance</code>.name="New cool name";
<code>awesome_instance.save(function (err) {
   if (err) return handleError(err); // saved!
   });</code>
</pre>

<h4 id="Searching_for_records">Searching for records</h4>

<p>You can search for records using query methods, specifying the query conditions as a JSON document. The code fragment below shows how you might find all athletes in a database that play tennis, returning just the fields for athlete <em>name</em> and <em>age</em>. Here we just specify one matching field (sport) but you can add more criteria, specify regular expression criteria, or remove the conditions altogether to return all athletes.</p>

<pre class="brush: js notranslate"><code>var Athlete = mongoose.model('Athlete', yourSchema);

// find all athletes who play tennis, selecting the 'name' and 'age' fields
Athlete.find({ 'sport': 'Tennis' }, 'name age', function (err, athletes) {
  if (err) return handleError(err);
  // 'athletes' contains the list of athletes that match the criteria.
})</code></pre>

<p>If you specify a callback, as shown above, the query will execute immediately. The callback will be invoked when the search completes.</p>

<div class="note">
<p><strong>Note:</strong> All callbacks in Mongoose use the pattern <code>callback(error, result)</code>. If an error occurs executing the query, the <code>error</code> parameter will contain an error document and <code>result</code> will be null. If the query is successful, the <code>error</code> parameter will be null, and the <code>result</code> will be populated with the results of the query.</p>
</div>

<p>If you don't specify a callback then the API will return a variable of type <a href="http://mongoosejs.com/docs/api.html#query-js">Query</a>. You can use this query object to build up your query and then execute it (with a callback) later using the <code>exec()</code> method.</p>

<pre class="brush: js notranslate"><code>// find all athletes that play tennis
var query = Athlete.find({ 'sport': 'Tennis' });

// selecting the 'name' and 'age' fields
query.select('name age');

// limit our results to 5 items
query.limit(5);

// sort by age
query.sort({ age: -1 });

// execute the query at a later time
query.exec(function (err, athletes) {
  if (err) return handleError(err);
  // athletes contains an ordered list of 5 athletes who play Tennis
})</code></pre>

<p>Above we've defined the query conditions in the <code>find()</code> method. We can also do this using a <code>where()</code> function, and we can chain all the parts of our query together using the dot operator (.) rather than adding them separately. The code fragment below is the same as our query above, with an additional condition for the age.</p>

<pre class="notranslate"><code>Athlete.
  find().
  where('sport').equals('Tennis').
  where('age').gt(17).lt(50).  //Additional where query
  limit(5).
  sort({ age: -1 }).
  select('name age').
  exec(callback); // where callback is the name of our callback function.</code></pre>

<p>The <a href="http://mongoosejs.com/docs/api.html#query_Query-find">find()</a> method gets all matching records, but often you just want to get one match. The following methods query for a single record:</p>

<ul>
 <li><code><a href="http://mongoosejs.com/docs/api.html#model_Model.findById">findById()</a></code>: Finds the document with the specified <code>id</code> (every document has a unique <code>id</code>).</li>
 <li><code><a href="http://mongoosejs.com/docs/api.html#query_Query-findOne">findOne()</a></code>: Finds a single document that matches the specified criteria.</li>
 <li><code><a href="http://mongoosejs.com/docs/api.html#model_Model.findByIdAndRemove">findByIdAndRemove()</a></code>, <code><a href="http://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate">findByIdAndUpdate()</a></code>, <code><a href="http://mongoosejs.com/docs/api.html#query_Query-findOneAndRemove">findOneAndRemove()</a></code>, <code><a href="http://mongoosejs.com/docs/api.html#query_Query-findOneAndUpdate">findOneAndUpdate()</a></code>: Finds a single document by <code>id</code> or criteria and either update or remove it. These are useful convenience functions for updating and removing records.</li>
</ul>

<div class="note">
<p><strong>Note:</strong> There is also a <code><a href="http://mongoosejs.com/docs/api.html#model_Model.count">count()</a></code> method that you can use to get the number of items that match conditions. This is useful if you want to perform a count without actually fetching the records.</p>
</div>

<p>There is a lot more you can do with queries. For more information see: <a href="http://mongoosejs.com/docs/queries.html">Queries</a> (Mongoose docs).</p>

<h4 id="Working_with_related_documents_—_population">Working with related documents — population</h4>

<p>You can create references from one document/model instance to another using the <code>ObjectId</code> schema field, or from one document to many using an array of <code>ObjectIds</code>. The field stores the id of the related model. If you need the actual content of the associated document, you can use the <code><a href="http://mongoosejs.com/docs/api.html#query_Query-populate">populate()</a></code> method in a query to replace the id with the actual data.</p>

<p>For example, the following schema defines authors and stories. Each author can have multiple stories, which we represent as an array of <code>ObjectId</code>. Each story can have a single author. The "ref" (highlighted in bold below) tells the schema which model can be assigned to this field.</p>

<pre class="brush: js notranslate"><code>var mongoose = require('mongoose')
  , Schema = mongoose.Schema

var authorSchema = Schema({
  name    : String,
  stories : [{ type: Schema.Types.ObjectId, <strong>ref</strong>: 'Story' }]
});

var storySchema = Schema({
  author : { type: Schema.Types.ObjectId, <strong>ref</strong>: 'Author' },
  title    : String
});

var Story  = mongoose.model('Story', storySchema);
var Author = mongoose.model('Author', authorSchema);</code></pre>

<p>We can save our references to the related document by assigning the <code>_id</code> value. Below we create an author, then a story, and assign the author id to our stories author field.</p>

<pre class="brush: js notranslate"><code>var bob = new Author({ name: 'Bob Smith' });

bob.save(function (err) {
  if (err) return handleError(err);

  //Bob now exists, so lets create a story
  var story = new Story({
    title: "Bob goes sledding",
    author: bob._id    // assign the _id from the our author Bob. This ID is created by default!
  });

  story.save(function (err) {
    if (err) return handleError(err);
    // Bob now has his story
  });
});</code></pre>

<p>Our story document now has an author referenced by the author document's ID. In order to get the author information in the story results we use <code>populate()</code>, as shown below.</p>

<pre class="brush: js notranslate"><code>Story
.findOne({ title: 'Bob goes sledding' })
.populate('author') //This populates the author id with actual author information!
.exec(function (err, story) {
  if (err) return handleError(err);
  console.log('The author is %s', story.author.name);
  // prints "The author is Bob Smith"
});</code></pre>

<div class="note">
<p><strong>Note:</strong> Astute readers will have noted that we added an author to our story, but we didn't do anything to add our story to our author's <code>stories</code> array. How then can we get all stories by a particular author? One way would be to add our author to the stories array, but this would result in us having two places where the information relating authors and stories needs to be maintained.</p>

<p>A better way is to get the <code>_id</code> of our <em>author</em>, then use <code>find()</code> to search for this in the author field across all stories.</p>

<pre class="brush: js notranslate"><code>Story
.find({ author : bob._id })
.exec(function (err, stories) {
  if (err) return handleError(err);
  // returns all stories that have Bob's id as their author.
});</code>
</pre>
</div>

<p>This is almost everything you need to know about working with related items<em> for this tutorial</em>. For more detailed information see <a href="http://mongoosejs.com/docs/populate.html">Population</a> (Mongoose docs).</p>

<h3 id="One_schemamodel_per_file">One schema/model per file</h3>

<p>While you can create schemas and models using any file structure you like, we highly recommend defining each model schema in its own module (file), exporting the method to create the model. This is shown below:</p>

<pre class="brush: js notranslate"><code>// File: ./models/somemodel.js

//Require Mongoose
var mongoose = require('mongoose');

//Define a schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string          : String,
  a_date            : Date,
});

<strong>//Export function to create "SomeModel" model class
module.exports = mongoose.model('SomeModel', SomeModelSchema );</strong></code></pre>

<p>You can then require and use the model immediately in other files. Below we show how you might use it to get all instances of the model.</p>

<pre class="brush: js notranslate"><code>//Create a SomeModel model just by requiring the module
var SomeModel = require('../models/somemodel')

// Use the SomeModel object (model) to find all SomeModel records
SomeModel.find(callback_function);</code></pre>

<h2 id="Setting_up_the_MongoDB_database">Setting up the MongoDB database</h2>

<p>Now that we understand something of what Mongoose can do and how we want to design our models, it's time to start work on the <em>LocalLibrary</em> website. The very first thing we want to do is set up a MongoDb database that we can use to store our library data.</p>

<p>For this tutorial, we're going to use <a href="https://mlab.com/welcome/">mLab</a>'s free cloud-hosted "<a href="https://mlab.com/plans/pricing/">sandbox</a>" database. This database tier is not considered suitable for production websites because it has no redundancy, but it is great for development and prototyping. We're using it here because it is free and easy to set up, and because mLab is a popular <em>database as a service</em> vendor that you might reasonably choose for your production database (other popular choices at the time of writing include <a href="https://www.compose.com/">Compose</a>, <a href="https://scalegrid.io/pricing.html">ScaleGrid</a> and <a href="https://www.mongodb.com/cloud/atlas">MongoDB Atlas</a>).</p>

<div class="note">
<p><strong>Note:</strong> If you prefer you can set up a MongoDb database locally by downloading and installing the <a href="https://www.mongodb.com/download-center">appropriate binaries for your system</a>. The rest of the instructions in this article would be similar, except for the database URL you would specify when connecting.</p>
</div>

<p>You will first need to <a href="https://mlab.com/signup/">create an account</a> with mLab (this is free, and just requires that you enter basic contact details and acknowledge their terms of service). </p>

<p>After logging in, you'll be taken to the <a href="https://mlab.com/home">home</a> screen:</p>

<ol>
 <li>Click <strong>Create New</strong> in the <em>MongoDB Deployments</em> section.<img alt="" src="https://mdn.mozillademos.org/files/14446/mLabCreateNewDeployment.png" style="height: 415px; width: 1000px;"></li>
 <li>This will open the <em>Cloud Provider Selection </em>screen.<br>
  <img alt="MLab - screen for new deployment" src="https://mdn.mozillademos.org/files/15661/mLab_new_deployment_form_v2.png" style="height: 931px; width: 1297px;"><br>

  <ul>
   <li>Select the SANDBOX (Free) plan from the Plan Type section. </li>
   <li>Select any provider from the <em>Cloud Provider </em>section. Different providers offer different regions (displayed below the selected plan type).</li>
   <li>Click the <strong>Continue</strong> button.</li>
  </ul>
 </li>
 <li>This will open the <em>Select Region</em> screen.
  <p><img alt="Select new region screen" src="https://mdn.mozillademos.org/files/15662/mLab_new_deployment_select_region_v2.png" style="height: 570px; width: 1293px;"></p>

  <ul>
   <li>
    <p>Select the region closest to you and then <strong>Continue</strong>.</p>
   </li>
  </ul>
 </li>
 <li>
  <p>This will open the <em>Final Details</em> screen.<br>
   <img alt="New deployment database name" src="https://mdn.mozillademos.org/files/15663/mLab_new_deployment_final_details.png" style="height: 569px; width: 1293px;"></p>

  <ul>
   <li>
    <p>Enter the name for the new database as <code>local_library</code> and then select <strong>Continue</strong>.</p>
   </li>
  </ul>
 </li>
 <li>
  <p>This will open the <em>Order Confirmation</em> screen.<br>
   <img alt="Order confirmation screen" src="https://mdn.mozillademos.org/files/15664/mLab_new_deployment_order_confirmation.png" style="height: 687px; width: 1290px;"></p>

  <ul>
   <li>
    <p>Click <strong>Submit Order</strong> to create the database.</p>
   </li>
  </ul>
 </li>
 <li>
  <p>You will be returned to the home screen. Click on the new database you just created to open its details screen. As you can see the database has no collections (data).<br>
   <img alt="mLab - Database details screen" src="https://mdn.mozillademos.org/files/15665/mLab_new_deployment_database_details.png" style="height: 700px; width: 1398px;"><br>
    <br>
   The URL that you need to use to access your database is displayed on the form above (shown for this database circled above). In order to use this you need to create a database user that you can specify in the URL.</p>
 </li>
 <li>Click the <strong>Users</strong> tab and select the <strong>Add database user</strong> button.</li>
 <li>Enter a username and password (twice), and then press <strong>Create</strong>. Do not select <em>Make read only</em>.<br>
  <img alt="" src="https://mdn.mozillademos.org/files/14454/mLab_database_users.png" style="height: 204px; width: 600px;"></li>
</ol>

<p>You have now created the database, and have an URL (with username and password) that can be used to access it. This will look something like: <code>mongodb://your_user_namer:your_password@ds119748.mlab.com:19748/local_library</code>.</p>

<h2 id="Install_Mongoose">Install Mongoose</h2>

<p>Open a command prompt and navigate to the directory where you created your <a href="/ja/docs/Learn/Server-side/Express_Nodejs/skeleton_website">skeleton Local Library website</a>. Enter the following command to install Mongoose (and its dependencies) and add it to your <strong>package.json</strong> file, unless you have already done so when reading the <a href="#Installing_Mongoose_and_MongoDB">Mongoose Primer</a> above.</p>

<pre class="brush: bash notranslate">npm install mongoose
</pre>

<h2 id="Connect_to_MongoDB">Connect to MongoDB</h2>

<p>Open <strong>/app.js</strong> (in the root of your project) and copy the following text below where you declare the <em>Express application object</em> (after the line <code>var app = express();</code>). Replace the database url string ('<em>insert_your_database_url_here</em>') with the location URL representing your own database (i.e. using the information <a href="#Setting_up_the_MongoDB_database">from mLab</a>).</p>

<pre class="brush: js notranslate">//Set up mongoose connection
var mongoose = require('mongoose');
var mongoDB = '<em>insert_your_database_url_here</em>';
mongoose.connect(mongoDB);
mongoose.Promise = global.Promise;
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'MongoDB connection error:'));</pre>

<p>As discussed <a href="#Connecting_to_MongoDB">in the Mongoose primer above</a>, this code creates the default connection to the database and binds to the error event (so that errors will be printed to the console). </p>

<h2 id="Defining_the_LocalLibrary_Schema">Defining the LocalLibrary Schema</h2>

<p>We will define a separate module for each model, as <a href="#One_schemamodel_per_file">discussed above</a>. Start by creating a folder for our models in the project root (<strong>/models</strong>) and then create separate files for each of the models:</p>

<pre class="notranslate">/express-locallibrary-tutorial  //the project root
  <strong>/models</strong>
    <strong>author.js</strong>
    <strong>book.js</strong>
    <strong>bookinstance.js</strong>
    <strong>genre.js</strong>
</pre>

<h3 id="Author_model">Author model</h3>

<p>Copy the <code>Author</code> schema code shown below and paste it into your <strong>./models/author.js</strong> file. The scheme defines an author has having <code>String</code> SchemaTypes for the first and family names, that are required and have a maximum of 100 characters, and <code>Date</code> fields for the date of birth and death.</p>

<pre class="brush: js notranslate">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var AuthorSchema = new Schema(
  {
    first_name: {type: String, required: true, max: 100},
    family_name: {type: String, required: true, max: 100},
    date_of_birth: {type: Date},
    date_of_death: {type: Date},
  }
);

<strong>// Virtual for author's full name
AuthorSchema
.virtual('name')
.get(function () {
  return this.family_name + ', ' + this.first_name;
});

// Virtual for author's lifespan
AuthorSchema
</strong>.virtual('lifespan')
.get(function () {
  return (this.date_of_death.getYear() - this.date_of_birth.getYear()).toString();
});

// Virtual for author's URL
AuthorSchema
.virtual('url')
.get(function () {
  return '/catalog/author/' + this._id;
});

//Export model
module.exports = mongoose.model('Author', AuthorSchema);

</pre>

<p>We've also declared a <a href="#Virtual_properties">virtual</a> for the AuthorSchema named "url" that returns the absolute URL required to get a particular instance of the model — we'll use the property in our templates whenever we need to get a link to a particular author.</p>

<div class="note">
<p><strong>Note:</strong> Declaring our URLs as a virtual in the schema is a good idea because then the URL for an item only ever needs to be changed in one place.<br>
 At this point, a link using this URL wouldn't work, because we haven't got any routes handling code for individual model instances. We'll set those up in a later article!</p>
</div>

<p>At the end of the module, we export the model.</p>

<h3 id="Book_model">Book model</h3>

<p>Copy the <code>Book</code> schema code shown below and paste it into your <strong>./models/book.js</strong> file. Most of this is similar to the author model — we've declared a schema with a number of string fields and a virtual for getting the URL of specific book records, and we've exported the model.</p>

<pre class="brush: js notranslate">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var BookSchema = new Schema(
  {
    title: {type: String, required: true},
  <strong>  author: {type: Schema.Types.ObjectId, ref: 'Author', required: true},</strong>
    summary: {type: String, required: true},
    isbn: {type: String, required: true},
  <strong>  genre: [{type: Schema.Types.ObjectId, ref: 'Genre'}]</strong>
  }
);

// Virtual for book's URL
BookSchema
.virtual('url')
.get(function () {
  return '/catalog/book/' + this._id;
});

//Export model
module.exports = mongoose.model('Book', BookSchema);
</pre>

<p>The main difference here is that we've created two references to other models:</p>

<ul>
 <li>author is a reference to a single <code>Author</code> model object, and is required.</li>
 <li>genre is a reference to an array of <code>Genre</code> model objects. We haven't declared this object yet!</li>
</ul>

<h3 id="BookInstance_model">BookInstance model</h3>

<p>Finally, copy the <code>BookInstance</code> schema code shown below and paste it into your <strong>./models/bookinstance.js</strong> file. The <code>BookInstance</code> represents a specific copy of a book that someone might borrow and includes information about whether the copy is available or on what date it is expected back, "imprint" or version details.</p>

<pre class="brush: js notranslate">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var BookInstanceSchema = new Schema(
  {
    book: { type: Schema.Types.ObjectId, ref: 'Book', required: true }, //reference to the associated book
    imprint: {type: String, required: true},
    status: {type: String, required: true, <strong>enum: ['Available', 'Maintenance', 'Loaned', 'Reserved']</strong>, <strong>default: 'Maintenance'</strong>},
    due_back: {type: Date, <strong>default: Date.now</strong>}
  }
);

// Virtual for bookinstance's URL
BookInstanceSchema
.virtual('url')
.get(function () {
  return '/catalog/bookinstance/' + this._id;
});

//Export model
module.exports = mongoose.model('BookInstance', BookInstanceSchema);</pre>

<p>The new things we show here are the field options:</p>

<ul>
 <li><code>enum</code>: This allows us to set the allowed values of a string. In this case, we use it to specify the availability status of our books (using an enum means that we can prevent mis-spellings and arbitrary values for our status)</li>
 <li><code>default</code>: We use default to set the default status for newly created bookinstances to maintenance and the default <code>due_back</code> date to <code>now</code> (note how you can call the Date function when setting the date!)</li>
</ul>

<p>Everything else should be familiar from our previous schema.</p>

<h3 id="Genre_model_-_challenge!">Genre model - challenge!</h3>

<p>Open your <strong>./models/genre.js</strong> file and create a schema for storing genres (the category of book, e.g. whether it is fiction or non-fiction, romance or military history, etc).</p>

<p>The definition will be very similar to the other models:</p>

<ul>
 <li>The model should have a <code>String</code> SchemaType called <code>name</code> to describe the genre.</li>
 <li>This name should be required and have between 3 and 100 characters.</li>
 <li>Declare a <a href="#Virtual_properties">virtual</a> for the genre's URL, named <code>url</code>.</li>
 <li>Export the model.</li>
</ul>

<h2 id="Testing_—_create_some_items">Testing — create some items</h2>

<p>That's it. We now have all models for the site set up!</p>

<p>In order to test the models (and to create some example books and other items that we can use in our next articles) we'll now run an <em>independent</em> script to create items of each type:</p>

<ol>
 <li>Download (or otherwise create) the file <a href="https://raw.githubusercontent.com/hamishwillee/express-locallibrary-tutorial/master/populatedb.js">populatedb.js</a> inside your <em>express-locallibrary-tutorial</em> directory (in the same level as <code>package.json</code>).

  <div class="note">
  <p><strong>Note:</strong> You don't need to know how <a href="https://raw.githubusercontent.com/hamishwillee/express-locallibrary-tutorial/master/populatedb.js">populatedb.js</a> works; it just adds sample data into the database.</p>
  </div>
 </li>
 <li>Enter the following commands in the project root to install the <em>async</em> module that is required by the script (we'll discuss this in later tutorials, )
  <pre class="brush: bash notranslate">npm install async</pre>
 </li>
 <li>Run the script using node in your command prompt, passing in the URL of your <em>MongoDB</em> database (the same one you replaced the <em>insert_your_database_url_here </em>placeholder with, inside <code>app.js</code> earlier):
  <pre class="brush: bash notranslate">node populatedb &lt;your mongodb url&gt;​​​​</pre>
 </li>
 <li>The script should run through to completion, displaying items as it creates them in the terminal.</li>
</ol>

<div class="note">
<p><strong>Tip:</strong> Go to your database on <a href="https://mlab.com/home">mLab</a>. You should now be able to drill down into individual collections of Books, Authors, Genres and BookInstances, and check out individual documents.</p>
</div>

<h2 id="まとめ">まとめ</h2>

<p>この記事では、Node/Express 上のデータベースと ORM について、また Mongoose のスキーマとモデルの定義方法について多くのことを学びました。次に、この情報を使用して、ローカルライブラリ Web サイト用の <code>Book</code>、<code>BookInstance</code>、<code>Author</code>、および <code>Genre</code> を設計および実装しました。</p>

<p>最後に、(スタンドアロンスクリプトを使用して) 多数のインスタンスを作成することによってモデルをテストしました。次の記事では、これらのオブジェクトを表示するためのページの作成について見ていきます。</p>

<h2 id="あわせて参照">あわせて参照</h2>

<ul>
 <li><a href="https://expressjs.com/en/guide/database-integration.html">Database integration</a> (Express ドキュメント)</li>
 <li><a href="http://mongoosejs.com/">Mongoose website</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/guide.html">Mongoose Guide</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/validation.html">Validation</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/schematypes.html">Schema Types</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/models.html">Models</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/queries.html">Queries</a> (Mongoose ドキュメント)</li>
 <li><a href="http://mongoosejs.com/docs/populate.html">Population</a> (Mongoose ドキュメント)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/skeleton_website", "Learn/Server-side/Express_Nodejs/routes", "Learn/Server-side/Express_Nodejs")}}</p>

<h2 id="このモジュール">このモジュール</h2>

<ul>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/Introduction">Express/Node のイントロダクション</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/development_environment">Node 開発環境の設定</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">Express チュートリアル: 地域図書館の Web サイト</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/skeleton_website">Express チュートリアル Part 2: スケルトン Web サイトの作成</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/mongoose">Express チュートリアル Part 3: データベースを使う (Mongoose を使用)</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/routes">Express チュートリアル Part 4: ルートとコントローラ</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/Displaying_data">Express チュートリアル Part 5: ライブラリデータの表示</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/forms">Express チュートリアル Part 6: フォームの操作</a></li>
 <li><a href="/ja/docs/Learn/Server-side/Express_Nodejs/deployment">Express チュートリアル Part 7: プロダクションへのデプロイ</a></li>
</ul>
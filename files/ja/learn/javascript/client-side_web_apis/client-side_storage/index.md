---
title: クライアント側ストレージ
slug: Learn/JavaScript/Client-side_web_APIs/Client-side_storage
tags:
  - API
  - Article
  - Beginner
  - CodingScripting
  - Guide
  - IndexedDB
  - JavaScript
  - Learn
  - Storage
  - Web Storage
  - ウェブストレージ
  - ガイド
  - 保存
  - 初心者
  - 学習
translation_of: Learn/JavaScript/Client-side_web_APIs/Client-side_storage
---
<p>{{LearnSidebar}}</p>

<div>{{PreviousMenu("Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</div>

<p class="summary">モダン・ウェブブラウザーは、ユーザーの許可のもとにウェブサイトがユーザーのコンピューター上にデータを保存して必要なときにそのデータを取得するための、いくつもの方法をサポートしています。このことにより、長期記憶のためにデータを存続させること、オフライン利用のためにサイトまたは文書を保存すること、サイトについてのユーザー独自の設定を保持すること、などなどが可能になります。本記事では、これらがどのようにして機能するのかについてのごく基本的な点を説明します。</p>

<table class="learn-box standard-table">
 <tbody>
  <tr>
   <th scope="row">前提知識:</th>
   <td>JavaScript の基本 (<a href="/ja/docs/Learn/JavaScript/First_steps">JavaScript の第一歩</a>、<a href="/ja/docs/Learn/JavaScript/Building_blocks">JavaScript の構成要素</a>、 <a href="/ja/docs/Learn/JavaScript/Objects">JavaScript オブジェクト入門</a> を参照)、<a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">ウェブ APIの紹介</a></td>
  </tr>
  <tr>
   <th scope="row">学習目標:</th>
   <td>アプリケーション・データを保存するためのクライアント側のストレージ API の使い方を学ぶこと</td>
  </tr>
 </tbody>
</table>

<h2 id="Client-side_storage" name="Client-side_storage">クライアント側での保存って?</h2>

<p>MDN 学習エリアの他の箇所で、<a href="/ja/docs/Learn/Server-side/First_steps/Client-Server_overview#Static_sites">静的なサイト</a> と <a href="/ja/docs/Learn/Server-side/First_steps/Client-Server_overview#Dynamic_sites">動的なサイト</a> の違いについて述べました。ほとんどの主要なモダン・ウェブサイトは動的です。つまり、ある種のデータベース (サーバー側のストレージ) を使ってデータをサーバー上に記憶し、必要なデータを取得するために<a href="/ja/docs/Learn/Server-side">サーバー側</a> のコードを実行し、そのデータを静的なページ雛型に挿入し、結果として出来上がった HTML をクライアントに提供して、それがユーザーのブラウザーによって表示されるようにします。</p>

<p>クライアント側での保存は類似の原理に基づいて機能しますが、これにはいくつかの異なる使い道があります。クライアント側での保存は、クライアント上に (つまりユーザーのマシン上に) データを保存して必要なときにそのデータを取得できるようにしてくれる、いくつかの JavaScript API から構成されています。クライアント側での保存には、たとえば以下のように多くの異なる用途があります。</p>

<ul>
 <li>サイトの環境設定を個人に合わせる (たとえば、カスタム・ウィジェット、カラースキーム、またはフォントサイズについて、ユーザーが選択したものを表示する、など)</li>
 <li>以前のサイト上の行動を存続させる (たとえば、前回のセッションからの買い物かごの中身を記憶しておく、ユーザーが以前ログインしたかどうかを憶えておく、など)</li>
 <li>サイトをより速く (かつ、潜在的にはより費用をかけずに) ダウンロードできるように、または、ネットワーク接続なしでサイトが利用可能となるように、データと資産をローカルに保存する</li>
 <li>ウェブ・アプリケーションが生成した文書を、オフラインで利用するために、ローカルに保存する</li>
</ul>

<p>クライアント側での保存とサーバ側での保存は、しばしば共に使われます。たとえば、複数の音楽ファイル (おそらくウェブゲームまたは音楽プレーヤー・アプリに使われる) をダウンロードし、それらの音楽ファイルをクライアント側のデータベース内に保存し、必要に応じて再生する、といったことが可能でしょう。ユーザーは、それらの音楽ファイルをただ一度ダウンロードするだけで済むでしょう。その後の訪問では、音楽ファイルは、ダウンロードされる代わりにデータベースから取得されるでしょう。</p>

<div class="note">
<p><strong>注</strong>: クライアント側のストレージ API を使って保存できるデータの量には、上限があります (もしかすると、個別の API ごとの上限と、累積的な上限の双方があるかもしれません)。正確な上限は、ブラウザーごとに異なりますし、もしかすると、ユーザーの設定によることもあるかもしれません。より詳しくは、<a href="/ja/docs/Web/API/IndexedDB_API/Browser_storage_limits_and_eviction_criteria">ブラウザーのストレージ制限と削除基準</a> を参照。</p>
</div>

<h3 id="Old_school_Cookies" name="Old_school_Cookies">旧式な方法: クッキー</h3>

<p>クライアント側での保存という考え方には、長い歴史があります。ウェブの初期から、ウェブサイト上でのユーザー体験を個別化するための情報を記憶するべく、サイトは <a href="/ja/docs/Web/HTTP/Cookies">クッキー</a> を使ってきました。そうしたクッキーは、ウェブ上で一般的に使われるクライアント側での保存の、最初期の形式です。</p>

<p>最近では、クライアント側のデータを保存するためのより簡単な仕組みが利用できるため、この記事ではクッキーの使用方法については説明しません。ただし、これはクッキーが現代のウェブで完全に役に立たないことを意味するわけではありません。クッキーは、ユーザーの個別化や状態に関連するデータを保存するために今でも一般的に使用されています。たとえば、セッション ID やアクセストークンです。クッキーの詳細については、<a href="/ja/docs/Web/HTTP/Cookies">HTTP cookies</a> の記事を参照してください。</p>

<h3 id="New_school_Web_Storage_and_IndexedDB" name="New_school_Web_Storage_and_IndexedDB">新方式派: ウェブストレージと IndexedDB</h3>

<p>前述の「簡単な」機能には次のものがあります:</p>

<ul>
 <li><a href="/ja/docs/Web/API/Web_Storage_API">Web Storage API</a> は、名前とそれに対応する値とからなる小規模なデータ項目を保存したり取り出したりするための、とても簡潔な構文を提供しています。これは、ユーザーの名前、ユーザーがログインしているかどうか、画面の背景にどの色を使うべきか、といったような、何らかの単純なデータを記憶するだけでよい場合に有用です。</li>
 <li><a href="/ja/docs/Web/API/IndexedDB_API">IndexedDB API</a> は、複雑なデータを保存するための完全なデータベース・システムをブラウザーに提供しています。これは、顧客レコードの完全な集合から、音声ファイルまたは動画ファイルのような複雑なデータ型にいたるまでの、種々の物事に対して使えます。</li>
</ul>

<p>以下ではこれらの API について学ぶことになります。</p>

<h3 id="The_future_Cache_API" name="The_future_Cache_API">将来: キャッシュ API</h3>

<p>いくつかのモダン・ブラウザーは、新しい {{domxref("Cache")}} API をサポートしています。この API は、特定の要求に対する HTTP 応答を記憶しておくために設計されています。 また、ネットワーク接続なしに後でサイトを利用できるように、ウェブサイト資産をオフラインに記憶しておく、といったようなことをするうえで非常に有用です。キャッシュは通常、<a href="/ja/docs/Web/API/Service_Worker_API">サービスワーカー API</a> と組み合わせて利用します。もっとも、必ずそうしなくてはならないというわけではありません。</p>

<p>キャッシュとサービスワーカーの利用は先進的な話題であり、この記事ではそれほど詳しくは扱いません。とは言うものの、後述の <a href="#offline_asset_storage">Offline asset storage</a> の節では、簡単な例をお見せします。</p>

<h2 id="Storing_simple_data_—_web_storage" name="Storing_simple_data_—_web_storage">単純なデータを保存する——ウェブストレージ</h2>

<p><a href="/ja/docs/Web/API/Web_Storage_API">Web Storage API</a> は大変使いやすいものです。(文字列や数などに限定された) データからなる単純な名前／値のペアを保存し、必要なときにその値を取り出します。</p>

<h3 id="Basic_syntax" name="Basic_syntax">基本的構文</h3>

<p>以下に方法を示しましょう。</p>

<ol>
 <li>
  <p>まず、GitHub 上の <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/web-storage/index.html">ウェブストレージの空白テンプレート</a> へ行ってください (新規タブで開いてください)。</p>
 </li>
 <li>
  <p>ブラウザーのデベロッパー・ツールの JavaScript コンソールを開いてください。</p>
 </li>
 <li>
  <p>ウェブストレージ・データのすべては、ブラウザー内部の二つのオブジェクト的な構造体の中に含まれます。つまり、{{domxref("Window.sessionStorage", "sessionStorage")}} と {{domxref("Window.localStorage", "localStorage")}} の中です。前者は、ブラウザーが開いている限り、データを存続させます (ブラウザーを閉じるとデータは失われます)。後者は、ブラウザーを閉じて、それから再びブラウザーを開いた後でさえも、データを存続させます。一般的には後者の方がより有用なので、本記事では後者を使います。</p>

  <p>{{domxref("Storage.setItem()")}} メソッドによって、ストレージ内にデータ項目を保存できます。このメソッドは二つの引数をとります。すなわち、その項目の名前と、その値です。JavaScript コンソールに以下のように打ち込んでみてください (もしお望みなら、値は御自分のお名前に変更してくださいね!)</p>

  <pre class="brush: js notranslate">localStorage.setItem('name','Chris');</pre>
 </li>
 <li>
  <p>{{domxref("Storage.getItem()")}} メソッドは一つの引数をとります。つまり、取り出したいデータ項目の名前です。そして、このメソッドは、その項目の値を返します。今度は JavaScript コンソールに以下の行を打ち込んでください。</p>

  <pre class="brush: js notranslate">let myName = localStorage.getItem('name');
myName</pre>

  <p>2 行目を入力すると、<code>myName</code> という変数が今や <code>name</code> というデータ項目の値を保有していることが分かるはずです。</p>
 </li>
 <li>
  <p>{{domxref("Storage.removeItem()")}} メソッドは一つの引数をとります。つまり、削除したいデータ項目の名前です。このメソッドは、ウェブストレージからその項目を削除します。JavaScript コンソールに以下の行を打ち込んでください。</p>

  <pre class="brush: js notranslate">localStorage.removeItem('name');
let myName = localStorage.getItem('name');
myName</pre>

  <p>3 行目は、今度は <code>null</code> を返すはずです。というのも、もはや <code>name</code> という項目はウェブストレージ内に存在しないからです。</p>
 </li>
</ol>

<h3 id="The_data_persists!" name="The_data_persists!">データが存続する!</h3>

<p>ウェブストレージの一つの重要な特徴は、ページ・ロードをまたいで (さらに、<code>localStorage</code> の場合には、ブラウザーを終了させてさえも) データが存続する、という点です。この特徴が機能しているところを見てみましょう。</p>

<ol>
 <li>
  <p>もう一度、ウェブストレージの空白テンプレートを開いてください。ただし今回は、本チュートリアルを開いたのとは別のブラウザーで開いてください! こうすることで、取り扱いがしやすくなるでしょう。</p>
 </li>
 <li>
  <p>以下の行をブラウザーの JavaScript コンソールに打ち込んでください。</p>

  <pre class="brush: js notranslate">localStorage.setItem('name','Chris');
let myName = localStorage.getItem('name');
myName</pre>

  <p><code>name</code> という項目が返されるのが分かるはずです。</p>
 </li>
 <li>
  <p>さてここでブラウザーを終了させてから再び起動して開いてください。</p>
 </li>
 <li>
  <p>再び、以下の行を入力してください。</p>

  <pre class="brush: js notranslate">let myName = localStorage.getItem('name');
myName</pre>

  <p>ブラウザーを終了させてから再び開いたというのに、それでも依然として値が利用可能である、ということが分かるはずです。</p>
 </li>
</ol>

<h3 id="Separate_storage_for_each_domain" name="Separate_storage_for_each_domain">ドメインごとに別々のストレージ</h3>

<p>ドメインごとに (ブラウザーにロードされた別々のウェブ・アドレスごとに)、別々のデータストアがあります。二つのウェブサイト (たとえば google.com と amazon.com) をロードして、一方のウェブサイトで項目を保存してみると、その項目は他方のウェブサイトでは利用できない、と分かるでしょう。</p>

<p>これには意義があります。もしウェブサイト同士がお互いのデータを見ることが可能であったら起こるであろうセキュリティ問題を想像できますよね!</p>

<h3 id="A_more_involved_example" name="A_more_involved_example">さらに込み入った例</h3>

<p>どのようにウェブストレージを使えるのかについてお教えするために、簡単で基礎的な事例を書くことによって、(ドメインごとのストレージという) この新たに得た知識を応用してみましょう。この事例では、名前を入力できるようにします。その入力の後、個人に合わせた挨拶を表示するべく、ページが更新されます。この状態は、ページ／ブラウザーのリロードをまたいでも存続するでしょう。なぜなら、名前がウェブストレージに記憶されているからです。</p>

<p>この例の HTML を <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/web-storage/personal-greeting.html">personal-greeting.html</a> で入手できます。これは、ヘッダーとコンテンツとフッターを備えた簡素なウェブサイトと、名前を入力するためのフォームとを含みます。</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15735/web-storage-demo.png" style="display: block; margin: 0 auto;"></p>

<p>この例を組み上げましょう。すると、これがどのように機能するのか理解できるでしょう。</p>

<ol>
 <li>
  <p>まず、御自分のコンピュータ上の新規ディレクトリに、<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/web-storage/personal-greeting.html">personal-greeting.html</a> というファイルのローカルコピーを作ってください。</p>
 </li>
 <li>
  <p>次に、<code>index.js</code> と呼ばれる JavaScript ファイルを、HTML がどのように参照しているのかに注意してください (40 行目を参照)。これ (<code>index.js</code>) を作成して、そこに JavaScript コードを書き込む必要があります。HTML ファイルと同じディレクトリに <code>index.js</code> というファイルを作成してください。</p>
 </li>
 <li>
  <p>この例で操作する必要のある HTML 項目 (features) のすべてに対する参照を作るところから取り掛かりましょう。それらの参照のすべてを定数として作ります。なぜなら、これらの参照は、アプリのライフサイクル内で変化する必要がないからです。以下の行を JavaScript ファイルに追加してください。</p>

  <pre class="brush: js notranslate">// 必要な定数を作ります。
const rememberDiv = document.querySelector('.remember');
const forgetDiv = document.querySelector('.forget');
const form = document.querySelector('form');
const nameInput = document.querySelector('#entername');
const submitBtn = document.querySelector('#submitname');
const forgetBtn = document.querySelector('#forgetname');

const h1 = document.querySelector('h1');
const personalGreeting = document.querySelector('.personal-greeting');</pre>
 </li>
 <li>
  <p>次に、送信ボタンが押されたときにフォームが実際にこのフォーム自体を送信することをやめさせるための、小規模なイベント・リスナーを含める必要があります。というのも、こうした送信は所望の振る舞いではないからです。以下に示すスニペットを、前のコードに追加してください。</p>

  <pre class="brush: js notranslate">// ボタンが押されたときにフォームが送信することをやめさせます。
form.addEventListener('submit', function(e) {
  e.preventDefault();
});</pre>
 </li>
 <li>
  <p>さてここで、イベント・リスナーを追加せねばなりません。そのイベント・リスナーのハンドラー関数は、"Say hello" ボタンがクリックされたときに実行されます。それぞれの断片が何を行うのかはコメントで詳しく説明してありますが、本質的にここでは、ユーザーがテキスト入力ボックスに入力した名前をとってきて、<code>setItem()</code> を用いてその名前をウェブストレージに保存し、その後、実際のウェブサイト上のテキストの更新を扱う <code>nameDisplayCheck()</code> と呼ばれる関数を実行しています。これをコードの末尾に加えてください。</p>

  <pre class="brush: js notranslate">// 'Say hello' ボタンがクリックされたら関数を実行します。
submitBtn.addEventListener('click', function() {
  // 入力された名前をウェブストレージに保存します。
  localStorage.setItem('name', nameInput.value);
  // 個人に合わせた挨拶を表示するとともにフォーム表示を更新する
  // 措置をとるべく、nameDisplayCheck() を実行します。
  nameDisplayCheck();
});</pre>
 </li>
 <li>
  <p> この時点で、"Forget" ボタンがクリックされたときに関数を実行するためのイベント・ハンドラーも必要です。"Forget" ボタンは、"Say hello" ボタンがクリックされた後にのみ表示されます (二つのフォーム状態が行ったり来たり切り替わります)。この関数では、<code>removeItem()</code> を用いてウェブストレージから <code>name</code> という項目を削除し、その後、表示を更新するために <code>nameDisplayCheck()</code> を再び実行します。これを末尾に付け加えてください。</p>

  <pre class="brush: js notranslate">// 'Forget' ボタンがクリックされたら関数を実行します。
forgetBtn.addEventListener('click', function() {
  // 保存してある名前をウェブストレージから削除します。
  localStorage.removeItem('name');
  // 再び一般的な挨拶を表示するとともにフォーム表示を更新する
  // 措置をとるべく、nameDisplayCheck() を実行します。
  nameDisplayCheck();
});</pre>
 </li>
 <li>
  <p>さて今や <code>nameDisplayCheck()</code> という関数そのものを定義すべきときです。ここでは、<code>localStorage.getItem('name')</code> を条件テストとして用いることにより、<code>name</code> という項目がウェブストレージに保存済みかどうかを調べます。もし保存済みなら、この呼び出しは <code>true</code> と評価されるでしょう。もし保存済みでなければ、<code>false</code> になるでしょう。もし <code>true</code> なら、個人に合わせた挨拶を表示し、フォームの "forget" の部分を表示し、フォームの "Say hello" の部分を隠します。もし <code>false</code> なら、一般的な挨拶を表示し、逆のことをします (フォームの "forget" の部分を隠し、フォームの "Say hello" の部分を表示します)。またもや末尾に以下のコードを追加してください。</p>

  <pre class="brush: js notranslate">// nameDisplayCheck() という関数を定義します。
function nameDisplayCheck() {
  // 'name' というデータ項目がウェブストレージに保存されているかどうかを調べます。
  if(localStorage.getItem('name')) {
    // もし保存されていたら、個人に合わせた挨拶を表示します。
    let name = localStorage.getItem('name');
    h1.textContent = 'Welcome, ' + name;
    personalGreeting.textContent = 'Welcome to our website, ' + name + '! We hope you have fun while you are here.';
    // フォームのうち 'remember' の部分を隠し、'forget' の部分を表示します。
    forgetDiv.style.display = 'block';
    rememberDiv.style.display = 'none';
  } else {
    // もし保存されていなければ、一般的な挨拶を表示します。
    h1.textContent = 'Welcome to our website ';
    personalGreeting.textContent = 'Welcome to our website. We hope you have fun while you are here.';
    // フォームのうち 'forget' の部分を隠し、'remember' の部分を表示します。
    forgetDiv.style.display = 'none';
    rememberDiv.style.display = 'block';
  }
}</pre>
 </li>
 <li>
  <p>最後に、ページがロードされるたびに <code>nameDisplayCheck()</code> という関数を実行せねばなりません。もしそうしなければ、個人に合わせた挨拶は、ページのリロードをまたがってまでは持続しなくなってしまうでしょう。以下のものをコードの末尾に追加してください。</p>

  <pre class="brush: js notranslate">document.body.onload = nameDisplayCheck;</pre>
 </li>
</ol>

<p>例が完成しました。よくできましたね! 現時点で残っているのは、コードを保存して HTML ページをブラウザーでテストすることだけです。<a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/web-storage/personal-greeting.html">ライブ実行される完成版をここで</a> 見られます。</p>

<div class="note">
<p><strong>注</strong>: <a href="/ja/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API">ウェブストレージ API の使用</a> のところには、探究するにはほんの少しだけ更に複雑な別の例もあります。</p>
</div>

<div class="note">
<p><strong>注</strong>: 完成版のソースのうち <code>&lt;script src="index.js" defer&gt;&lt;/script&gt;</code> という行では、<code>defer</code> 属性により、ページをロードし終わるまでは {{htmlelement("script")}} 要素の中身を実行しないように指定しています。</p>
</div>

<h2 id="Storing_complex_data_—_IndexedDB" name="Storing_complex_data_—_IndexedDB">複雑なデータを保存する—— IndexedDB</h2>

<p><a href="/ja/docs/Web/API/IndexedDB_API">IndexedDB API</a>  (ときには IDB と省略します) は、ブラウザーで利用可能であり、複雑で関係性のあるデータを保存できる、完全なデータベース・システムです。そしてそのデータの型は、文字列または数値のような単純な値に限定されません。動画や静止画像、そして、その他のものもほとんどすべて、IndexedDB インスタンスに保存できます。</p>

<p>しかし、これは高くつきます。IndexedDB の使用は、ウェブストレージ API の使用よりも遥かに複雑なのです。本節では、IndexedDB ができることのうち本当に表面的なところに触れるだけですが、始めるのに十分なだけのことは、お伝えしましょう。</p>

<h3 id="Working_through_a_note_storage_example" name="Working_through_a_note_storage_example">メモ書きの保存の事例を通して作業します</h3>

<p>ここでは、メモ書きをブラウザーに保存して好きなときにそれを見たり消したりできるようにする事例を、見ていただきましょう。その際、その例は御自分で組み立てていただきますが、進行に合わせて、IDB の最も根本的な部分について御説明します。</p>

<p>当該アプリは、以下のような見かけをしています。</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15744/idb-demo.png" style="border-style: solid; border-width: 1px; display: block; margin: 0px auto;"></p>

<p>メモ書きの各々には題名と何らかの本文があり、題名と本文のそれぞれは別々に編集できます。以下で見てゆく JavaScript コードには、何が起きているのかを理解する手助けとなる詳しいコメントがあります。</p>

<h3 id="Getting_started" name="Getting_started">始めますよ</h3>

<ol>
 <li>まず、<code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index.html">index.html</a></code> と <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/style.css">style.css</a></code> と <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index-start.js">index-start.js</a></code> というファイルのローカルコピーを、ローカルマシンの新規ディレクトリ内に作成してください。</li>
 <li>ファイルを見てください。HTML がかなり簡潔なのがお分かりでしょう。これは、ヘッダーとフッターのあるウェブサイトです。また、メモ書きを表示する場所と、データベースに新たなメモ書きを入力するためのフォームとを含む、本文コンテンツ領域もあります。 CSS は、何が起きているのかをより明瞭にするための、ある種の簡素なスタイルづけを提供しています。JavaScript ファイルは、宣言された五つの定数を含んでいます。つまり、 内部にメモ書きを表示することになる {{htmlelement("ul")}} 要素への参照と、題名および本文の {{htmlelement("input")}} 要素への参照と、{{htmlelement("form")}} 自体への参照と、{{htmlelement("button")}} への参照とを含んでいます。</li>
 <li>JavaScript ファイルの名前を <code>index.js</code> に変更してください。コードをそこに追加し始める準備がこれで整いました。</li>
</ol>

<h3 id="Database_initial_set_up" name="Database_initial_set_up">データベースの初期設定</h3>

<p>では、実際にデータベースを設定するために最初にすべきことを見てみましょう。</p>

<ol>
 <li>
  <p>定数の宣言の下に、以下の行を追加してください。</p>

  <pre class="brush: js notranslate">// 開いたデータベースを記憶しておくためのデータベース・オブジェクトのインスタンスを作成します。
let db;</pre>

  <p>ここでは、<code>db</code> と呼ばれる変数を宣言しています。これは後に、データベースを表すオブジェクトを記憶するのに使われます。この変数を何箇所かで使うつもりなので、物事を容易にするために、ここでこの変数を大域的に宣言しておきました。</p>
 </li>
 <li>
  <p>次に、以下のものをコードの末尾に加えてください。</p>

  <pre class="brush: js notranslate">window.onload = function() {

};</pre>

  <p>続きのコードはすべて、この <code>window.onload</code> イベント・ハンドラー関数——ウィンドウの {{event("load")}} イベントが発火したときに呼ばれます——の中に書いてゆきます。アプリが完全にロード動作を終えるよりも前には IndexedDB 機能を使おうとはしないよう保証するために、そうしています  (もしそう保証しなかったら、失敗する可能性があります)。</p>
 </li>
 <li>
  <p><code>window.onload</code> ハンドラーの中に、以下のものを追加してください。</p>

  <pre class="brush: js notranslate">// データベースを開きます。データベースは、まだ存在していない場合には
// 新規作成されます (後述の onupgradeneeded を参照)。
let request = window.indexedDB.open('notes', 1);</pre>

  <p>この行では、<code>notes</code> と呼ばれるデータベースのバージョン <code>1</code> を開く <code>request</code> (要求) を作成します。もしそのデータベースがまだ存在しなければ、後述のコードによって新規作成されます。IndexedDB の全体を通じて、この要求パターンが非常に高頻度で使われることが、いずれお分かりになるでしょう。データベース操作には時間がかかります。その結果を待つ間、ブラウザーをハングさせることはお望みでないでしょうから、データベース操作は {{Glossary("asynchronous")}} (非同期) となっています。このことが意味するのは、結果は直ちに生じるのではなく、将来のいずれかの時点で生じるだろうということ、および、結果が出たときには通知されるということです。</p>

  <p>こういったことを IndexedDB で扱うために、要求オブジェクト (何とでも好きなように呼んで構いませんが、何を目的としたものなのかが明白になるので、<code>request</code> (要求) と呼んでおきました) を作成します。それから、要求が完了する、失敗する、などの際にコードを実行するために、いくつかのイベント・ハンドラーを使います。この点については、使用されているところを後で見ることになります。</p>

  <div class="note">
  <p><strong>注</strong>:  バージョン番号は重要です。(たとえばテーブル構造を変更することによって) データベースをアップグレードしたい場合には、上げたバージョン番号や、<code>onupgradeneeded</code> ハンドラー (下記参照) の内部で指定される別のスキーマなどを使って、コードを再度実行せねばなりません。この簡単なチュートリアルでは、データベースのアップグレードは扱いません。</p>
  </div>
 </li>
 <li>
  <p>さて今度は、前に追加した分のすぐ下に、以下のイベント・ハンドラーを追加してください。今度もまた、<code>window.onload</code> ハンドラーの中への追加です。</p>

  <pre class="brush: js notranslate">// onerror ハンドラーは、データベースがうまく開けなかったことを意味します。
request.onerror = function() {
  console.log('Database failed to open');
};

// onsuccess ハンドラーは、データベースがうまく開けたことを意味します。
request.onsuccess = function() {
  console.log('Database opened successfully');

  // 開いたデータベース・オブジェクトを、db という変数に記憶します。この変数は、以下でたくさん使われます。
  db = request.result;

  // IDB 内の既存のメモ書きを表示するために、displayData() 関数を実行します。
  displayData();
};</pre>

  <p>要求は失敗した、と伝えつつシステムが戻ってくる場合には、{{domxref("IDBRequest.onerror", "request.onerror")}} というハンドラーが実行されます。これによって、(要求が失敗したという) この問題に対処できるようになります。この簡単な例では、単に JavaScript コンソールにメッセージを印字します。</p>

  <p>他方、{{domxref("IDBRequest.onsuccess", "request.onsuccess")}} ハンドラーは、要求が成功裡に戻ってくる場合、つまりデータベースをうまく開けた場合に、実行されます。この場合、開いたデータベースを表すオブジェクトが、{{domxref("IDBRequest.result", "request.result")}} というプロパティで利用可能となります。それにより、データベースを操作できるようになります。後で使うために、と事前に作っておいた <code>db</code> という変数に、このオブジェクトを保存します。また、<code>displayData()</code> と呼ばれるカスタム関数も実行します。この関数は、データベース内のデータを {{HTMLElement("ul")}} 内部に表示します。すでにデータベース内にあるメモ書きが、ページがロードされ次第すぐに表示されるように、ここでこの関数を実行しています。この関数を定義する様子は、後で見ることにしましょう。</p>
 </li>
 <li>
  <p>本節の最後では、データベースを設定するためには多分もっとも重要なイベント・ハンドラーを追加しましょう。つまり、{{domxref("IDBOpenDBRequest.onupgradeneeded", "request.onupgradeneeded")}} です。このハンドラーは、データベースがまだ設定されていなかった場合、あるいは、保存済みの既存のデータベースよりも上のバージョン番号でデータベースが開かれた場合 (アップグレードを行う場合) に、実行されます。前のハンドラーの下に、以下のコードを追加してください。</p>

  <pre class="brush: js notranslate">// これがまだ実行されていない場合に、データベースのテーブルを設定します。
request.onupgradeneeded = function(e) {
  // 開いたデータベースに対する参照を求めます。
  let db = e.target.result;

  // 自動的にインクリメントするキーを含んでおり、メモ書きを中に保存するための
  // (基本的に一つのテーブルに類似した) objectStore を、作成します。
  let objectStore = db.createObjectStore('notes', { keyPath: 'id', autoIncrement:true });

  // objectStore が含むことになるデータ項目を定義します。
  objectStore.createIndex('title', 'title', { unique: false });
  objectStore.createIndex('body', 'body', { unique: false });

  console.log('Database setup complete');
};</pre>

  <p>ここは、データベースのスキーマ (構造) ——すなわち、データベースが含む列 (ないしフィールド) の集合——を定義している箇所です。ここではまず、<code>e.target.result</code> (イベント・ターゲットの <code>result</code> というプロパティ) から、既存のデータベースへの参照を求めていますが、これ (<code>e.target</code> というイベント・ターゲット) は、<code>request</code> というオブジェクトです。この行は、<code>onsuccess</code> ハンドラーの中の <code>db = request.result;</code> という行と等価です。しかし、それとは別に、ここでこのようにする必要があります。なぜなら、<code>onupgradeneeded</code> ハンドラーは、(もし必要な場合には) <code>onsuccess</code> ハンドラーよりも前に実行されることになる——つまり、もしここでこのようにしておかなければ、<code>db</code> の値を利用できない——からです。</p>

  <p>それから、{{domxref("IDBDatabase.createObjectStore()")}} を用いて、開いたデータベースの内部に新たなオブジェクト・ストアを作成します。これは、従来のデータベース・システムにおける一つのテーブルと等価です。このオブジェクト・ストアには <code>notes</code> という名前をつけました。また、<code>id</code> と呼ばれる <code>autoIncrement</code> キーのフィールドも指定しました。新規レコードの各々において、このフィールドには、インクリメントされた値が自動的に与えられ、開発者は、このフィールドを明示的に設定する必要がありません。キーであるがゆえに、<code>id</code> フィールドは、たとえばレコードを削除または表示する際に、レコードを一意に識別するのに使われることでしょう。</p>

  <p>{{domxref("IDBObjectStore.createIndex()")}} メソッドを用いて、別の二つのインデックス (フィールド) も作成します。すなわち、<code>title</code>  (それぞれのメモ書きの題名を含むことになります) と、<code>body</code> (そのメモ書きの本文を含むことになります) を作成します。</p>
 </li>
</ol>

<p>以上のようにこの簡素なデータベース・スキーマを設定したので、データベースにレコードを追加し始めれば、それぞれのレコードは、以下の行のようなオブジェクトとして表現されることでしょう。</p>

<pre class="brush: js notranslate">{
  title: "Buy milk",
  body: "Need both cows milk and soy.",
  id: 8
}</pre>

<h3 id="Adding_data_to_the_database" name="Adding_data_to_the_database">データをデータベースに追加します</h3>

<p>それでは、どのようにしたらデータベースにレコードを追加できるか、その方法を見てみましょう。これは、ページ上のフォームを使って行われます。</p>

<p>前のイベント・ハンドラーの下に (ただし、やはり <code>window.onload</code> ハンドラーの内部に)、 以下の行を追加してください。以下の行では、フォームが送信された際に (送信 {{htmlelement("button")}} が押され、成功したフォーム送信、という結果に至ったときに)、<code>addData()</code> と呼ばれる関数を実行する、<code>onsubmit</code> ハンドラーを設定しています。</p>

<pre class="brush: js notranslate">// フォームが送信されたときに addData() 関数が実行されるように、onsubmit ハンドラーを作成します。
form.onsubmit = addData;</pre>

<p>では、<code>addData()</code> 関数を定義しましょう。上記の行の下に、以下のものを追加してください。</p>

<pre class="brush: js notranslate">// addData() 関数を定義します。
function addData(e) {
  // デフォルト動作を防止します。従来通りの方法でフォームを送信したくはないからです。
  e.preventDefault();

  // フォーム・フィールドに入力された値を求めます。そして、それらの値を、データベースへ挿入すべく準備してあるオブジェクトに保存します。
  let newItem = { title: titleInput.value, body: bodyInput.value };

  // 読み書きのデータベース・トランザクションを開いて、データの追加に備えます。
  let transaction = db.transaction(['notes'], 'readwrite');

  // データベースに追加済みのオブジェクト・ストアを呼び出します。
  let objectStore = transaction.objectStore('notes');

  // newItem というオブジェクトをオブジェクト・ストアに追加するための要求を作ります。
  let request = objectStore.add(newItem);
  request.onsuccess = function() {
    // フォームをクリアして、次のエントリーの追加に備えます。
    titleInput.value = '';
    bodyInput.value = '';
  };

  // すべてが済んだら、完了するトランザクションの成功を報告します。
  transaction.oncomplete = function() {
    console.log('Transaction completed: database modification finished.');

    // displayData() を再度実行することによって、データの表示を更新して、新たに追加した項目を表示します。
    displayData();
  };

  transaction.onerror = function() {
    console.log('Transaction not opened due to error');
  };
}</pre>

<p>これは割と複雑ですね。噛み砕くと、以下の通りです。</p>

<ul>
 <li>フォームが実際に従来通りの方法で送信してしまうこと (これはページ・リフレッシュを引き起こし、体験をそこなうでしょう) を防ぐために、イベント・オブジェクトに対して {{domxref("Event.preventDefault()")}} を実行します。</li>
 <li>データベースに入力すべきレコードを表すオブジェクトを作成します。その際、そのオブジェクトには、フォーム入力からの値を埋め込みます。<code>id</code> の値を明示的に含める必要がないことに注意してください。以前説明したとおり、これは自動的に埋め込まれます。</li>
 <li>{{domxref("IDBDatabase.transaction()")}} メソッドを用いて、<code>notes</code> というオブジェクト・ストアに対する<code>readwrite</code> (読み書き) トランザクションを開きます。このトランザクション・オブジェクトのおかげでオブジェクト・ストアにアクセスできるようになり、オブジェクト・ストアに対して何か——たとえば新規レコードの追加など——を行えるようになります。</li>
 <li>{{domxref("IDBTransaction.objectStore()")}}メソッドを用いてオブジェクト・ストアにアクセスし、その結果を <code>objectStore</code> という変数に保存します。</li>
 <li> {{domxref("IDBObjectStore.add()")}} を用いて、データベースに新規レコードを追加します。これは、以前見たのと同様の方法で、要求オブジェクトを作り出します。</li>
 <li>ライフサイクル内での重大な時点 (クリティカル・ポイント) においてコードを実行するために、<code>request</code> (要求) と <code>transaction</code> (トランザクション) に対する一群のイベント・ハンドラーを追加します。要求が成功したら、次のメモ書きの入力に備えてフォーム入力をクリアします。トランザクションが完了したら、ページ上のメモ書きの表示を更新するために、<code>displayData()</code> 関数を再び実行します。</li>
</ul>

<h3 id="Displaying_the_data" name="Displaying_the_data">データを表示します</h3>

<p>すでにコード内で <code>displayData()</code> を二度も参照したからには、多分これを定義すべきでしょうね。以下のものをコードに (今までの関数定義の下に) 追加してください。</p>

<pre class="brush: js notranslate">// displayData() 関数を定義します。
function displayData() {
  // ここでは、表示を更新するたびにリスト要素の中身を空にします。
  // もしこのようにしなかったら、新たなメモ書きを追加するたびに複製を列挙する羽目になるでしょう。
  while (list.firstChild) {
    list.removeChild(list.firstChild);
  }

  // オブジェクト・ストアを開き、それから、カーソルを取得します。
  // カーソルは、ストア内の異なるデータ項目のすべてにわたって反復処理を行うものです。
  let objectStore = db.transaction('notes').objectStore('notes');
  objectStore.openCursor().onsuccess = function(e) {
    // カーソルへの参照を求めます。
    let cursor = e.target.result;

    // 反復処理を行うべき別のデータ項目がまだあれば、このコードを実行し続けます。
    if(cursor) {
      // 各データ項目を表示する際にそのデータ項目を中に入れるための、リスト項目と h3 と p とを作成します。
      // HTML 断片を組み立てて、それをリスト内の最後に追加します。
      const listItem = document.createElement('li');
      const h3 = document.createElement('h3');
      const para = document.createElement('p');

      listItem.appendChild(h3);
      listItem.appendChild(para);
      list.appendChild(listItem);

      // h3 および para の内部に、カーソルからのデータを入れます。
      h3.textContent = cursor.value.title;
      para.textContent = cursor.value.body;

      // listItem の属性内部に、このデータ項目の ID を保存します。こうすると、
      // listItem がどの項目に対応しているのかがわかります。これは、後で項目を削除したくなったときに有用です。
      listItem.setAttribute('data-note-id', cursor.value.id);

      // ボタンを作成し、それを各 listItem の内部に設置します。
      const deleteBtn = document.createElement('button');
      listItem.appendChild(deleteBtn);
      deleteBtn.textContent = 'Delete';

      // ボタンがクリックされたら deleteItem() 関数が実行されるように、
      // イベント・ハンドラーを設定します。
      deleteBtn.onclick = deleteItem;

      // カーソルにおける次の項目へと反復処理を進めます。
      cursor.continue();
    } else {
      // またもや、リスト項目が空であれば、'No notes stored' (メモ書きは何も保存されていません) というメッセージを表示します。
      if(!list.firstChild) {
        const listItem = document.createElement('li');
        listItem.textContent = 'No notes stored.';
        list.appendChild(listItem);
      }
      // 反復処理をすべきカーソル項目がこれ以上ない場合、そのように示します。
      console.log('Notes all displayed');
    }
  };
}</pre>

<p>再びになりますが、これを噛み砕いてみましょう。</p>

<ul>
 <li>まず、更新した中身を埋め込む前に、{{htmlelement("ul")}} 要素の中身を空っぽにします。これを行わないと、遂には、更新のたびに追加された複製された中身からなる巨大なリストができあがってしまいます。</li>
 <li>次に、{{domxref("IDBDatabase.transaction()")}} と {{domxref("IDBTransaction.objectStore()")}} を用いて、<code>addData()</code> で行ったのと同様にして (ただしここではこれらを繋いで 1 行にまとめている点が異なりますが)、<code>notes</code> というオブジェクト・ストアへの参照を求めます。</li>
 <li>次の段階は、{{domxref("IDBObjectStore.openCursor()")}} メソッドを使って、カーソルに対する要求を開くことです。カーソルとは、オブジェクト・ストア内の全レコードにわたって反復処理を行うのに使える構造体です。コードをより簡潔にするために、この行の最後に <code>onsuccess</code> ハンドラーを繋げています。カーソルが成功裡に返されると、このハンドラーが実行されます。</li>
 <li><code>let cursor = e.target.result</code>  を用いて、カーソル自体 ({{domxref("IDBCursor")}} オブジェクト) に対する参照を求めています。</li>
 <li>次に、カーソルがデータストアのレコードを含むか否かを調べます (<code>if(cursor){ ... }</code>)。もし含むなら、DOM 断片を作成し、その断片にレコードのデータを埋め込み、ページ内に (<code>&lt;ul&gt;</code> 要素の内部に) その断片を挿入します。また、クリックされたら <code>deleteItem()</code> 関数を実行することによって当該メモ書きを削除するような削除ボタンも含めておきます。この関数は、次の節で見ることにします。</li>
 <li><code>if</code> ブロックの最後では、{{domxref("IDBCursor.continue()")}} メソッドを用いてカーソルをデータストア内の次のレコードへと進め、<code>if</code> ブロックの中身を再び実行します。反復処理をすべき別のレコードがある場合には、こうすることにより、その別のレコードがページに挿入されることになり、その後また <code>continue()</code> が実行され、以下同様に続きます。</li>
 <li>反復処理をすべき対象のレコードがもうない場合、<code>cursor</code> は <code>undefined</code> を返すことになります。よって、<code>if</code> ブロックの代わりに <code>else</code> ブロックが実行されることになります。このブロックでは、<code>&lt;ul&gt;</code> に何らかのメモ書きが挿入されたかどうかを調べます。もし何も挿入されていなければ、何もメモ書きが保存されていなかった旨を述べるメッセージを挿入します。</li>
</ul>

<h3 id="Deleting_a_note" name="Deleting_a_note">メモ書きを削除します</h3>

<p>上述のとおり、メモ書きの削除ボタンが押されると、そのメモ書きは削除されます。これは、<code>deleteItem()</code> 関数により達成されます。この関数は以下のようなものです。</p>

<pre class="brush: js notranslate">// deleteItem() 関数を定義します。
function deleteItem(e) {
  // 削除したいタスクの名前 (訳注: ID の間違い?) を取り出します。
  // それを IDB で使おうとする前に、数値に変換する必要があります。
  // IDB のキーの値には、型による区別があるのです。
  let noteId = Number(e.target.parentNode.getAttribute('data-note-id'));

  // データベース・トランザクションを開き、当該タスクを削除します。その際、上記で取得した ID を用いて、当該タスクを見つけます。
  let transaction = db.transaction(['notes'], 'readwrite');
  let objectStore = transaction.objectStore('notes');
  let request = objectStore.delete(noteId);

  // データ項目を削除したことを報告します。
  transaction.oncomplete = function() {
    // ボタンの親——リスト項目——を削除します。
    // すると、それはもはや表示されなくなります。
    e.target.parentNode.parentNode.removeChild(e.target.parentNode);
    console.log('Note ' + noteId + ' deleted.');

    // 再びになりますが、リスト項目が空の場合は、'No notes stored' (メモ書きは何も保存されていません) というメッセージを表示します。
    if(!list.firstChild) {
      let listItem = document.createElement('li');
      listItem.textContent = 'No notes stored.';
      list.appendChild(listItem);
    }
  };
}</pre>

<ul>
 <li>これの最初の部分は説明を要します。 <code>Number(e.target.parentNode.getAttribute('data-note-id'))</code> を用いて、削除すべきレコードの ID を取り出します。レコードの ID は、最初にその <code>&lt;li&gt;</code> が表示された際にその <code>&lt;li&gt;</code> の <code>data-note-id</code> という属性に保存されている、ということを思い出してください。しかし、その属性は、 <a href="/ja/docs/Web/JavaScript/Reference/Global_Objects/Number">Number()</a> というグローバル・ビルトイン・オブジェクトを通じて渡す必要があります。なぜなら、属性は今のところ文字列であり、こうしなければデータベースに認識されないからです。</li>
 <li>それから、以前に見たのと同じパターンを使って、オブジェクト・ストアへの参照を求めます。そして、{{domxref("IDBObjectStore.delete()")}} メソッドを用いて、データベースから当該レコードを削除します。その際、データベースには ID を渡します。</li>
 <li>データベース・トランザクションが完了したら、当該メモ書きの <code>&lt;li&gt;</code> を DOM から削除します。そして再び、<code>&lt;ul&gt;</code> が現時点で空かどうかを調べ、適宜注記を挿入します。</li>
</ul>

<p>さあ、これで全部終わりです! あなたの例は今やちゃんと動くはずですよ。</p>

<p>もし問題があれば、気軽に <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/indexeddb/notes/">ライブ例と突き合わせてみてください</a> (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index.js">ソースコード</a> も参照してください)。</p>

<h3 id="Storing_complex_data_via_IndexedDB" name="Storing_complex_data_via_IndexedDB">IndexedDB を通じて複雑なデータを保存します</h3>

<p>上述のとおり、IndexedDB は、単純なテキスト文字列以上のものを保存するのに使えます。望むものはほとんど何でも——動画や静止画像のブロブ (blob) のような、複雑なオブジェクトまで含めて——保存できるのです。しかも、他のどの型のデータと比べても、達成するのがずっと困難だという訳でもないのです。</p>

<p>やり方を実演するために、<a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/client-side-storage/indexeddb/video-store">IndexedDB 動画ストア</a> と呼ばれる別の例を書きました (<a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/indexeddb/video-store/">ここでライブで動いているところも</a> 参照してください)。この例を最初に実行すると、すべての動画をネットワークからダウンロードして IndexedDB データベースに保存し、それから、{{htmlelement("video")}} 要素内部の UI の中に動画を表示します。二度目に実行すると、動画を表示する前に、データベース内の動画を見つけ出し、(ネットワークからダウンロードする) 代わりにそこから動画を取ってきます。こうすることにより、後続のロードは高速化され、帯域幅をあまり食わなくなります。</p>

<p>この例のもっとも興味深い部分を見て回りましょう。すべては見ないことにします。というのも、多くの部分は前の例に類似しており、コードにはちゃんとコメントがつけてありますから。</p>

<ol>
 <li>
  <p>この単純な例のために、取得すべき動画の名前をオブジェクトの配列の形で保存しておきました。</p>

  <pre class="brush: js notranslate">const videos = [
  { 'name' : 'crystal' },
  { 'name' : 'elf' },
  { 'name' : 'frog' },
  { 'name' : 'monster' },
  { 'name' : 'pig' },
  { 'name' : 'rabbit' }
];</pre>
 </li>
 <li>
  <p>まずはじめに、データベースを成功裡に開くことができたら、<code>init()</code> 関数を実行します。これは、異なる動画の名前をループしてゆきますが、その際、それぞれの名前で識別されるレコードを <code>videos</code> というデータベースからロードしようと試みます。</p>

  <p>各々の動画がデータベース内で見つかったら (これは、<code>request.result</code> が <code>true</code> と評価されるかどうかを調べることにより、容易に確認できます。もしレコードが存在しなければ、<code>undefined</code> となります)、その動画ファイル (ブロブとして保存されています) および動画の名前が、UI に配置するために、すぐに <code>displayVideo()</code> 関数へと渡されます。もし動画がデータベース内で見つからなければ、動画の名前が <code>fetchVideoFromNetwork()</code> 関数に渡されます。それが何のためか、見当がついていることでしょうが……そう、その動画をネットワークから取ってくるためです。</p>

  <pre class="brush: js notranslate">function init() {
  // 動画の名前を一つずつループしてゆきます。
  for(let i = 0; i &lt; videos.length; i++) {
    // トランザクションを開き、オブジェクト・ストアを取得し、名前によって各動画を get() します。
    let objectStore = db.transaction('videos').objectStore('videos');
    let request = objectStore.get(videos[i].name);
    request.onsuccess = function() {
      // もし結果がデータベース内に存在したら (存在しなければ undefined)、
      if(request.result) {
        // displayVideo() を用いて、動画を IDB から取り出して表示します。
        console.log('taking videos from IDB');
        displayVideo(request.result.mp4, request.result.webm, request.result.name);
      } else {
        // 動画をネットワークから取ってきます。
        fetchVideoFromNetwork(videos[i]);
      }
    };
  }
}</pre>
 </li>
 <li>
  <p>以下のスニペットは、<code>fetchVideoFromNetwork()</code> の内部から取ったものです。ここでは、二つの別々の {{domxref("fetch()", "WindowOrWorkerGlobalScope.fetch()")}} 要求を用いて、MP4 版の動画と WebM 版の動画を取ってきます。それから、{{domxref("blob()", "Body.blob()")}} メソッドを用いて、それぞれの応答の本体をブロブとして抽出します。このブロブは、保存して後で表示することの可能な、動画のオブジェクト表現を与えてくれます。</p>

  <p>しかし、ここで問題があります。これらの二つの要求はどちらも非同期的なのですが、双方のプロミスが成立 (fulfill) した場合にだけ動画を表示もしくは保存しようと試みたいのです。幸い、そうした問題を扱うビルトイン・メソッドがあります。すなわち  {{jsxref("Promise.all()")}} です。これは一つの引数——成立したかどうかを調べたい個々のプロミスすべてに対する参照を配列に入れたもの——をとり、これ自体がプロミスに基づいています。</p>

  <p>それらのプロミスすべてが成立したら、成立した個々の値すべてを含む配列をともなって、<code>all()</code> プロミスも成立します。<code>all()</code> のブロック内部では、以前 UI に動画を表示するために行ったのと同様にして <code>displayVideo()</code> 関数を呼び出していること、そして、それらの動画をデータベース内に保存するために <code>storeVideo()</code> 関数も呼び出していることが、お分かりでしょう。</p>

  <pre class="brush: js notranslate">let mp4Blob = fetch('videos/' + video.name + '.mp4').then(response =&gt;
  response.blob()
);
let webmBlob = fetch('videos/' + video.name + '.webm').then(response =&gt;
  response.blob()
);

// 双方のプロミスが成立したときのみ、次のコードを実行します。
Promise.all([mp4Blob, webmBlob]).then(function(values) {
  // ネットワークから取ってきた動画を、displayVideo() により表示します。
  displayVideo(values[0], values[1], video.name);
  // storeVideo() を用いて、その動画を IDB に保存します。
  storeVideo(values[0], values[1], video.name);
});</pre>
 </li>
 <li>
  <p>まず <code>storeVideo()</code> を見ましょう。これは、データベースにデータを追加するための上記の例で見たパターンに、とてもよく似ています。つまり、<code>readwrite</code> (読み書き) トランザクションを開き、<code>videos</code> に対するオブジェクト・ストア参照を求め、データベースに追加すべきレコードを表すオブジェクトを作成し、それから、{{domxref("IDBObjectStore.add()")}} を用いてそのオブジェクトを単純に追加しています。</p>

  <pre class="brush: js notranslate">function storeVideo(mp4Blob, webmBlob, name) {
  // トランザクションを開き、オブジェクト・ストアを求めます。IDB に書き込めるようにするために、これは読み書きトランザクションにしておきます。
  let objectStore = db.transaction(['videos'], 'readwrite').objectStore('videos');
  // IDB に追加するレコードを作成します。
  let record = {
    mp4 : mp4Blob,
    webm : webmBlob,
    name : name
  }

  // add() を使ってレコードを IDB に追加します。
  let request = objectStore.add(record);

  ...

};</pre>
 </li>
 <li>
  <p>最後に、<code>displayVideo()</code> があります。これは、UI に動画を挿入するのに必要な DOM 要素を作成してから、それらの DOM 要素をページに追加します。これの一番面白い部分は、以下に示した箇所です。<code>&lt;video&gt;</code> 要素内に動画ブロブを実際に表示するには、{{domxref("URL.createObjectURL()")}} メソッドを使って、オブジェクト URL (メモリに記憶されている動画ブロブを指し示す内部 URL) を作成せねばならないのです。それが済んだら、オブジェクト URL を {{htmlelement("source")}} 要素の <code>src</code> 属性の値として設定できて、物事がうまく機能します。</p>

  <pre class="brush: js notranslate">function displayVideo(mp4Blob, webmBlob, title) {
  // ブロブからオブジェクト URL を作成します。
  let mp4URL = URL.createObjectURL(mp4Blob);
  let webmURL = URL.createObjectURL(webmBlob);

  ...

  const video = document.createElement('video');
  video.controls = true;
  const source1 = document.createElement('source');
  source1.src = mp4URL;
  source1.type = 'video/mp4';
  const source2 = document.createElement('source');
  source2.src = webmURL;
  source2.type = 'video/webm';

  ...
}</pre>
 </li>
</ol>

<h2 id="Offline_asset_storage" name="Offline_asset_storage">オフラインでの資産の保存</h2>

<p>上記の例は、IndexedDB データベース内に大規模な資産を保存するアプリの作り方を既に示しており、こうすることで、それらの大規模な資産を二度以上ダウンロードする必要性をなくしています。これは既にユーザー体験にとっての多大なる進歩ではありますが、まだ一つ欠けていることがあります。すなわち、依然として、主たる HTML と CSS と JavaScript のファイルを、サイトにアクセスするたびにダウンロードせねばならないのです。これが意味することは、ネットワーク接続がない場合にはサイトが動作しないということです。</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15759/ff-offline.png" style="border-style: solid; border-width: 1px; display: block; height: 307px; margin: 0px auto; width: 765px;"></p>

<p>ここは、 <a href="/ja/docs/Web/API/Service_Worker_API">サービスワーカー</a> およびそれと緊密に関連した <a href="/ja/docs/Web/API/Cache">キャッシュ API</a> の出番です。</p>

<p>サービスワーカーとは、ただ単に置いてあって、特定のオリジン (ウェブサイト、または、あるドメインにあるウェブサイトの一部) に対して、そこにブラウザでアクセスした際に登録される、JavaScript ファイルのことです。登録されれば、サービスワーカーは、当該オリジンで利用可能なページを制御できます。サービスワーカーは、ロードされたページとネットワークとの間に位置して、当該オリジン宛のネットワーク要求を横取りすることにより、こうした制御を行います。</p>

<p>サービスワーカーが要求を横取りすると、その要求に対して望むことは何でも行えますが (<a href="/ja/docs/Web/API/Service_Worker_API#Other_use_case_ideas">使用例の案</a> を参照)、典型例では、ネットワーク応答をオフラインに保存しており、その後、要求に応じて、ネットワークからの応答の代わりに、保存してあるそれらの応答を提供しています。これによって事実上、ウェブサイトを完全にオフラインで機能させることが可能になります。</p>

<p>キャッシュ API は、クライアント側での保存のもう一つの仕組みですが、これにはちょっとした相違点があります。キャッシュ API は HTTP 応答を保存するように設計されているのです。そのため、サービスワーカーと一緒に使うと、とてもうまく機能します。</p>

<div class="note">
<p><strong>注</strong>: サービスワーカーとキャッシュは、現在、ほとんどのモダン・ブラウザーでサポートされています。執筆時点では、Safari はまだ実装するのに忙しかったのですが、もうすぐサポートされるはずです。</p>
</div>

<h3 id="A_service_worker_example" name="A_service_worker_example">サービスワーカーの例</h3>

<p>これがどのような感じなのかについて少しばかりお教えするために、例を見ましょう。前節で見た動画ストアの例の、別のバージョンを作っておきました。このバージョンは、サービスワーカーを介してキャッシュ API で HTML と CSS と JavaScript も保存する点を除いて、同等に機能しますが、この点のおかげで、この例がオフラインで実行できるようになるのです!</p>

<p><a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/">サービスワーカーを用いた IndexedDB 動画ストアがライブで実行中のところ</a> をご覧ください。また、<a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/client-side-storage/cache-sw/video-store-offline">ソースコードも参照してください</a>。</p>

<h4 id="Registering_the_service_worker" name="Registering_the_service_worker">サービスワーカーを登録します</h4>

<p>注意すべき第一の点は、主たる JavaScript ファイル中に追加のコードが少々ある点です (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/cache-sw/video-store-offline/index.js">index.js</a> を参照)。まず、{{domxref("Navigator")}} オブジェクトにおいて <code>serviceWorker</code> メンバーが利用可能かどうかを調べる機能検出検査を行います。もしこれが true を返したら、サービスワーカーの少なくとも基本部分がサポートされていることが分かります。ここの内部では、{{domxref("ServiceWorkerContainer.register()")}} メソッドを用いて、<code>sw.js</code> ファイルに含まれるサービスワーカーを、このファイルのあるオリジンに対して登録します。すると、同一ディレクトリまたは下位ディレクトリにあるページを制御できるようになります。このメソッドのプロミスが成立すると、サービスワーカーは登録されたものと見なされます。</p>

<pre class="brush: js notranslate">  // サイトがオフラインで動くようにする処理を制御するために、サービスワーカーを登録します。

  if('serviceWorker' in navigator) {
    navigator.serviceWorker
             .register('/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js')
             .then(function() { console.log('Service Worker Registered'); });
  }</pre>

<div class="note">
<p><strong>注</strong>: <code>sw.js</code> ファイルに至るまでの、与えられたパスは、サイト・オリジンに対して相対的なのであり、上記コードを含む JavaScript ファイルに対して相対的なのではありません。サービスワーカーは <code>https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js</code> にあります。オリジンは <code>https://mdn.github.io</code> です。よって、与えられるパスは、<code>/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js</code> でなくてはなりません。もしこの例を御自分のサーバーにホストしたいとお思いでしたら、それに合わせて、ここを変更せねばなりません。これはやや混乱を招くところですが、セキュリティ上の理由から、この方法で動作する必要があるのです。</p>
</div>

<h4 id="Installing_the_service_worker" name="Installing_the_service_worker">サービスワーカーをインストールします</h4>

<p>サービスワーカーの制御下にあるいずれかのページが次にアクセスされた際には (たとえば、この例がリロードされたときには)、サービスワーカーがそのページに対してインストールされます。それが意味することは、サービスワーカーがそのページを制御し始めるだろう、ということです。これが起きると、サービスワーカーに対して <code>install</code> イベントを発火させます。サービスワーカー自体の内部には、当該インストールに応じるコードを書くことができます。</p>

<p><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js">sw.js</a> ファイル (サービスワーカー) 内の例を見てみましょう。<code>self</code> に対して <code>install</code> リスナーが登録されるのがお分かりでしょう。この <code>self</code> というキーワードは、サービスワーカー・ファイルの内部からサービスワーカーのグローバル・スコープを参照する手段です。</p>

<p><code>install</code> ハンドラーの内部では、{{domxref("ExtendableEvent.waitUntil()")}}メソッド——イベント・オブジェクト上で使えます——を用いて、当メソッドの内部のプロミスが成功して成立するまではブラウザーはサービスワーカーのインストールを完了させるべきではない、と知らせます。</p>

<p>ここは、キャッシュ API が動作しているのが見られる箇所です。応答を保存できる新規キャッシュ・オブジェクト (IndexedDB オブジェクト・ストアと似ています) を開くために、{{domxref("CacheStorage.open()")}} メソッドを使います。このプロミスは、<code>video-store</code> というキャッシュを表現する {{domxref("Cache")}} オブジェクトをともなって成立します。その後、一連の資源を取ってきて、その応答をキャッシュに追加するために、{{domxref("Cache.addAll()")}} メソッドを使います。</p>

<pre class="brush: js notranslate">self.addEventListener('install', function(e) {
 e.waitUntil(
   caches.open('video-store').then(function(cache) {
     return cache.addAll([
       '/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/',
       '/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/index.html',
       '/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/index.js',
       '/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/style.css'
     ]);
   })
 );
});</pre>

<p>さて、これで終わりです。インストールが済みました。</p>

<h4 id="Responding_to_further_requests" name="Responding_to_further_requests">さらなる要求に応答します</h4>

<p>HTML ページに対してサービスワーカーが登録されてインストールされ、関連する資産がすべてキャッシュに追加されれば、ほぼ開始準備が整っています。すべきことは、あと一つだけです。つまり、さらなるネットワーク要求に応答するための何らかのコードを書くことです。</p>

<p><code>sw.js</code> における第二のちょっとしたコードがしていることは、こうです。すなわち、サービスワーカーのグローバル・スコープにもう一つのリスナーを追加し、これにより、<code>fetch</code> イベントが生じたときにハンドラー関数を実行します。このイベントは、サービスワーカーの登録先のディレクトリ内の資産に対してブラウザーが要求を出す際には、いつでも生じます。</p>

<p>ハンドラーの内部では、要求された資産の URL をまず記録します。それから、{{domxref("FetchEvent.respondWith()")}} メソッドを使って、その要求に対するカスタム応答を提供します。</p>

<p>このブロックの内部では、{{domxref("CacheStorage.match()")}} を用いて、マッチング要求 (URL にマッチします) がいずれかのキャッシュの中に見つかるかどうかを調べます。このプロミスは、マッチが見つからなければ (訳注: 正しくは「見つかれば」?) そのマッチする応答をともなって成立し、マッチが見つからなければ <code>undefined</code> となります。</p>

<p>もしマッチが見つかれば、単純にそれをカスタム応答として返します。もしマッチが見つからなければ、代わりに、ネットワークから応答を <a href="/ja/docs/Web/API/WindowOrWorkerGlobalScope/fetch">fetch()</a> して、それを返します。</p>

<pre class="brush: js notranslate">self.addEventListener('fetch', function(e) {
  console.log(e.request.url);
  e.respondWith(
    caches.match(e.request).then(function(response) {
      return response || fetch(e.request);
    })
  );
});</pre>

<p>これで私たちの単純なサービスワーカーは終わりです。サービスワーカーを使ってできる、もっと多くのことがありますが、より詳しくは、<a href="https://serviceworke.rs/">service worker cookbook</a> を参照してください。また、<a href="https://developers.google.com/web/fundamentals/codelabs/offline/">ウェブアプリへの Service Worker とオフラインの追加</a> という記事について、著者の Paul Kinlan さんに感謝します。あの記事のおかげで、この単純な例の着想を得られました。</p>

<h4 id="Testing_the_example_offline" name="Testing_the_example_offline">この例をオフラインで試します</h4>

<p><a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/">サービスワーカー の例</a> を試すには、それが確実にインストールされるように、二、三度ロードする必要があるでしょう。それが済んだら、以下のことができます。</p>

<ul>
 <li>ネットワーク接続ケーブルを抜いてみましょう。あるいは、Wi-Fi を切ってみましょう。</li>
 <li>Firefox を使っているなら、[ファイル] &gt; [オフライン作業] を選択してください。</li>
 <li>Chrome を使っているなら、[デベロッパーツール] へ進み、 [<em>Application] &gt; [Service Workers]</em> を選び、それから、[<em>Offline] </em>のチェックボックスをチェックしてください。</li>
</ul>

<p>この例のページをもう一度リフレッシュすれば、当該ページが依然として、まさに申し分なくロードされているところを見ることになるはずです。あらゆるものがオフラインに保存されています。すなわち、ページ資産はキャッシュに保存されており、動画は IndexedDB データベースに保存されています。</p>

<h2 id="Summary" name="Summary">まとめ</h2>

<p>これで終わりです。クライアント側での保存の技術についてのこの概要を、皆さんが有用だと思ってくださったのであれば良いな、と望んでいます。</p>

<h2 id="See_also" name="See_also">あわせて参照</h2>

<ul>
 <li><a href="/ja/docs/Web/API/Web_Storage_API">Web storage API</a></li>
 <li><a href="/ja/docs/Web/API/IndexedDB_API">IndexedDB API</a></li>
 <li><a href="/ja/docs/Web/HTTP/Cookies">Cookies</a></li>
 <li><a href="/ja/docs/Web/API/Service_Worker_API">Service worker API</a></li>
</ul>

<p>{{PreviousMenu("Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</p>

<h2 id="このモジュール">このモジュール</h2>

<div>
<ul>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">Web API の紹介</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents">ドキュメントの操作</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data">サーバからのデータ取得</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Third_party_APIs">サードパーティ API</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Drawing_graphics">グラフィックの描画</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs">動画と音声の API</a></li>
 <li><a href="/ja/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage">クライアント側ストレージ</a></li>
</ul>
</div>
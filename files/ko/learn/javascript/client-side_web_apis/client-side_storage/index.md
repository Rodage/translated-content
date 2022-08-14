---
title: Client-side storage
slug: Learn/JavaScript/Client-side_web_APIs/Client-side_storage
translation_of: Learn/JavaScript/Client-side_web_APIs/Client-side_storage
---
<p>{{LearnSidebar}}</p>

<div>{{PreviousMenu("Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</div>



<p class="summary">현대 웹 브라우저들은 (사용자의 허락 하에) 사용자 컴퓨터에 웹사이트 정보를 저장할 수 있는 다양한 방법을 제공합니다. 그리고 필요한때 그 정보들을 읽어오죠. 이는 당신이 장기간 데이터를 보관할 수 있게 해주고 사이트와 웹문서를 당신이 지정한 설정에 따라 오프라인 상태에서도 사용할수 있게 해줍니다. 이 문서는 이러한 것들이 어떻게 동작하는지에 대한 기본지식들을 설명합니다.  </p>

<table class="learn-box standard-table">
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>JavaScript basics (see <a href="/en-US/docs/Learn/JavaScript/First_steps">first steps</a>, <a href="/en-US/docs/Learn/JavaScript/Building_blocks">building blocks</a>, <a href="/en-US/docs/Learn/JavaScript/Objects">JavaScript objects</a>), the <a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">basics of Client-side APIs</a></td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To learn how to use client-side storage APIs to store application data.</td>
  </tr>
 </tbody>
</table>

<h2 id="Client-side_storage">Client-side storage?</h2>

<p>우리는 다른 MDN 학습영역에서 <a href="/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview#Static_sites">정적인 사이트</a>와 <a href="/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview#Dynamic_sites">동적인 사이트</a>에 대해 이미 설명하였습니다. 현대의 대부분의 웹사이트들은 어떤 데이터베이스(서버의 저장소)를 이용하여 서버에 데이터를 저장하고, 필요한 데이터를 찾아오기 위해 <a href="/en-US/docs/Learn/Server-side">서버-사이드</a> 코드를 돌리고, 정적인 페이지 템플릿에 데이터를 삽입하고, HTML 결과물을 사용자의 브라우저에 표시될 수 있게 제공합니다 - 즉 동적입니다.</p>

<p>클라이언트-사이드 저장소는 비슷한 원리로 작동하지만, 다르게 쓰입니다. 이것은 개발자가 클라이언트 측(사용자의 컴퓨터 등)에 데이터를 저장할 수 있고 필요할 때 가져올 수 있게 해주는 자바스크립트 API로 구성되어 있습니다. 이것의 다양한 용도는 다음과 같습니다.</p>

<ul>
 <li>웹사이트에 대한 선호를 개인화하기(사용자가 선택한 커스텀 위젯, 배색, 폰트 크기로 보여주기)</li>
 <li>이전 활동 기록 저장하기(이전 세션에 담았던 장바구니 목록 저장하기, 로그인 유지하기)</li>
 <li>사이트 다운로드가 빨라지고(잠재적으로 비용이 적게 들어가고) 네트워크 연결 없이도 사용할 수 있게끔 데이터를 로컬에 저장하기</li>
 <li>오프라인 상태에서 사용할 수 있도록 웹 어플리케이션 생성 문서를 로컬에 저장하기</li>
</ul>

<p>클라이언트-사이드 저장소와 서버-사이드 저장소는 대개 함께 사용됩니다. 예를 들면, 당신은 (아마도 웹 게임이나 음악 재생 어플리케이션에서 사용할)음악 파일 여러 개를 다운받아 클라이언트-사이드 데이터베이스에 저장하고 필요할 때 재생할 수 있습니다. 사용자는 음악 파일을 한번만 다운받고, 재방문 시에는 데이터베이스에서 가져오기만 하면 됩니다.</p>

<div class="note">
<p><strong>Note</strong>: There are limits to the amount of data you can store using client-side storage APIs (possibly both per individual API and cumulatively); the exact limit varies depending on the browser and possibly based on user settings. See <a href="/en-US/docs/Web/API/IndexedDB_API/Browser_storage_limits_and_eviction_criteria">Browser storage limits and eviction criteria</a> for more information.</p>
</div>

<h3 id="Old_fashioned_cookies">Old fashioned: cookies</h3>

<p>클라이언트-사이드 저장소에 대한 개념은 오래전부터 있었습니다. 웹의 태동기 시절, 웹 사이트들은 사용자 경험(UX)을 개인화하는 정보들을 저장하기 위해 <a href="/en-US/docs/Web/HTTP/Cookies">cookies</a>를 사용했습니다. 그것들이 웹에서 보편적으로 사용된 클라이언트-사이드 저장소의 제일 오래된 형태입니다.</p>

<p>오늘날에는 클라이언트 사이드에 데이터를 저장하는 더 쉬운 방법이 있지만, 이 문서에서 cookies를 사용하는 법을 가르쳐 주지는 않습니다. 그러나, 이것이 현대의 웹에서 cookies가 완벽하게 쓸모없다는 것을 뜻하지는 않습니다. cookies는 세션 ID나 access token 같은 사용자 상태와 개인화에 관련된 정보를 저장하는데 여전히 보편적으로 쓰입니다. cookies에 대한 더 자세한 정보는 우리의 <a href="/en-US/docs/Web/HTTP/Cookies">Using HTTP cookies</a> 문서를 참고하세요.</p>

<h3 id="New_school_Web_Storage_and_IndexedDB">New school: Web Storage and IndexedDB</h3>

<p>현대의 브라우저들은 클라이언트-사이드 데이터를 저장하는 데에 cookies보다 더 쉽고 더 효율적인 API들을 제공합니다.</p>

<ul>
 <li><a href="/en-US/docs/Web/API/Web_Storage_API">Web Storage API</a>는 이름과 대응되는 값으로 이루어진 더 작은 데이터를 저장하고 가져오는 매우 간단한 기능을 제공합니다. 이것은 사용자 이름과 로그인 여부, 스크린 배경색을 어떤 색으로 할지 등등 같은 간단한 데이터를 저장할 필요가 있을 때 유용합니다. </li>
 <li><a href="/en-US/docs/Web/API/IndexedDB_API">IndexedDB API</a>는 복잡한 데이터를 저장할 수 있는 완벽한 데이터베이스를 브라우저에서 제공할 수 있게 해줍니다. 이것은 소비자 기록의 복잡한 데이터셋부터 오디오나 비디오 파일같은 더욱 복잡한 데이터까지 저장하는 데에 쓰일 수 있습니다.</li>
</ul>

<p>밑에서 이런 API들을 더 배울 수 있습니다.</p>

<h3 id="The_future_Cache_API">The future: Cache API</h3>

<p>몇몇 현대적인 브라우저들은 새로운 {{domxref("Cache")}} API를 제공합니다. 이 API는 특정 requests에 대한 HTTP responses를 저장하기 위해 디자인되었고, 웹사이트가 차후에 네트워크 연결 없이도 사용될 수 있도록 사이트 정보를 저장하는 등의 일을 하는데 유용합니다. Cache는 일반적으로 <a href="/en-US/docs/Web/API/Service_Worker_API">Service Worker API</a>와 함께 사용되지만, 꼭 그럴 필요는 없습니다.</p>

<p>Cache와 Service Workers의 사용은 심화 주제이므로 이 문서에서는 아래의 <a href="#offline_asset_storage">Offline asset storage</a> 섹션에서 보여주는 것 이상으로 깊게 다루지는 않을 것입니다.</p>

<h2 id="Storing_simple_data_—_web_storage">Storing simple data — web storage</h2>

<p>The <a href="/en-US/docs/Web/API/Web_Storage_API">Web Storage API</a> is very easy to use — you store simple name/value pairs of data (limited to strings, numbers, etc.) and retrieve these values when needed.</p>

<h3 id="Basic_syntax">Basic syntax</h3>

<p>Let's show you how:</p>

<ol>
 <li>
  <p>First, go to our <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/web-storage/index.html">web storage blank template</a> on GitHub (open this in a new tab).</p>
 </li>
 <li>
  <p>Open the JavaScript console of your browser's developer tools.</p>
 </li>
 <li>
  <p>All of your web storage data is contained within two object-like structures inside the browser: {{domxref("Window.sessionStorage", "sessionStorage")}} and {{domxref("Window.localStorage", "localStorage")}}. The first one persists data for as long as the browser is open (the data is lost when the browser is closed) and the second one persists data even after the browser is closed and then opened again. We'll use the second one in this article as it is generally more useful.</p>

  <p>The {{domxref("Storage.setItem()")}} method allows you to save a data item in storage — it takes two parameters: the name of the item, and its value. Try typing this into your JavaScript console (change the value to your own name, if you wish!):</p>

  <pre class="brush: js notranslate">localStorage.setItem('name','Chris');</pre>
 </li>
 <li>
  <p>The {{domxref("Storage.getItem()")}} method takes one parameter — the name of a data item you want to retrieve — and returns the item's value. Now type these lines into your JavaScript console:</p>

  <pre class="brush: js notranslate">var myName = localStorage.getItem('name');
myName</pre>

  <p>Upon typing in the second line, you should see that the <code>myName</code> variable now contains the value of the <code>name</code> data item.</p>
 </li>
 <li>
  <p>The {{domxref("Storage.removeItem()")}} method takes one parameter — the name of a data item you want to remove — and removes that item out of web storage. Type the following lines into your JavaScript console:</p>

  <pre class="brush: js notranslate">localStorage.removeItem('name');
var myName = localStorage.getItem('name');
myName</pre>

  <p>The third line should now return <code>null</code> — the <code>name</code> item no longer exists in the web storage.</p>
 </li>
</ol>

<h3 id="The_data_persists!">The data persists!</h3>

<p>One key feature of web storage is that the data persists between page loads (and even when the browser is shut down, in the case of <code>localStorage</code>). Let's look at this in action.</p>

<ol>
 <li>
  <p>Open our web storage blank template again, but this time in a different browser to the one you've got this tutorial open in! This will make it easier to deal with.</p>
 </li>
 <li>
  <p>Type these lines into the browser's JavaScript console:</p>

  <pre class="brush: js notranslate">localStorage.setItem('name','Chris');
var myName = localStorage.getItem('name');
myName</pre>

  <p>You should see the name item returned.</p>
 </li>
 <li>
  <p>Now close down the browser and open it up again.</p>
 </li>
 <li>
  <p>Enter the following lines again:</p>

  <pre class="brush: js notranslate">var myName = localStorage.getItem('name');
myName</pre>

  <p>You should see that the value is still available, even though the browser has been closed and then opened again.</p>
 </li>
</ol>

<h3 id="Separate_storage_for_each_domain">Separate storage for each domain</h3>

<p>There is a separate data store for each domain (each separate web address loaded in the browser). You will see that if you load two websites (say google.com and amazon.com) and try storing an item on one website, it won't be available to the other website.</p>

<p>This makes sense — you can imagine the security issues that would arise if websites could see each other's data!</p>

<h3 id="A_more_involved_example">A more involved example</h3>

<p>Let's apply this new-found knowledge by writing a simple working example to give you an idea of how web storage can be used. Our example will allow you enter a name, after which the page will update to give you a personalized greeting. This state will also persist across page/browser reloads, because the name is stored in web storage.</p>

<p>You can find the example HTML at <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/web-storage/personal-greeting.html">personal-greeting.html</a> — this contains a simple website with a header, content, and footer, and a form for entering your name.</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15735/web-storage-demo.png" style="display: block; margin: 0 auto;"></p>

<p>Let's build up the example, so you can understand how it works.</p>

<ol>
 <li>
  <p>First, make a local copy of our <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/web-storage/personal-greeting.html">personal-greeting.html</a> file in a new directory on your computer.</p>
 </li>
 <li>
  <p>Next, note how our HTML references a JavaScript file called <code>index.js</code> (see line 40). We need to create this and write our JavaScript code into it. Create an <code>index.js</code> file in the same directory as your HTML file. </p>
 </li>
 <li>
  <p>We'll start off by creating references to all the HTML features we need to manipulate in this example — we'll create them all as constants, as these references do not need to change in the lifecycle of the app. Add the following lines to your JavaScript file:</p>

  <pre class="brush: js notranslate">// create needed constants
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
  <p>Next up, we need to include a small event listener to stop the form from actually submitting itself when the submit button is pressed, as this is not the behavior we want. Add this snippet below your previous code:</p>

  <pre class="brush: js notranslate">// Stop the form from submitting when a button is pressed
form.addEventListener('submit', function(e) {
  e.preventDefault();
});</pre>
 </li>
 <li>
  <p>Now we need to add an event listener, the handler function of which will run when the "Say hello" button is clicked. The comments explain in detail what each bit does, but in essence here we are taking the name the user has entered into the text input box and saving it in web storage using <code>setItem()</code>, then running a function called <code>nameDisplayCheck()</code> that will handle updating the actual website text. Add this to the bottom of your code:</p>

  <pre class="brush: js notranslate">// run function when the 'Say hello' button is clicked
submitBtn.addEventListener('click', function() {
  // store the entered name in web storage
  localStorage.setItem('name', nameInput.value);
  // run nameDisplayCheck() to sort out displaying the
  // personalized greetings and updating the form display
  nameDisplayCheck();
});</pre>
 </li>
 <li>
  <p>At this point we also need an event handler to run a function when the "Forget" button is clicked — this is only displayed after the "Say hello" button has been clicked (the two form states toggle back and forth). In  this function we remove the <code>name</code> item from web storage using <code>removeItem()</code>, then again run <code>nameDisplayCheck()</code> to update the display. Add this to the bottom:</p>

  <pre class="brush: js notranslate">// run function when the 'Forget' button is clicked
forgetBtn.addEventListener('click', function() {
  // Remove the stored name from web storage
  localStorage.removeItem('name');
  // run nameDisplayCheck() to sort out displaying the
  // generic greeting again and updating the form display
  nameDisplayCheck();
});</pre>
 </li>
 <li>
  <p>It is now time to define the <code>nameDisplayCheck()</code> function itself. Here we check whether the name item has been stored in web storage by using <code>localStorage.getItem('name')</code> as a conditional test. If it has been stored, this call will evaluate to <code>true</code>; if not, it will be <code>false</code>. If it is <code>true</code>, we display a personalized greeting, display the "forget" part of the form, and hide the "Say hello" part of the form. If it is <code>false</code>, we display a generic greeting and do the opposite. Again, put the following code at the bottom:</p>

  <pre class="brush: js notranslate">// define the nameDisplayCheck() function
function nameDisplayCheck() {
  // check whether the 'name' data item is stored in web Storage
  if(localStorage.getItem('name')) {
    // If it is, display personalized greeting
    let name = localStorage.getItem('name');
    h1.textContent = 'Welcome, ' + name;
    personalGreeting.textContent = 'Welcome to our website, ' + name + '! We hope you have fun while you are here.';
    // hide the 'remember' part of the form and show the 'forget' part
    forgetDiv.style.display = 'block';
    rememberDiv.style.display = 'none';
  } else {
    // if not, display generic greeting
    h1.textContent = 'Welcome to our website ';
    personalGreeting.textContent = 'Welcome to our website. We hope you have fun while you are here.';
    // hide the 'forget' part of the form and show the 'remember' part
    forgetDiv.style.display = 'none';
    rememberDiv.style.display = 'block';
  }
}</pre>
 </li>
 <li>
  <p>Last but not least, we need to run the <code>nameDisplayCheck()</code> function every time the page is loaded. If we don't do this, then the personalized greeting will not persist across page reloads. Add the following to the bottom of your code:</p>

  <pre class="brush: js notranslate">document.body.onload = nameDisplayCheck;</pre>
 </li>
</ol>

<p>Your example is finished — well done! All that remains now is to save your code and test your HTML page in a browser. You can see our <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/web-storage/personal-greeting.html">finished version running live here</a>.</p>

<div class="note">
<p><strong>Note</strong>: There is another, slightly more complex example to explore at <a href="/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API">Using the Web Storage API</a>.</p>
</div>

<div class="note">
<p><strong>Note</strong>: In the line <code>&lt;script src="index.js" defer&gt;&lt;/script&gt;</code> of the source for our finished version, the <code>defer</code> attribute specifies that the contents of the {{htmlelement("script")}} element will not execute until the page has finished loading.</p>
</div>

<h2 id="Storing_complex_data_—_IndexedDB">Storing complex data — IndexedDB</h2>

<p>The <a href="/en-US/docs/Web/API/IndexedDB_API">IndexedDB API</a> (sometimes abbreviated IDB) is a complete database system available in the browser in which you can store complex related data, the types of which aren't limited to simple values like strings or numbers. You can store videos, images, and pretty much anything else in an IndexedDB instance.</p>

<p>However, this does come at a cost: IndexedDB is much more complex to use than the Web Storage API. In this section, we'll really only scratch the surface of what it is capable of, but we will give you enough to get started.</p>

<h3 id="Working_through_a_note_storage_example">Working through a note storage example</h3>

<p>Here we'll run you through an example that allows you to store notes in your browser and view and delete them whenever you like, getting you to build it up for yourself and explaining the most fundamental parts of IDB as we go along.</p>

<p>The app looks something like this:</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15744/idb-demo.png" style="border-style: solid; border-width: 1px; display: block; margin: 0px auto;"></p>

<p>Each note has a title and some body text, each individually editable. The JavaScript code we'll go through below has detailed comments to help you understand what's going on.</p>

<h3 id="Getting_started">Getting started</h3>

<ol>
 <li>First of all, make local copies of our <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index.html">index.html</a></code>, <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/style.css">style.css</a></code>, and <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index-start.js">index-start.js</a></code> files into a new directory on your local machine.</li>
 <li>Have a look at the files. You'll see that the HTML is pretty simple: a web site with a header and footer, as well as a main content area that contains a place to display notes, and a form for entering new notes into the database. The CSS provides some simple styling to make it clearer what is going on. The JavaScript file contains five declared constants containing references to the {{htmlelement("ul")}} element the notes will be displayed in, the title and body {{htmlelement("input")}} elements, the {{htmlelement("form")}} itself, and the {{htmlelement("button")}}.</li>
 <li>Rename your JavaScript file to <code>index.js</code>. You are now ready to start adding code to it.</li>
</ol>

<h3 id="Database_initial_set_up">Database initial set up</h3>

<p>Now let's look at what we have to do in the first place, to actually set up a database.</p>

<ol>
 <li>
  <p>Below the constant declarations, add the following lines:</p>

  <pre class="brush: js notranslate">// Create an instance of a db object for us to store the open database in
let db;</pre>

  <p>Here we are declaring a variable called <code>db</code> — this will later be used to store an object representing our database. We will use this in a few places, so we've declared it globally here to make things easier.</p>
 </li>
 <li>
  <p>Next, add the following to the bottom of your code:</p>

  <pre class="brush: js notranslate">window.onload = function() {

};</pre>

  <p>We will write all of our subsequent code inside this <code>window.onload</code> event handler function, called when the window's {{event("load")}} event fires, to make sure we don't try to use IndexedDB functionality before the app has completely finished loading (it could fail if we don't).</p>
 </li>
 <li>
  <p>Inside the <code>window.onload</code> handler, add the following:</p>

  <pre class="brush: js notranslate">// Open our database; it is created if it doesn't already exist
// (see onupgradeneeded below)
let request = window.indexedDB.open('notes_db', 1);</pre>

  <p>This line creates a <code>request</code> to open version <code>1</code> of a database called <code>notes_db</code>. If this doesn't already exist, it will be created for you by subsequent code. You will see this request pattern used very often throughout IndexedDB. Database operations take time. You don't want to hang the browser while you wait for the results, so database operations are {{Glossary("asynchronous")}}, meaning that instead of happening immediately, they will happen at some point in the future, and you get notified when they're done.</p>

  <p>To handle this in IndexedDB, you create a request object (which can be called anything you like — we called it <code>request</code> so it is obvious what it is for). You then use event handlers to run code when the request completes, fails, etc., which you'll see in use below.</p>

  <div class="note">
  <p><strong>Note</strong>: The version number is important. If you want to upgrade your database (for example, by changing the table structure), you have to run your code again with an increased version number, different schema specified inside the <code>onupgradeneeded</code> handler (see below), etc. We won't cover upgrading databases in this simple tutorial.</p>
  </div>
 </li>
 <li>
  <p>Now add the following event handlers just below your previous addition — again inside the <code>window.onload</code> handler:</p>

  <pre class="brush: js notranslate">// onerror handler signifies that the database didn't open successfully
request.onerror = function() {
  console.log('Database failed to open');
};

// onsuccess handler signifies that the database opened successfully
request.onsuccess = function() {
  console.log('Database opened successfully');

  // Store the opened database object in the db variable. This is used a lot below
  db = request.result;

  // Run the displayData() function to display the notes already in the IDB
  displayData();
};</pre>

  <p>The {{domxref("IDBRequest.onerror", "request.onerror")}} handler will run if the system comes back saying that the request failed. This allows you to respond to this problem. In our simple example, we just print a message to the JavaScript console.</p>

  <p>The {{domxref("IDBRequest.onsuccess", "request.onsuccess")}} handler on the other hand will run if the request returns successfully, meaning the database was successfully opened. If this is the case, an object representing the opened database becomes available in the {{domxref("IDBRequest.result", "request.result")}} property, allowing us to manipulate the database. We store this in the <code>db</code> variable we created earlier for later use. We also run a custom function called <code>displayData()</code>, which displays the data in the database inside the {{HTMLElement("ul")}}. We run it now so that the notes already in the database are displayed as soon as the page loads. You'll see this defined later on.</p>
 </li>
 <li>
  <p>Finally for this section, we'll add probably the most important event handler for setting up the database: {{domxref("IDBOpenDBRequest.onupgradeneeded", "request.onupdateneeded")}}. This handler runs if the database has not already been set up, or if the database is opened with a bigger version number than the existing stored database (when performing an upgrade). Add the following code, below your previous handler:</p>

  <pre class="brush: js notranslate">// Setup the database tables if this has not already been done
request.onupgradeneeded = function(e) {
  // Grab a reference to the opened database
  let db = e.target.result;

  // Create an objectStore to store our notes in (basically like a single table)
  // including a auto-incrementing key
  let objectStore = db.createObjectStore('notes_os', { keyPath: 'id', autoIncrement:true });

  // Define what data items the objectStore will contain
  objectStore.createIndex('title', 'title', { unique: false });
  objectStore.createIndex('body', 'body', { unique: false });

  console.log('Database setup complete');
};</pre>

  <p>This is where we define the schema (structure) of our database; that is, the set of columns (or fields) it contains. Here we first grab a reference to the existing database from <code>e.target.result</code> (the event target's <code>result</code> property), which is the <code>request</code> object. This is equivalent to the line <code>db = request.result;</code> inside the <code>onsuccess</code> handler, but we need to do this separately here because the <code>onupgradeneeded</code> handler (if needed) will run before the <code>onsuccess</code> handler, meaning that the <code>db</code> value wouldn't be available if we didn't do this.</p>

  <p>We then use {{domxref("IDBDatabase.createObjectStore()")}} to create a new object store inside our opened database called <code>notes_os</code>. This is equivalent to a single table in a conventional database system. We've given it the name notes, and also specified an <code>autoIncrement</code> key field called <code>id</code> — in each new record this will automatically be given an incremented value — the developer doesn't need to set this explicitly. Being the key, the <code>id</code> field will be used to uniquely identify records, such as when deleting or displaying a record.</p>

  <p>We also create two other indexes (fields) using the {{domxref("IDBObjectStore.createIndex()")}} method: <code>title</code> (which will contain a title for each note), and <code>body</code> (which will contain the body text of the note).</p>
 </li>
</ol>

<p>So with this simple database schema set up, when we start adding records to the database; each one will be represented as an object along these lines:</p>

<pre class="brush: js notranslate"><span class="message-body-wrapper"><span class="message-flex-body"><span class="devtools-monospace message-body"><span class="objectBox objectBox-object"><span class="objectLeftBrace">{
  </span><span class="nodeName">title</span><span class="objectEqual">: </span><span class="objectBox objectBox-string">"Buy milk"</span>,
  <span class="nodeName">body</span><span class="objectEqual">: </span><span class="objectBox objectBox-string">"Need both cows milk and soya."</span>,
  <span class="nodeName">id</span><span class="objectEqual">: </span><span class="objectBox objectBox-number">8</span></span></span></span></span><span class="message-body-wrapper"><span class="message-flex-body"><span class="devtools-monospace message-body"><span class="objectBox objectBox-object"><span class="objectRightBrace">
}</span></span></span></span></span></pre>

<h3 id="Adding_data_to_the_database">Adding data to the database</h3>

<p>Now let's look at how we can add records to the database. This will be done using the form on our page.</p>

<p>Below your previous event handler (but still inside the <code>window.onload</code> handler), add the following line, which sets up an <code>onsubmit</code> handler that runs a function called <code>addData()</code> when the form is submitted (when the submit {{htmlelement("button")}} is pressed leading to a successful form submission):</p>

<pre class="brush: js notranslate">// Create an onsubmit handler so that when the form is submitted the addData() function is run
form.onsubmit = addData;</pre>

<p>Now let's define the <code>addData()</code> function. Add this below your previous line:</p>

<pre class="brush: js notranslate">// Define the addData() function
function addData(e) {
  // prevent default - we don't want the form to submit in the conventional way
  e.preventDefault();

  // grab the values entered into the form fields and store them in an object ready for being inserted into the DB
  let newItem = { title: titleInput.value, body: bodyInput.value };

  // open a read/write db transaction, ready for adding the data
  let transaction = db.transaction(['notes_os'], 'readwrite');

  // call an object store that's already been added to the database
  let objectStore = transaction.objectStore('notes_os');

  // Make a request to add our newItem object to the object store
  var request = objectStore.add(newItem);
  request.onsuccess = function() {
    // Clear the form, ready for adding the next entry
    titleInput.value = '';
    bodyInput.value = '';
  };

  // Report on the success of the transaction completing, when everything is done
  transaction.oncomplete = function() {
    console.log('Transaction completed: database modification finished.');

    // update the display of data to show the newly added item, by running displayData() again.
    displayData();
  };

  transaction.onerror = function() {
    console.log('Transaction not opened due to error');
  };
}</pre>

<p>This is quite complex; breaking it down, we:</p>

<ul>
 <li>Run {{domxref("Event.preventDefault()")}} on the event object to stop the form actually submitting in the conventional manner (this would cause a page refresh and spoil the experience).</li>
 <li>Create an object representing a record to enter into the database, populating it with values from the form inputs. note that we don't have to explicitly include an <code>id</code> value — as we explained earlier, this is auto-populated.</li>
 <li>Open a <code>readwrite</code> transaction against the <code>notes_os</code> object store using the {{domxref("IDBDatabase.transaction()")}} method. This transaction object allows us to access the object store so we can do something to it, e.g. add a new record.</li>
 <li>Access the object store using the {{domxref("IDBTransaction.objectStore()")}} method, saving the result in the <code>objectStore</code> variable.</li>
 <li>Add the new record to the database using {{domxref("IDBObjectStore.add()")}}. This creates a request object, in the same fashion as we've seen before.</li>
 <li>Add a bunch of event handlers to the <code>request</code> and the <code>transaction</code> to run code at critical points in the lifecycle. Once the request has succeeded, we clear the form inputs ready for entering the next note. Once the transaction has completed, we run the <code>displayData()</code> function again to update the display of notes on the page.</li>
</ul>

<h3 id="Displaying_the_data">Displaying the data</h3>

<p>We've referenced <code>displayData()</code> twice in our code already, so we'd probably better define it. Add this to your code, below the previous function definition:</p>

<pre class="brush: js notranslate">// Define the displayData() function
function displayData() {
  // Here we empty the contents of the list element each time the display is updated
  // If you didn't do this, you'd get duplicates listed each time a new note is added
  while (list.firstChild) {
    list.removeChild(list.firstChild);
  }

  // Open our object store and then get a cursor - which iterates through all the
  // different data items in the store
  let objectStore = db.transaction('notes_os').objectStore('notes_os');
  objectStore.openCursor().onsuccess = function(e) {
    // Get a reference to the cursor
    let cursor = e.target.result;

    // If there is still another data item to iterate through, keep running this code
    if(cursor) {
      // Create a list item, h3, and p to put each data item inside when displaying it
      // structure the HTML fragment, and append it inside the list
      let listItem = document.createElement('li');
      let h3 = document.createElement('h3');
      let para = document.createElement('p');

      listItem.appendChild(h3);
      listItem.appendChild(para);
      list.appendChild(listItem);

      // Put the data from the cursor inside the h3 and para
      h3.textContent = cursor.value.title;
      para.textContent = cursor.value.body;

      // Store the ID of the data item inside an attribute on the listItem, so we know
      // which item it corresponds to. This will be useful later when we want to delete items
      listItem.setAttribute('data-note-id', cursor.value.id);

      // Create a button and place it inside each listItem
      let deleteBtn = document.createElement('button');
      listItem.appendChild(deleteBtn);
      deleteBtn.textContent = 'Delete';

      // Set an event handler so that when the button is clicked, the deleteItem()
      // function is run
      deleteBtn.onclick = deleteItem;

      // Iterate to the next item in the cursor
      cursor.continue();
    } else {
      // Again, if list item is empty, display a 'No notes stored' message
      if(!list.firstChild) {
        let listItem = document.createElement('li');
        listItem.textContent = 'No notes stored.';
        list.appendChild(listItem);
      }
      // if there are no more cursor items to iterate through, say so
      console.log('Notes all displayed');
    }
  };
}</pre>

<p>Again, let's break this down:</p>

<ul>
 <li>First we empty out the {{htmlelement("ul")}} element's content, before then filling it with the updated content. If you didn't do this, you'd end up with a huge list of duplicated content being added to with each update.</li>
 <li>Next, we get a reference to the <code>notes_os</code> object store using {{domxref("IDBDatabase.transaction()")}} and {{domxref("IDBTransaction.objectStore()")}} like we did in <code>addData()</code>, except here we are chaining them together in one line.</li>
 <li>The next step is to use {{domxref("IDBObjectStore.openCursor()")}} method to open a request for a cursor — this is a construct that can be used to iterate over the records in an object store. We chain an <code>onsuccess</code> handler on to the end of this line to make the code more concise — when the cursor is successfully returned, the handler is run.</li>
 <li>We get a reference to the cursor itself (an {{domxref("IDBCursor")}} object) using let <code>cursor = e.target.result</code>.</li>
 <li>Next, we check to see if the cursor contains a record from the datastore (<code>if(cursor){ ... }</code>) — if so, we create a DOM fragment, populate it with the data from the record, and insert it into the page (inside the <code>&lt;ul&gt;</code> element). We also include a delete button that, when clicked, will delete that note by running the <code>deleteItem()</code> function, which we will look at in the next section.</li>
 <li>At the end of the <code>if</code> block, we use the {{domxref("IDBCursor.continue()")}} method to advance the cursor to the next record in the datastore, and run the content of the <code>if</code> block again. If there is another record to iterate to, this causes it to be inserted into the page, and then <code>continue()</code> is run again, and so on.</li>
 <li>When there are no more records to iterate over, <code>cursor</code> will return <code>undefined</code>, and therefore the <code>else</code> block will run instead of the <code>if</code> block. This block checks whether any notes were inserted into the <code>&lt;ul&gt;</code> — if not, it inserts a message to say no note was stored.</li>
</ul>

<h3 id="Deleting_a_note">Deleting a note</h3>

<p>As stated above, when a note's delete button is pressed, the note is deleted. This is achieved by the <code>deleteItem()</code> function, which looks like so:</p>

<pre class="brush: js notranslate">// Define the deleteItem() function
function deleteItem(e) {
  // retrieve the name of the task we want to delete. We need
  // to convert it to a number before trying it use it with IDB; IDB key
  // values are type-sensitive.
  let noteId = Number(e.target.parentNode.getAttribute('data-note-id'));

  // open a database transaction and delete the task, finding it using the id we retrieved above
  let transaction = db.transaction(['notes_os'], 'readwrite');
  let objectStore = transaction.objectStore('notes_os');
  let request = objectStore.delete(noteId);

  // report that the data item has been deleted
  transaction.oncomplete = function() {
    // delete the parent of the button
    // which is the list item, so it is no longer displayed
    e.target.parentNode.parentNode.removeChild(e.target.parentNode);
    console.log('Note ' + noteId + ' deleted.');

    // Again, if list item is empty, display a 'No notes stored' message
    if(!list.firstChild) {
      let listItem = document.createElement('li');
      listItem.textContent = 'No notes stored.';
      list.appendChild(listItem);
    }
  };
}</pre>

<ul>
 <li>The first part of this could use some explaining — we retrieve the ID of the record to be deleted using <code>Number(e.target.parentNode.getAttribute('data-note-id'))</code> — recall that the ID of the record was saved in a <code>data-note-id</code> attribute on the <code>&lt;li&gt;</code> when it was first displayed. We do however need to pass the attribute through the global built-in <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number">Number()</a></code> object as it is of datatype string, and therefore wouldn't be recognized by the database, which expects a number.</li>
 <li>We then get a reference to the object store using the same pattern we've seen previously, and use the {{domxref("IDBObjectStore.delete()")}} method to delete the record from the database, passing it the ID.</li>
 <li>When the database transaction is complete, we delete the note's <code>&lt;li&gt;</code> from the DOM, and again do the check to see if the <code>&lt;ul&gt;</code> is now empty, inserting a note as appropriate.</li>
</ul>

<p>So that's it! Your example should now work.</p>

<p>If you are having trouble with it, feel free to <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/indexeddb/notes/">check it against our live example</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/indexeddb/notes/index.js">source code</a> also).</p>

<h3 id="Storing_complex_data_via_IndexedDB">Storing complex data via IndexedDB</h3>

<p>As we mentioned above, IndexedDB can be used to store more than just simple text strings. You can store just about anything you want, including complex objects such as video or image blobs. And it isn't much more difficult to achieve than any other type of data.</p>

<p>To demonstrate how to do it, we've written another example called <a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/client-side-storage/indexeddb/video-store">IndexedDB video store</a> (see it <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/indexeddb/video-store/">running live here also</a>). When you first run the example, it downloads all the videos from the network, stores them in an IndexedDB database, and then displays the videos in the UI inside {{htmlelement("video")}} elements. The second time you run it, it finds the videos in the database and gets them from there instead before displaying them — this makes subsequent loads much quicker and less bandwidth-hungry.</p>

<p>Let's walk through the most interesting parts of the example. We won't look at it all — a lot of it is similar to the previous example, and the code is well-commented.</p>

<ol>
 <li>
  <p>For this simple example, we've stored the names of the videos to fetch in an array of objects:</p>

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
  <p>To start with, once the database is successfully opened we run an <code>init()</code> function. This loops through the different video names, trying to load a record identified by each name from the <code>videos</code> database.</p>

  <p>If each video is found in the database (easily checked by seeing whether <code>request.result</code> evaluates to <code>true</code> — if the record is not present, it will be <code>undefined</code>), its video files (stored as blobs) and the video name are passed straight to the <code>displayVideo()</code> function to place them in the UI. If not, the video name is passed to the <code>fetchVideoFromNetwork()</code> function to ... you guessed it — fetch the video from the network.</p>

  <pre class="brush: js notranslate">function init() {
  // Loop through the video names one by one
  for(let i = 0; i &lt; videos.length; i++) {
    // Open transaction, get object store, and get() each video by name
    let objectStore = db.transaction('videos_os').objectStore('videos_os');
    let request = objectStore.get(videos[i].name);
    request.onsuccess = function() {
      // If the result exists in the database (is not undefined)
      if(request.result) {
        // Grab the videos from IDB and display them using displayVideo()
        console.log('taking videos from IDB');
        displayVideo(request.result.mp4, request.result.webm, request.result.name);
      } else {
        // Fetch the videos from the network
        fetchVideoFromNetwork(videos[i]);
      }
    };
  }
}</pre>
 </li>
 <li>
  <p>The following snippet is taken from inside <code>fetchVideoFromNetwork()</code> — here we fetch MP4 and WebM versions of the video using two separate {{domxref("fetch()", "WindowOrWorkerGlobalScope.fetch()")}} requests. We then use the {{domxref("blob()", "Body.blob()")}} method to extract each response's body as a blob, giving us an object representation of the videos that can be stored and displayed later on.</p>

  <p>We have a problem here though — these two requests are both asynchronous, but we only want to try to display or store the video when both promises have fulfilled. Fortunately there is a built-in method that handles such a problem — {{jsxref("Promise.all()")}}. This takes one argument — references to all the individual promises you want to check for fulfillment placed in an array — and is itself promise-based.</p>

  <p>When all those promises have fulfilled, the <code>all()</code> promise fulfills with an array containing all the individual fulfillment values. Inside the <code>all()</code> block, you can see that we then call the <code>displayVideo()</code> function like we did before to display the videos in the UI, then we also call the <code>storeVideo()</code> function to store those videos inside the database.</p>

  <pre class="brush: js notranslate">let mp4Blob = fetch('videos/' + video.name + '.mp4').then(response =&gt;
  response.blob()
);
let webmBlob = fetch('videos/' + video.name + '.webm').then(response =&gt;
  response.blob()
);

// Only run the next code when both promises have fulfilled
Promise.all([mp4Blob, webmBlob]).then(function(values) {
  // display the video fetched from the network with displayVideo()
  displayVideo(values[0], values[1], video.name);
  // store it in the IDB using storeVideo()
  storeVideo(values[0], values[1], video.name);
});</pre>
 </li>
 <li>
  <p>Let's look at <code>storeVideo()</code> first. This is very similar to the pattern you saw in the previous example for adding data to the database — we open a <code>readwrite</code> transaction and get a reference to our <code>videos_os</code> object store, create an object representing the record to add to the database, then simply add it using {{domxref("IDBObjectStore.add()")}}.</p>

  <pre class="brush: js notranslate">function storeVideo(mp4Blob, webmBlob, name) {
  // Open transaction, get object store; make it a readwrite so we can write to the IDB
  let objectStore = db.transaction(['videos_os'], 'readwrite').objectStore('videos_os');
  // Create a record to add to the IDB
  let record = {
    mp4 : mp4Blob,
    webm : webmBlob,
    name : name
  }

  // Add the record to the IDB using add()
  let request = objectStore.add(record);

  ...

};</pre>
 </li>
 <li>
  <p>Last but not least, we have <code>displayVideo()</code>, which creates the DOM elements needed to insert the video in the UI and then appends them to the page. The most interesting parts of this are those shown below — to actually display our video blobs in a <code>&lt;video&gt;</code> element, we need to create object URLs (internal URLs that point to the video blobs stored in memory) using the {{domxref("URL.createObjectURL()")}} method. Once that is done, we can set the object URLs to be the values of our {{htmlelement("source")}} element's <code>src</code> attributes, and it works fine.</p>

  <pre class="brush: js notranslate">function displayVideo(mp4Blob, webmBlob, title) {
  // Create object URLs out of the blobs
  let mp4URL = URL.createObjectURL(mp4Blob);
  let webmURL = URL.createObjectURL(webmBlob);

  ...

  let video = document.createElement('video');
  video.controls = true;
  let source1 = document.createElement('source');
  source1.src = mp4URL;
  source1.type = 'video/mp4';
  let source2 = document.createElement('source');
  source2.src = webmURL;
  source2.type = 'video/webm';

  ...
}</pre>
 </li>
</ol>

<h2 id="Offline_asset_storage">Offline asset storage</h2>

<p>The above example already shows how to create an app that will store large assets in an IndexedDB database, avoiding the need to download them more than once. This is already a great improvement to the user experience, but there is still one thing missing — the main HTML, CSS, and JavaScript files still need to downloaded each time the site is accessed, meaning that it won't work when there is no network connection.</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/15759/ff-offline.png" style="border-style: solid; border-width: 1px; display: block; height: 307px; margin: 0px auto; width: 765px;"></p>

<p>This is where <a href="/en-US/docs/Web/API/Service_Worker_API">Service workers</a> and the closely-related <a href="/en-US/docs/Web/API/Cache">Cache API</a> come in.</p>

<p>A service worker is a JavaScript file that, simply put, is registered against a particular origin (web site, or part of a web site at a certain domain) when it is accessed by a browser. When registered, it can control pages available at that origin. It does this by sitting between a loaded page and the network and intercepting network requests aimed at that origin.</p>

<p>When it intercepts a request, it can do anything you wish to it (see <a href="/en-US/docs/Web/API/Service_Worker_API#Other_use_case_ideas">use case ideas</a>), but the classic example is saving the network responses offline and then providing those in response to a request instead of the responses from the network. In effect, it allows you to make a web site work completely offline.</p>

<p>The Cache API is a another client-side storage mechanism, with a bit of a difference — it is designed to save HTTP responses, and so works very well with service workers.</p>

<div class="note">
<p><strong>Note</strong>: Service workers and Cache are supported in most modern browsers now. At the time of writing, Safari was still busy implementing it, but it should be there soon.</p>
</div>

<h3 id="A_service_worker_example">A service worker example</h3>

<p>Let's look at an example, to give you a bit of an idea of what this might look like. We have created another version of the video store example we saw in the previous section — this functions identically, except that it also saves the HTML, CSS, and JavaScript in the Cache API via a service worker, allowing the example to run offline!</p>

<p>See <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/">IndexedDB video store with service worker running live</a>, and also <a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/client-side-storage/cache-sw/video-store-offline">see the source code</a>.</p>

<h4 id="Registering_the_service_worker">Registering the service worker</h4>

<p>The first thing to note is that there's an extra bit of code placed in the main JavaScript file (see <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/cache-sw/video-store-offline/index.js">index.js</a>). First we do a feature detection test to see if the <code>serviceWorker</code> member is available in the {{domxref("Navigator")}} object. If this returns true, then we know that at least the basics of service workers are supported. Inside here we use the {{domxref("ServiceWorkerContainer.register()")}} method to register a service worker contained in the <code>sw.js</code> file against the origin it resides at, so it can control pages in the same directory as it, or subdirectories. When its promise fulfills, the service worker is deemed registered.</p>

<pre class="brush: js notranslate">  // Register service worker to control making site work offline

  if('serviceWorker' in navigator) {
    navigator.serviceWorker
             .register('/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js')
             .then(function() { console.log('Service Worker Registered'); });
  }</pre>

<div class="note">
<p><strong>Note</strong>: The given path to the <code>sw.js</code> file is relative to the site origin, not the JavaScript file that contains the code. The service worker is at <code>https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js</code>. The origin is <code>https://mdn.github.io</code>, and therefore the given path has to be <code>/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js</code>. If you wanted to host this example on your own server, you'd have to change this accordingly. This is rather confusing, but it has to work this way for security reasons.</p>
</div>

<h4 id="Installing_the_service_worker">Installing the service worker</h4>

<p>The next time any page under the service worker's control is accessed (e.g. when the example is reloaded), the service worker is installed against that page, meaning that it will start controlling it. When this occurs, an <code>install</code> event is fired against the service worker; you can write code inside the service worker itself that will respond to the installation.</p>

<p>Let's look at an example, in the <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/client-side-storage/cache-sw/video-store-offline/sw.js">sw.js</a> file (the service worker). You'll see that the install listener is registered against <code>self</code>. This <code>self</code> keyword is a way to refer to the global scope of the service worker from inside the service worker file.</p>

<p>Inside the <code>install</code> handler we use the {{domxref("ExtendableEvent.waitUntil()")}} method, available on the event object, to signal that the browser shouldn't complete installation of the service worker until after the promise inside it has fulfilled successfully.</p>

<p>Here is where we see the Cache API in action. We use the {{domxref("CacheStorage.open()")}} method to open a new cache object in which responses can be stored (similar to an IndexedDB object store). This promise fulfills with a {{domxref("Cache")}} object representing the <code>video-store</code> cache. We then use the {{domxref("Cache.addAll()")}} method to fetch a series of assets and add their responses to the cache.</p>

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

<p>That's it for now, installation done.</p>

<h4 id="Responding_to_further_requests">Responding to further requests</h4>

<p>With the service worker registered and installed against our HTML page, and the relevant assets all added to our cache, we are nearly ready to go. There is only one more thing to do, write some code to respond to further network requests.</p>

<p>This is what the second bit of code in <code>sw.js</code> does. We add another listener to the service worker global scope, which runs the handler function when the <code>fetch</code> event is raised. This happens whenever the browser makes a request for an asset in the directory the service worker is registered against.</p>

<p>Inside the handler we first log the URL of the requested asset. We then provide a custom response to the request, using the {{domxref("FetchEvent.respondWith()")}} method.</p>

<p>Inside this block we use {{domxref("CacheStorage.match()")}} to check whether a matching request (i.e. matches the URL) can be found in any cache. This promise fulfills with the matching response if a match is not found, or <code>undefined</code> if it isn't.</p>

<p>If a match is found, we simply return it as the custom response. If not, we <a href="/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch">fetch()</a> the response from the network and return that instead.</p>

<pre class="brush: js notranslate">self.addEventListener('fetch', function(e) {
  console.log(e.request.url);
  e.respondWith(
    caches.match(e.request).then(function(response) {
      return response || fetch(e.request);
    })
  );
});</pre>

<p>And that is it for our simple service worker. There is a whole load more you can do with them — for a lot more detail, see the <a href="https://serviceworke.rs/">service worker cookbook</a>. And thanks to Paul Kinlan for his article <a href="https://developers.google.com/web/fundamentals/codelabs/offline/">Adding a Service Worker and Offline into your Web App</a>, which inspired this simple example.</p>

<h4 id="Testing_the_example_offline">Testing the example offline</h4>

<p>To test our <a href="https://mdn.github.io/learning-area/javascript/apis/client-side-storage/cache-sw/video-store-offline/">service worker example</a>, you'll need to load it a couple of times to make sure it is installed. Once this is done, you can:</p>

<ul>
 <li>Try unplugging your network/turning your Wifi off.</li>
 <li>Select <em>File &gt; Work Offline</em> if you are using Firefox.</li>
 <li>Go to the devtools, then choose <em>Application &gt; Service Workers</em>, then check the <em>Offline</em> checkbox if you are using Chrome.</li>
</ul>

<p>If you refresh your example page again, you should still see it load just fine. Everything is stored offline — the page assets in a cache, and the videos in an IndexedDB database.</p>

<h2 id="Summary">Summary</h2>

<p>That's it for now. We hope you've found our rundown of client-side storage technologies useful.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="/en-US/docs/Web/API/Web_Storage_API">Web storage API</a></li>
 <li><a href="/en-US/docs/Web/API/IndexedDB_API">IndexedDB API</a></li>
 <li><a href="/en-US/docs/Web/HTTP/Cookies">Cookies</a></li>
 <li><a href="/en-US/docs/Web/API/Service_Worker_API">Service worker API</a></li>
</ul>

<p>{{PreviousMenu("Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">Introduction to web APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents">Manipulating documents</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data">Fetching data from the server</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Third_party_APIs">Third party APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Drawing_graphics">Drawing graphics</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs">Video and audio APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage">Client-side storage</a></li>
</ul>
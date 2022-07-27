---
title: 处理常见的 JavaScript 问题
slug: Learn/Tools_and_testing/Cross_browser_testing/JavaScript
translation_of: Learn/Tools_and_testing/Cross_browser_testing/JavaScript
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS","Learn/Tools_and_testing/Cross_browser_testing/Accessibility", "Learn/Tools_and_testing/Cross_browser_testing")}}</div>

<p>现在我们看看如何跟踪一些常见的浏览器 JavaScript 问题并且如何修复它们。这个包括如何使用浏览器开发工具跟踪和修复问题、使用 Polyfill 或第三方库解决问题、如果让一些现代 JavaScript 的特性也能在旧的浏览器下面工作等。</p>

<table class="learn-box standard-table">
 <tbody>
  <tr>
   <th scope="row">先决条件：</th>
   <td>熟练使用 <a href="/en-US/docs/Learn/HTML">HTML</a>, <a href="/en-US/docs/Learn/CSS">CSS</a>, 和 <a href="/en-US/docs/Learn/JavaScript">JavaScript</a> 语言; 以及一些<a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Introduction">跨浏览器测试的高级概念</a>.</td>
  </tr>
  <tr>
   <th scope="row">本文目标：</th>
   <td>可以分析一些常见的 JavaScript 跨浏览器问题，并且学会使用一些工具和技术修复它们</td>
  </tr>
 </tbody>
</table>

<h2 id="The_trouble_with_JavaScript">The trouble with JavaScript</h2>

<p>Historically, JavaScript was plagued with cross-browser compatibility problems — back in the 1990s, the main browser choices back then (Internet Explorer and Netscape) had scripting implemented in different language flavours (Netscape had JavaScript, IE had JScript and also offered VBScript as an option), and while at least JavaScript and JScript were compatible to some degree (both based on the {{glossary("ECMAScript")}} specification), things were often implemented in conflicting, incompatible ways, causing developers many nightmares.</p>

<p>Such incompatibility problems persisted well into the early 2000s, as old browsers were still being used and still needed supporting. This is one of the main reasons why libraries like <a href="http://jquery.com/">jQuery</a> came into existence — to abstract away differences in browser implementations (e.g. see the code snippet in <a href="/en-US/docs/AJAX/Getting_Started#Step_1_%E2%80%93_How_to_make_an_HTTP_request">How to make an HTTP request</a>) so developers only have to write one simple bit of code (see <code><a href="http://api.jquery.com/jquery.ajax/">jQuery.ajax()</a></code>). jQuery (or whatever library you are using) will then handle the differences in the background, so you don't have to.</p>

<p>Things have got much better since then; modern browsers do a good job of supporting "classic JavaScript features", and the requirement to use such code has diminished as the requirement to support older browsers has lessened (although bear in mind that they have not gone away altogether).</p>

<p>These days, most cross-browser JavaScript problems are seen:</p>

<ul>
 <li>When bad quality browser sniffing code, feature detection code, and vendor prefix usage blocks browsers from running code they could otherwise use just fine.</li>
 <li>When developers make use of new/nascent JavaScript features (for example <a href="/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla">ECMAScript 6</a> / <a href="/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_Next_support_in_Mozilla">ECMAScript Next</a> features, modern Web APIs...) in their code, and find that such features don't work in older browsers.</li>
</ul>

<p>We'll explore all these problems and more below.</p>

<h2 id="修复一般的JavaScript问题">修复一般的 JavaScript 问题</h2>

<p>As we said in the <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS#First_things_first_fixing_general_problems">previous article</a> on HTML/CSS, you should make sure your code is working generally, before going on to concentrate on the cross-browser issues. If you are not already familiar with the basics of <a href="/en-US/docs/Learn/JavaScript/First_steps/What_went_wrong">Troubleshooting JavaScript</a>, you should study that article before moving on. There are a number of common JavaScript problems that you will want to be mindful of, such as:</p>

<ul>
 <li>Basic syntax and logic problems (again, check out <a href="/en-US/docs/Learn/JavaScript/First_steps/What_went_wrong">Troubleshooting JavaScript</a>).</li>
 <li>Making sure variables, etc. are defined in the correct scope, and you are not running into conflicts between items declared in different places (see <a href="/en-US/docs/Learn/JavaScript/Building_blocks/Functions#Function_scope_and_conflicts">Function scope and conflicts</a>).</li>
 <li>Confusion about <a href="/en-US/docs/Web/JavaScript/Reference/Operators/this">this</a>, in terms of what scope it applies to, and therefore if its value is what you intended. You can read <a href="/en-US/docs/Learn/JavaScript/Objects/Basics#What_is_this">What is "this"?</a> for a light introduction; you should also study examples like <a href="https://github.com/mdn/learning-area/blob/7ed039d17e820c93cafaff541aa65d874dde8323/javascript/oojs/assessment/main.js#L143">this one</a>, which shows a typical pattern of saving a <code>this</code> scope to a separate variable, then using that variable in nested functions so you can be sure you are applying functionality to the correct <code>this</code> scope.</li>
 <li>Incorrectly using functions inside loops — for example, in <a href="https://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/bad-for-loop.html">bad-for-loop.html</a> (see <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/bad-for-loop.html">source code</a>), we loop through 10 iterations, each time creating a paragraph and adding an <a href="/en-US/docs/Web/API/GlobalEventHandlers/onclick">onclick</a> event handler to it. When clicked, each one should alert a message containing its number (the value of <code>i</code> at the time it was created), however each one reports <code>i</code> as 11, because for loops do all their iterating before nested functions are invoked. If you want this to work correctly, you need to define a function to add the handler separately, calling it on each iteration and passing it the current value of <code>para</code> and <code>i</code> each time (or something similar). See <a href="https://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/good-for-loop.html">good-for-loop.html</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/good-for-loop.html">source code</a> also) for a version that works.</li>
 <li>Making sure asynchronous operations have returned before trying to use the values they return. For example, <a href="/en-US/docs/AJAX/Getting_Started#Step_3_%E2%80%93_A_Simple_Example">this Ajax example</a> checks to make sure the request is complete and the response has been returned before trying to use the response for anything. This kind of operation has been made easier to handle by the introduction to <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promises</a> to the JavaScript language.</li>
</ul>

<div class="note">
<p><strong>备注：</strong> <a href="https://www.toptal.com/javascript/10-most-common-javascript-mistakes">Buggy JavaScript Code: The 10 Most Common Mistakes JavaScript Developers Make</a> has some nice discussions of these common mistakes and more.</p>
</div>

<h3 id="Linters">Linters</h3>

<p>As with <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS#Linters">HTML and CSS</a>, you can ensure better quality, less error-prone JavaScript code using a linter, which points out errors and can also flag up warnings about bad practices, etc., and be customized to be stricter or more relaxed in their error/warning reporting. The JavaScript/ECMAScript linters we'd recommend are <a href="http://jshint.com/">JSHint</a> and <a href="http://eslint.org/">ESLint</a>; these can be used in a variety of ways, some of which we'll detail below.</p>

<h4 id="Online">Online</h4>

<p>The <a href="http://jshint.com/">JSHint homepage</a> provides an online linter, which allows you to enter your JavaScript code on the left and provides an output on the right, including metrics, warnings, and errors.</p>

<p><img src="jshint-online.png"></p>

<h4 id="Code_editor_plugins">Code editor plugins</h4>

<p>It is not very convenient to have to copy and paste your code over to a web page to check its validity several times. What you really want is a linter that will fit into your standard workflow with the minimum of hassle. Many code editors have linter plugins, for example Github's <a href="https://atom.io/">Atom</a> code editor has a JSHint plugin available.</p>

<p>To install it:</p>

<ol>
 <li>Install Atom (if you haven't got an up-to-date version already installed) — download it from the Atom page linked above.</li>
 <li>Go to Atom's <em>Preferences...</em> dialog (e.g. by Choosing <em>Atom &gt; Preferences...</em> on Mac, or <em>File &gt; Preferences...</em> on Windows/Linux) and choose the <em>Install</em> option in the left-hand menu.</li>
 <li>In the <em>Search packages</em> text field, type "jslint" and press Enter/Return to search for linting-related packages.</li>
 <li>You should see a package called <strong>lint</strong> at the top of the list. Install this first (using the <em>Install</em> button), as other linters rely on it to work. After that, install the <strong>linter-jshint</strong> plugin.</li>
 <li>After the packages have finished installing, try loading up a JavaScript file: you'll see any issues highlighted with green (for warnings) and red (for errors) circles next to the line numbers, and a separate panel at the bottom provides line numbers, error messages, and sometimes suggested values or other fixes.</li>
</ol>

<p><img src="jshint-linter.png">Other popular editors have similar linting packages available. For example, see the "Plugins for text editors and IDEs"  section of the <a href="http://jshint.com/install/">JSHint install page</a>.</p>

<h4 id="其他方式">其他方式</h4>

<p>There are other ways to use such linters; you can read about them on the <a href="http://jshint.com/install/">JSHint</a> and <a href="http://eslint.org/docs/user-guide/getting-started">ESLint</a> install pages.</p>

<p>It is worth mentioning command line uses — you can install these tools as command line utilities (available via the CLI — command line interface) using npm (Node Package Manager — you'll have to install <a href="https://nodejs.org/en/">NodeJS</a> first). For example, the following command installs JSHint:</p>

<pre class="brush: bash">npm install -g jshint
</pre>

<p>You can then point these tools at JavaScript files you want to lint, for example:</p>

<p><img src="js-hint-commandline.png">You can also use these tools with a task runner/build tool such as <a href="http://gulpjs.com/">Gulp</a> or <a href="https://webpack.github.io/">Webpack</a> to automatically lint your JavaScript during development. (see <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Automated_testing#Using_a_task_runner_to_automate_testing_tools">Using a task runner to automate testing tools</a> in a later article.) See <a href="http://eslint.org/docs/user-guide/integrations">ESLint integrations</a> for ESLint options; JSHint is supported out of the box by Grunt, and also has other integrations available, e.g. <a href="https://github.com/webpack/jshint-loader">JSHint loader for Webpack</a>.</p>

<div class="note">
<p><strong>备注：</strong> ESLint takes a bit more setup and configuration than JSHint, but it is more powerful too.</p>
</div>

<h3 id="浏览器开发者工具">浏览器开发者工具</h3>

<p>浏览器开发者工具有很多功能可以帮助定位 JavaScript 的问题，尤其是在开发的工程中，JavaScript 的 Console 会提醒一些错误信息</p>

<p>下载示例文件 <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/broken-ajax.html">broken-ajax.html</a> 到本地 (也可以阅读 <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/broken-ajax.html">source code</a> )。</p>
  
<p>如果你打开 console 面板，你将会看到错误提示 "TypeError: jsonObj is null"，标示出问题出现在代码的第 37 行。如果你看了源代码，相关的代码如下面所示：</p>

<pre class="brush: js">function populateHeader(jsonObj) {
  var myH1 = document.createElement('h1');
<strong>  myH1.textContent = jsonObj['squadName'];</strong>
  header.appendChild(myH1);

  ...</pre>

<p>So the code falls over as soon as we try to access <code>jsonObj</code> (which as you might expect, is supposed to be a <a href="/en-US/docs/Learn/JavaScript/Objects/JSON">JSON object</a>). This is supposed to be fetched from an external <code>.json</code> file using the following XMLHttpRequest call:</p>

<pre class="brush: js">var requestURL = 'https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json';
var request = new XMLHttpRequest();
request.open('GET', requestURL);
request.send();

<strong>var superHeroes = request.response;</strong>
populateHeader(superHeroes);
showHeroes(superHeroes);</pre>

<p>But this fails.</p>

<h4 id="Console相关API">Console 相关 API</h4>

<p>You may already know what is wrong with this code, but let's explore it some more to show how you could investigate this. For a start, there is a <a href="/en-US/docs/Web/API/Console">Console</a> API that allows JavaScript code to interact with the browser's JavaScript console. It has a number of features available, but the main one you'll use often is <code><a href="/en-US/docs/Web/API/Console/log">console.log()</a></code>, which prints a custom message to the console.</p>

<p>Try inserting the following line just below line 31 (bolded above):</p>

<pre class="brush: js">console.log('Response value: ' + superHeroes);</pre>

<p>Refresh the page in the browser, and you will get an output in the console of "Response value:", plus the same error message we saw before</p>

<p>The <code>console.log()</code> output shows that the <code>superHeroes</code> object doesn't appear to contain anything, although note that the error message has now changed, to "TypeError: heroes is undefined". This shows that the error is intermittent, giving further evidence that this is some kind of asynchronous error. Let's fix the current error and move on — remove the <code>console.log()</code> line, and update this code block:</p>

<pre class="brush: js">var superHeroes = request.response;
populateHeader(superHeroes);
showHeroes(superHeroes);</pre>

<p>to the following:</p>

<pre class="brush: js">request.onload = function() {
  var superHeroes = request.response;
  populateHeader(superHeroes);
  showHeroes(superHeroes);
}</pre>

<p>This solves the asynchronous issue, by ensuring that the functions are not run and passed the <code>superHeroes</code> object until the response has finished loading and is available.</p>

<p>So to summarize, anytime something is not working and a value does not appear to be what it is meant to be at some point in your code, you can use <code>console.log()</code> to print it out and see what is happening.</p>

<h4 id="Using_the_JavaScript_debugger">Using the JavaScript debugger</h4>

<p>We have solved one problem, but we are still stuck with the error message "TypeError: heroes is undefined", reported on line 51. Let's investigate this now, using a more sophisticated feature of browser developer tools: the <a href="/en-US/docs/Tools/Debugger">JavaScript debugger</a> as it is called in Firefox.</p>

<div class="note">
<p><strong>备注：</strong> Similar tools are available in other browsers; the <a href="https://developers.google.com/web/tools/chrome-devtools/#sources">Sources tab</a> in Chrome, Debugger in Safari (see <a href="https://developer.apple.com/safari/tools/">Safari Web Development Tools</a>), etc.</p>
</div>

<p>In Firefox, the Debugger tab looks as follows:</p>

<p><img src="debugger-tab.png"></p>

<ul>
 <li>On the left, you can select the script you want to debug (in this case we have only one).</li>
 <li>The center panel shows the code in the selected script.</li>
 <li>The right-hand panel shows useful details pertaining to the current environment — <em>Breakpoints</em>, <em>Callstack</em> and currently active <em>Scopes</em>.</li>
</ul>

<p>The main feature of such tools is the ability to add breakpoints to code — these are points where the execution of the code stops, and at that point you can examine the environment in its current state and see what is going on.</p>

<p>Let's get to work. First of all, we know that the error is being thrown at line 51. Click on line number 51 in the center panel to add a breakpoint to it (you'll see a blue arrow appear over the top of it). Now refresh the page (Cmd/Ctrl + R) — the browser will pause execution of the code at line 51. At this point, the right-hand side will update to show some very useful information.</p>

<p><img src="breakpoint.png"></p>

<ul>
 <li>Under <em>Breakpoints</em>, you'll see the details of the break-point you have set.</li>
 <li>Under <em>Call Stack</em>, you'll see two entries — this is basically a list of the series of functions that were invoked to cause the current function to be invoked. At the top, we have <code>showHeroes()</code> the function we are currently in, and below we have <code>request.onload</code>, which stores the event handler function containing the call to <code>showHeroes()</code>.</li>
 <li>Under <em>Scopes</em>, you'll see the currently active scope for the function we are looking at. We only have two — <code>showHeroes</code>, and <code>Window</code> (the global scope). Each scope can be expanded to show the values of variables inside the scope at the point that execution of the code was stopped.</li>
</ul>

<p>We can find out some very useful information in here.</p>

<ol>
 <li>Expand the <code>showHeroes</code> scope — you can see from this that the heroes variable is undefined, indicating that accessing the <code>members</code> property of <code>jsonObj</code> (first line of the function) didn't work.</li>
 <li>You can also see that the <code>jsonObj</code> variable is storing a text string, not a JSON object.</li>
 <li>Exploring further down the call stack, click <code>request.onload</code> in the <em>Call Stack</em> section. The view will update to show the <code>request.onload</code> function in the center panel, and its scopes in the <em>Scopes</em> section.</li>
 <li>Now if you expand the <code>request.onload</code> scope, you'll see that the <code>superHeroes</code> variable is a text string too, not an object. This settles it — our <code><a href="/en-US/docs/Web/API/XMLHttpRequest">XMLHttpRequest</a></code> call is returning the JSON as text, not JSON.</li>
</ol>

<div class="note">
<p><strong>备注：</strong> We'd like you to try fixing this problem yourself. To give you a clue, you can either <a href="/en-US/docs/Web/API/XMLHttpRequest/responseType">tell the XMLHttpRequest object explicitly to return JSON format</a>, or <a href="/en-US/docs/Learn/JavaScript/Objects/JSON#Converting_between_objects_and_text">convert the returned text to JSON</a> after the response arrives. If you get stuck, consult our <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/fixed-ajax.html">fixed-ajax.html</a> example.</p>
</div>

<div class="note">
<p><strong>备注：</strong> The debugger tab has many other useful features that we've not discussed here, for example conditional breakpoints and watch expressions. For a lot more information, see the <a href="/en-US/docs/Tools/Debugger">Debugger</a> page.</p>
</div>

<h3 id="Performance_issues">Performance issues</h3>

<p>As your apps get more complex and you start to use more JavaScript, you may start to run into performance problems, especially when viewing apps on slower devices. Performance is a big topic, and we don't have time to cover it in detail here. Some quick tips are as follows:</p>

<ul>
 <li>To avoid loading more JavaScript than you need, bundle your scripts into a single file using a solution like <a href="http://browserify.org/">Browserify</a>. In general, reducing the number of HTTP requests is very good for performance.</li>
 <li>Make your files even smaller by minifying them before you load them onto your production server. Minifying squashes all the code together onto a huge single line, making it take up far less file size. It is ugly, but you don't need to read it when it is finished! This is best done using a minification tool like <a href="https://github.com/mishoo/UglifyJS2">Uglify</a> (there's also an online version — see <a href="https://jscompress.com/">JSCompress.com</a>)</li>
 <li>When using APIs, make sure you turn off the API features when they are not being used; some API calls can be really expensive on processing power. For example, when showing a video stream, make sure it is turned off when you can't see it. When tracking a device's location using repeated Geolocation calls, make sure you turn it off when the user stops using it.</li>
 <li>Animations can be really costly for performance. A lot of JavaScript libraries provide animation capabilities programmed by JavaScript, but it is much more cost effective to do the animations via native browser features like <a href="/en-US/docs/Web/CSS/CSS_Animations">CSS Animations</a> (or the nascent <a href="/en-US/docs/Web/API/Web_Animations_API">Web Animations API</a>) than JavaScript. Read Brian Birtles' <a href="https://hacks.mozilla.org/2016/08/animating-like-you-just-dont-care-with-element-animate/">Animating like you just don’t care with Element.animate</a> for some really useful theory on why animation is expensive, tips on how to improve animation performance, and information on the Web Animations API.</li>
</ul>

<div class="note">
<p><strong>备注：</strong> Addy Osmani's <a href="https://www.smashingmagazine.com/2012/11/writing-fast-memory-efficient-javascript/">Writing Fast, Memory-Efficient JavaScript</a> contains a lot of detail and some excellent tips for boosting JavaScript performance.</p>
</div>

<h2 id="Cross-browser_JavaScript_problems">Cross-browser JavaScript problems</h2>

<p>In this section, we'll look at some of the more common cross-browser JavaScript problems. We'll break this down into:</p>

<ul>
 <li>Using modern core JavaScript features</li>
 <li>Using modern Web API features</li>
 <li>Using bad browser sniffing code</li>
 <li>Performance problems</li>
</ul>

<h3 id="Using_modern_JavaScriptAPI_features">Using modern JavaScript/API features</h3>

<p>In the <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS#Older_browsers_not_supporting_modern_features">previous article</a> we described some of the ways in which HTML and CSS errors and unrecognized features can be handled due to the nature of the languages. JavaScript is not as permissive as HTML and CSS however — if the JavaScript engine encounters mistakes or unrecognized syntax, more often than not it will throw errors.</p>

<p>There are a number of modern JavaScript language features defined in recent versions of the specs (<a href="/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla">ECMAScript 6</a> / <a href="/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_Next_support_in_Mozilla">ECMAScript Next</a>) that simply won't work in older browsers. Some of these are syntactic sugar (basically an easier, nicer way of writing what you can already do using existing features), and some offer interesting new possibilities.</p>

<p>For example:</p>

<ul>
 <li><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promises</a> are a great new feature for performing asynchronous operations and making sure those operations are complete before code that relies on their results is used for something else. As an example, the <a href="/en-US/docs/Web/API/GlobalFetch/fetch">Fetch API</a> (a modern equivalent to <a href="/en-US/docs/Web/API/XMLHttpRequest">XMLHTTPRequest</a>) uses promises to fetch resources across the network and make sure that the response has been returned before they are used (for example, displaying an image inside an {{htmlelement("img")}} element). They are not supported in IE at all but are supported across all modern browsers.</li>
 <li>Arrow functions provide a shorter, more convenient syntax for writing <a href="/en-US/docs/Learn/JavaScript/Building_blocks/Functions#Anonymous_functions">anonymous functions</a>, which also has other advantages (see <a href="/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions">Arrow functions</a>). For a quick example, see <a href="https://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/arrow-function.html">arrow-function.html</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/arrow-function.html">source code</a> also). Arrow functions are supported across all modern browsers, except for IE and Safari.</li>
 <li>Declaring <a href="/en-US/docs/Web/JavaScript/Reference/Strict_mode">strict mode</a> at the top of your JavaScript code causes it to be parsed with a stricter set of rules, meaning that more warnings and errors will be thrown, and some things will be disallowed that would otherwise be acceptable. It is arguably a good idea to use strict mode, as it makes for better, more efficient code, however it has limited/patchy support across browsers (see <a href="/en-US/docs/Web/JavaScript/Reference/Strict_mode#Strict_mode_in_browsers">Strict mode in browsers</a>).</li>
 <li><a href="/en-US/docs/Web/JavaScript/Typed_arrays">Typed arrays</a> allow JavaScript code to access and manipulate raw binary data, which is necessary as browser APIs for example start to manipulate streams of raw video and audio data. These are available in IE10 and above, and all modern browsers.</li>
</ul>

<p>There are also many new APIs appearing in recent browsers, which don't work in older browsers, for example:</p>

<ul>
 <li><a href="/en-US/docs/Web/API/IndexedDB_API">IndexedDB API</a>, <a href="/en-US/docs/Web/API/Web_Storage_API">Web Storage API</a>, and others for storing website data on the client-side.</li>
 <li><a href="/en-US/docs/Web/API/Web_Workers_API">Web Workers API</a> for running JavaScript in a separate thread, helping to improve performance.</li>
 <li><a href="/en-US/docs/Learn/WebGL">WebGL API</a> for real 3D graphics.</li>
 <li><a href="/en-US/docs/Web/API/Web_Audio_API">Web Audio API</a> for advanced audio manipulation.</li>
 <li><a href="/en-US/docs/Web/API/WebRTC_API">WebRTC API</a> for multi-person, real-time video/audio connectivity (e.g. video conferencing).</li>
 <li><a href="/en-US/docs/Web/API/WebVR_API">WebVR API</a> for engineering virtual reality experiences in the browser (e.g. controlling a 3D view with data input from VR Hardware)</li>
</ul>

<p>There are a few strategies for handling incompatibilities between browsers relating to feature support; let's explore the most common ones.</p>

<div class="note">
<p><strong>备注：</strong> These strategies do not exist in separate silos — you can, of course combine them as needed. For example, you could use feature detection to determine whether a feature is supported; if it isn't, you could then run code to load a polyfill or a library to handle the lack of support.</p>
</div>

<h4 id="Feature_detection">Feature detection</h4>

<p>The idea behind feature detection is that you can run a test to determine whether a JavaScript feature is supported in the current browser, and then conditionally run code to provide an acceptable experience both in browsers that do and don't support the feature. As a quick example, the <a href="/en-US/docs/Web/API/Geolocation/Using_geolocation">Geolocation API</a>  (which exposes available location data for the device the web browser is running on) has a main entry point for its use — a <code>geolocation</code> property available on the global <a href="/en-US/docs/Web/API/Navigator">Navigator</a> object. Therefore, you can detect whether the browser supports geolocation or not by using something like the following:</p>

<pre class="brush: js">if("geolocation" in navigator) {
  navigator.geolocation.getCurrentPosition(function(position) {
    // show the location on a map, perhaps using the Google Maps API
  });
} else {
  // Give the user a choice of static maps instead perhaps
}</pre>

<p>You could also write such a test for a CSS feature, for example by testing for the existence of <em><a href="/en-US/docs/Web/API/HTMLElement/style">element.style.property</a></em> (e.g. <code>paragraph.style.transform !== undefined</code>). But for both CSS and JavaScript, it is probably better to use an established feature detection library rather than writing your own all the time. Modernizr is the industry standard for feature detection tests.</p>

<p>As a last point, don't confuse feature detection with <strong>browser sniffing</strong> (detecting what specific browser is accessing the site) — this is a terrible practice that should be discouraged at all costs. See <a href="#using_bad_browser_sniffing_code">Using bad browser sniffing code</a>, later on, for more details.</p>

<div class="note">
<p><strong>备注：</strong> Some features are known to be undetectable — see Modernizr's list of <a href="https://github.com/Modernizr/Modernizr/wiki/Undetectables">Undetectables</a>.</p>
</div>

<div class="note">
<p><strong>备注：</strong> Feature detection will be covered in a lot more detail in its own dedicated article, later in the module.</p>
</div>

<h4 id="Libraries">Libraries</h4>

<p>JavaScript libraries are essentially third party units of code that you can attach to your page, providing you with a wealth of ready-made functionality that can be used straight away, saving you a lot of time in the process. A lot of JavaScript libraries probably came into existence because their developer was writing a set of common utility functions to save them time when writing future projects, and decided to release them into the wild because other people might find them useful too.</p>

<p>JavaScript libraries tend to come in a few main varieties (some libraries will serve more than one of these purposes):</p>

<ul>
 <li>Utility libraries: Provide a bunch of functions to make mundane tasks easier and less boring to manage. <a href="http://jquery.com/">jQuery</a> for example provides its own fully-featured selectors and DOM manipuation libraries, to allow CSS-selector type selecting of elements in JavaScript and easier DOM building. It is not so important now we have modern features like {{domxref("Document.querySelector()")}}/{{domxref("Document.querySelectorAll()")}}/{{domxref("Node")}} methods available across browsers, but it can still be useful when older browsers need supporting.</li>
 <li>Convenience libraries: Make difficult things easier to do. For example, the <a href="/en-US/docs/Learn/WebGL">WebGL API</a> is really complex and challenging to use when you write it directly, so the <a href="https://threejs.org/">Three.js</a> library (and others) is built on top of WebGL and provides a much easier API for creating common 3D objects, lighting, textures, etc. The <a href="/en-US/docs/Web/API/Service_Worker_API">Service Worker API</a> is also very complex to use, so code libraries have started appearing to make common Service Worker uses-cases much easier to implement (see the <a href="https://serviceworke.rs/">Service Worker Cookbook</a> for several useful code samples).</li>
 <li>Effects libraries: These libraries are designed to allow you to easily add special effects to your websites. This was more useful back when {{glossary("DHTML")}} was a popular buzzword, and implementing effect involved a lot of complex JavaScript, but these days browsers have a lot of built in CSS3 features and APIs to implementing effects more easily. See <a href="https://www.javascripting.com/animation/">JavaScripting.com/animation</a> for a list of libraries.</li>
 <li>UI libraries: Provide methods for implementing complex UI features that would otherwise be challenging to implement and get working cross browser, for example <a href="http://jqueryui.com/">jQuery UI</a> and <a href="http://foundation.zurb.com/">Foundation</a>. These tend to be used as the basis of an entire site layout; it is often difficult to drop them in just for one UI feature.</li>
 <li>Normalization libraries: Give you a simple syntax that allows you to easily complete a task without having to worry about cross browser differences. The library will manipulate appropriate APIs in the background so the functionality will work whatever the browser (in theory). For example, <a href="https://github.com/localForage/localForage">LocalForage</a> is a library for client-side data storage, which provides a simple syntax for storing and retrieving data. In the background, it uses the best API the browser has available for storing the data, whether that is <a href="/en-US/docs/Web/API/IndexedDB_API">IndexedDB</a>, <a href="/en-US/docs/Web/API/Web_Storage_API">Web Storage</a>, or even WebSQL (which is now deprecated, but is still supported in some older versions of Safari/IE). As another example, jQuery</li>
</ul>

<p>When choosing a library to use, make sure that it works across the set of browsers you want to support, and test your implementation thoroughly. Also make sure that the library is popular and well-supported, and isn't likely to just become obsolete next week. Talk to other developers to find out what they recommend, see how much activity and how many contributors the library has on Github (or wherever else it is stored), etc.</p>

<div class="note">
<p><strong>备注：</strong> <a href="https://www.javascripting.com/">JavaScripting.com</a> gives you a good idea of just how many JavaScript libraries there are available, and can be useful for finding libraries for specific purposes.</p>
</div>

<p>Library usage at a basic level tends to consist of downloading the library's files (JavaScript, possibly some CSS or other dependencies too) and attaching them to your page (e.g. via a {{htmlelement("script")}} element), although there are normally many other usage options for such libraries, like installing them as <a href="https://bower.io/">Bower</a> components, or including them as dependencies via the <a href="https://webpack.github.io/">Webpack</a> module bundler. You will have to read the libraries' individual install pages for more information.</p>

<div class="note">
<p><strong>备注：</strong> You will also come across JavaScript frameworks in your travels around the Web, like <a href="http://emberjs.com/">Ember</a> and <a href="https://angularjs.org/">Angular</a>. Whereas libraries are often usable for solving individual problems and dropping into existing sites, frameworks tend to be more along the lines of complete solutions for developing complex web applications.</p>
</div>

<h4 id="Polyfills">Polyfills</h4>

<p>Polyfills also consist of 3rd party JavaScript files that you can drop into your project, but they differ from libraries — whereas libraries tend to enhance existing functionality and make things easier, polyfills provide functionality that doesn't exist at all. Polyfills use JavaScript or other technologies entirely to build in support for a feature that a browser doesn't support natively. For example, you might use a polyfill like <a href="https://github.com/stefanpenner/es6-promise">es6-promise</a> to make promises work in browsers where they are not supported natively.</p>

<p>Modernizr's list of <a href="https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills">HTML5 Cross Browser Polyfills</a> is a useful place to find polyfills for different purposes. Again, you should research them before you use them — make sure they work and are maintained.</p>

<p>Let's work through an exercise — in this example we will use a Fetch polyfill to provide support for the Fetch API in older browsers; however we also need to use the es6-promise polyfill, as Fetch makes heavy use of promises, and browsers that don't support them will still be in trouble.</p>

<ol>
 <li>To get started, make a local copy of our <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/fetch-polyfill.html">fetch-polyfill.html</a> example and <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/flowers.jpg">our nice image of some flowers</a> in a new directory. We are going to write code to fetch the flowers image and display it in the page.</li>
 <li>Next, save copies of the <a href="https://raw.githubusercontent.com/github/fetch/master/fetch.js">Fetch polyfill</a> and the <a href="https://raw.githubusercontent.com/stefanpenner/es6-promise/master/dist/es6-promise.js">es6-promises polyfill</a> in the same directory as the HTML.</li>
 <li>Apply the polyfill scripts to the page using the following code — place these above the existing {{htmlelement("script")}} element so they will be available on the page already when we start trying to use Fetch:
  <pre class="brush: js">&lt;script src="es6-promise.js"&gt;&lt;/script&gt;
&lt;script src="fetch.js"&gt;&lt;/script&gt;</pre>
 </li>
 <li>Inside the original {{htmlelement("script")}}, add the following code:</li>
 <li>
  <pre class="brush: js">var myImage = document.querySelector('.my-image');

fetch('flowers.jpg').then(function(response) {
  response.blob().then(function(myBlob) {
    var objectURL = URL.createObjectURL(myBlob);
    myImage.src = objectURL;
  });
});</pre>
 </li>
 <li>Now if you load it in a browser that doesn't support Fetch (Safari and IE are obvious candidates), you should still see the flower image appear — cool! <br>
  <img src="fetch-image.jpg"></li>
</ol>

<div class="note">
<p><strong>备注：</strong> You can find our finished version at <a href="http://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/fetch-polyfill-finished.html">fetch-polyfill-finished.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/fetch-polyfill-finished.html">source code</a>).</p>
</div>

<div class="note">
<p><strong>备注：</strong> Again, there are many different ways to make use of the different polyfills you will encounter — consult each polyfill's individual documentation.</p>
</div>

<p>One thing you might be thinking is "why should we always load the polyfill code, even if we don't need it?" This is a good point — as your sites get more complex and you start to use more libraries, polyfills, etc., you can start to load a lot of extra code, which can start to affect performance, especially on less-powerful devices. It makes sense to only load files as needed.</p>

<p>Doing this requires some extra setup in your JavaScript. You need some kind of a feature detection test that detects whether the browser supports the feature we are trying to use:</p>

<pre class="brush: js"><code class="language-js">if (browserSupportsAllFeatures()) {
  main();
} else {
  loadScript('polyfills.js', main);
}

function main(err) {
  // actual app code goes in here
}</code></pre>

<p>So first we run a conditional that checks whether the function <code>browserSupportsAllFeatures()</code> returns true. If it does, we run the <code>main()</code> function, which will contain all our app's code. <code>browserSupportsAllFeatures()</code> looks like this:</p>

<pre class="brush: js"><code class="language-js">function browserSupportsAllFeatures() {
  return window.Promise &amp;&amp; window.fetch;
}</code></pre>

<p>Here we are testing whether the <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promise</a></code> object and <code><a href="/en-US/docs/Web/API/GlobalFetch/fetch">fetch()</a></code> function exist in the browser. If both do, the function returns true. If the function returns <code>false</code>, then we run the code inside the second part of the conditional  — this runs a function called loadScript(), which loads the polyfills into the page, then runs <code>main()</code> after the loading has finished. <code>loadScript()</code> looks like this:</p>

<pre class="brush: js"><code class="language-js">function loadScript(src, done) {
  var js = document.createElement('script');
  js.src = src;
  js.onload = function() {
    done();
  };
  js.onerror = function() {
    done(new Error('Failed to load script ' + src));
  };
  document.head.appendChild(js);
}</code>
</pre>

<p>This function creates a new <code>&lt;script&gt;</code> element, then sets its <code>src</code> attribute to the path we specified as the first argument (<code>'polyfills.js'</code> when we called it in the code above). When it has loaded, we run the function we specified as the second argument (<code>main()</code>). If an error occurs in the loading of the script, we still call the function, but with a custom error that we can retrieve to help debug a problem if it occurs.</p>

<p>Note that polyfills.js is basically the two polyfills we are using put together into one file. We did this manually, but there are cleverer solutions that will automatically generate bundles for you — see <a href="http://browserify.org/">Browserify</a> (see <a href="https://www.sitepoint.com/getting-started-browserify/">Getting started with Browserify</a> for a basic tutorial). It is a good idea to bundle JS files into one like this — reducing the number of HTTP requests you need to make improves the performance of your site.</p>

<p>You can see this code in action in <a href="http://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/fetch-polyfill-only-when-needed.html">fetch-polyfill-only-when-needed.html</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/fetch-polyfill-only-when-needed.html">source code also</a>). We'd like to make it clear that we can't take credit for this code — it was originally written by Philip Walton.  Check out his article <a href="https://philipwalton.com/articles/loading-polyfills-only-when-needed/">Loading Polyfills Only When Needed</a> for the original code, plus a lot of useful explanation around the wider subject).</p>

<div class="note">
<p><strong>备注：</strong> There are some 3rd party options to consider, for example <a href="https://polyfill.io/v2/docs/">Polyfill.io</a> — this is a meta-polyfill library that will look at each browser's capabilities and apply polyfills as needed, depending on what APIs and JS features you are using in your code.</p>
</div>

<h4 id="JavaScript_transpiling">JavaScript transpiling</h4>

<p>Another option that is becoming popular for people that want to use modern JavaScript features now is converting code that makes use of ECMAScript 6/ECMAScript 2015 features to a version that will work in older browsers.</p>

<div class="note">
<p><strong>备注：</strong> This is called "transpiling" — you are not compiling code into a lower level for to be run on a computer (like you would say with C code); instead, you are changing it into a syntax that exists at a similar level of abstraction so it can be used in the same way, but in slightly different circumstances (in this case, transforming one flavor of JavaScript into another).</p>
</div>

<p>So for example, we talked about arrow functions (see <a href="http://mdn.github.io/learning-area/tools-testing/cross-browser-testing/javascript/arrow-function.html">arrow-function.html</a> live, and see the <a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/javascript/arrow-function.html">source code</a>) earlier in the article, which only work in the newest browsers:</p>

<pre class="brush: js">() =&gt; { ... }</pre>

<p>We could transpile this across to a traditional old-fashioned anonymous function, so it would work in older browsers:</p>

<pre class="brush: js">function() { ... }</pre>

<p>The recommended tool for JavaScript transpiling is currently <a href="https://babeljs.io/">Babel</a>. This offers transpilation capabilities for language features that are appropriate for transpilation. For features that can't just be easily transpiled into an older equivalent, Babel also offers polyfills to provide support.</p>

<p>The easiest way to give Babel a try is to use the <a href="https://babeljs.io/repl/">online version</a>, which allows you to enter your source code on the left, and outputs a transpiled version on the right.</p>

<div class="note">
<p><strong>备注：</strong> There are many ways to use Babel (task runners, automation tools, etc.), as you'll see on the <a href="https://babeljs.io/docs/setup/">setup page</a>.</p>
</div>

<h3 id="Using_bad_browser_sniffing_code">Using bad browser sniffing code</h3>

<p>All browsers have a <strong>user-agent</strong> string, which identifies what the browser is (version, name, OS, etc.) In the bad only days when pretty much everyone used Netscape or Internet Explorer, developers used to use so-called <strong>browser sniffing code</strong> to detect which browser the user was using, and give them appropriate code to work on that browser.</p>

<p>The code used to look something like this (although this is a simplified example):</p>

<pre class="brush: js">var ua = navigator.userAgent;

if(ua.indexOf('Firefox') !== -1) {
  // run Firefox-specific code
} else if(ua.indexOf('Chrome') !== -1) {
  // run Chrome-specific code
}</pre>

<p>The idea was fairly good — detect what browser is viewing the site, and run code as appropriate to make sure the browser will be able to use your site OK.</p>

<div class="note">
<p><strong>备注：</strong> Try opening up your JavaScript console now and running navigator.userAgent, to see what you get returned.</p>
</div>

<p>However, as time went on, developers started to see major problems with this approach. For a start, the code was error prone. What if you knew a feature didn't work in say, Firefox 10 and below, and implemented code to detect this, and then Firefox 11 came out — which did support that feature? Firefox 11 probably wouldn't be supported because it's not Firefox 10. You'd have to change all your sniffing code regularly.</p>

<p>Many developers implemented bad browser sniffing code and didn't maintain it, and browsers start getting locked out of using websites containing features that they had since implemented. This became so common that browsers started to lie about what browser they were in their user-agent strings (or claim they were all browsers), to get around sniffing code. Browsers also implemented facilities to allow users to change what user-agent string the browser reported when queried with JavaScript. This all made browser sniffing even more error prone, and ultimately pointless. </p>

<div class="note">
<p><strong>备注：</strong> You should read <a href="http://webaim.org/blog/user-agent-string-history/">History of the browser user-agent string</a> by Aaron Andersen for a useful and amusing take on this situation.</p>
</div>

<p>The lesson to be learned here is — NEVER use browser sniffing. The only really use case for browser sniffing code in the modern day is if you are implementing a fix for a bug in a very specific version of a particular browser. But even then, most bugs get fixed pretty quickly in browser vendor rapid release cycles. It won't come up very often. <a href="#feature_detection">Feature detection</a> is almost always a better option — if you detect whether a feature is supported, you won't need to change your code when new browser versions come out, and the tests are much more reliable.</p>

<p>If you come across browser sniffing when joining an existing project, look at whether it can be replaced with something more sensible. Browser sniffing causes all kind of interesting bugs, like {{bug(1308462)}}.</p>

<h3 id="Handling_JavaScript_prefixes">Handling JavaScript prefixes</h3>

<p>In the previous article, we included quite a lot of discussion about <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS#Handling_CSS_prefixes">handing CSS prefixes</a>. Well, new JavaScript implementations sometimes use prefixes too, although JavaScript uses camel case rather than hyphenation like CSS. For example, if a prefix was being used on a new shint API object called <code>Object</code>:</p>

<ul>
 <li>Mozilla would use <code>mozObject</code></li>
 <li>Chrome/Opera/Safari would use <code>webkitObject</code></li>
 <li>Microsoft would use <code>msObject</code></li>
</ul>

<p>Here's an example, taken from our <a href="http://mdn.github.io/violent-theremin/">violent-theremin demo</a> (see <a href="https://github.com/mdn/violent-theremin">source code</a>), which uses a combination of the <a href="/en-US/docs/Web/API/Canvas_API">Canvas API</a> and the <a href="/en-US/docs/Web/API/Web_Audio_API">Web Audio API</a> to create a fun (and noisy) drawing tool:</p>

<pre class="brush: js">var AudioContext = window.AudioContext || window.webkitAudioContext;
var audioCtx = new AudioContext();</pre>

<p>In the case of the Web Audio API, the key entry points to using the API were supported in Chrome/Opera via <code>webkit</code> prefixed versions (they now support the unprefixed versions). The easy way to get around this situation is to create a new version of the objects that are prefixed in some browsers, and make it equal to the non-prefixed version, OR the prefixed version (OR any other prefixed versions that need consideration) — whichever one is supported by the browser currently viewing the site will be used.</p>

<p>Then we use that object to manipulate the API, rather than the original one. In this case we are creating a modified <a href="/en-US/docs/Web/API/AudioContext">AudioContext</a> constructor, then creating a new audio context instance to use for our Web Audio coding.</p>

<p>This pattern can be applied to just about any prefixed JavaScript feature. JavaScript libraries/polyfills also make use of this kind of code, to abstract browser differences away from the developer as much as possible.</p>

<p>Again, prefixed features were never supposed to be used in production websites — they are subject to change or removal without warning, and cause cross browser issues. If you insist on using prefixed features, make sure you use the right ones. You can look up what browsers require prefixes for different JavaScript/API features on MDN reference pages, and sites like <a href="http://caniuse.com/">caniuse.com</a>. If you are unsure, you can also find out by doing some testing directly in browsers.</p>

<p>For example, try going into your browser's developer console and start typing</p>

<pre class="brush: js">window.AudioContext</pre>

<p>If this feature is supported in your browser, it will autocomplete.</p>

<h2 id="Finding_help">Finding help</h2>

<p>There are many other issues you'll encounter with JavaScript; the most important thing to know really is how to find answers online. Consult the HTML and CSS article's <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS#Finding_help">Finding help section</a> for our best advice.</p>

<h2 id="Summary">Summary</h2>

<p>So that's JavaScript. Simple huh? Maybe not so simple, but this article should at least give you a start, and some ideas on how to tackle the JavaScript-related problems you will come across.</p>

<p>{{PreviousMenuNext("Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS","Learn/Tools_and_testing/Cross_browser_testing/Accessibility", "Learn/Tools_and_testing/Cross_browser_testing")}}</p>

<p> </p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Introduction">Introduction to cross browser testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Testing_strategies">Strategies for carrying out testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS">Handling common HTML and CSS problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/JavaScript">Handling common JavaScript problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Accessibility">Handling common accessibility problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection">Implementing feature detection</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Automated_testing">Introduction to automated testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment">Setting up your own test automation environment</a></li>
</ul>

<p> </p>
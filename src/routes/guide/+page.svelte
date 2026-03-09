<svelte:head>
	<title>Quickstart | Baseline</title>
	<meta name="description" content="Learn Baseline from installation to building web servers, step by step." />
</svelte:head>

<nav class="on-this-page">
	<h4>On this page</h4>
	<ul>
		<li><a href="#install">Installation</a></li>
		<li><a href="#hello">Hello World</a></li>
		<li><a href="#functions">Functions</a></li>
		<li><a href="#annotations">Type Annotations</a></li>
		<li><a href="#pipes">Pipes</a></li>
		<li><a href="#pattern-matching">Pattern Matching</a></li>
		<li><a href="#errors">Error Handling</a></li>
		<li><a href="#testing">Testing</a></li>
		<li><a href="#web">Web Server</a></li>
		<li><a href="#next">Next Steps</a></li>
	</ul>
</nav>

<h1>Quickstart</h1>
<p class="lead">
	Install Baseline, then go from hello world to hello web server.
</p>

<section id="install">
	<h2>Installation</h2>
	<pre><code>brew install baseline-lang/tap/baseline</code></pre>
</section>

<section id="hello">
	<h2>Hello World</h2>
	<p>Create <code>hello.bl</code>:</p>
	<pre><code>{@html `<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">script</span><span class="punct">)</span>

<span class="kw">fn</span> <span class="fn">main!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="punct">()</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"Hello, World!"</span><span class="punct">)</span>`}</code></pre>
	<pre><code>{@html `$ blc run hello.bl
Hello, World!`}</code></pre>
	<p>
		<code>@prelude(script)</code> loads the standard library.
		The <code>!</code> suffix means this function has side effects, and
		<code>{"{Console}"}</code> says which ones.
	</p>
</section>

<section id="functions">
	<h2>Functions</h2>
	<p>
		Every function is <code>fn name(params) -> Type = body</code>.
		The last expression is the return value, there is no <code>return</code> keyword.
	</p>
	<p>
		If the body is a single expression, write it right after <code>=</code>.
		If you need multiple steps, wrap the body in <code>{"{ }"}</code> braces.
		The last expression in a block is the return value.
	</p>
	<pre><code>{@html `<span class="comment">// Single expression: no braces needed</span>
<span class="kw">fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Still one expression, just on the next line</span>
<span class="kw">fn</span> <span class="fn">greet</span><span class="punct">(</span><span class="fn">name</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">String</span> <span class="op">=</span>
  <span class="str">"Hello, \${name}!"</span>

<span class="comment">// Multiple steps: use braces</span>
<span class="kw">fn</span> <span class="fn">process</span><span class="punct">(</span><span class="fn">input</span><span class="op">:</span> <span class="type">Input</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Output</span> <span class="op">=</span> <span class="punct">{</span>
  <span class="kw">let</span> <span class="fn">parsed</span> <span class="op">=</span> <span class="fn">parse</span><span class="punct">(</span><span class="fn">input</span><span class="punct">)</span>
  <span class="kw">let</span> <span class="fn">validated</span> <span class="op">=</span> <span class="fn">validate</span><span class="punct">(</span><span class="fn">parsed</span><span class="punct">)</span>
  <span class="fn">transform</span><span class="punct">(</span><span class="fn">validated</span><span class="punct">)</span>
<span class="punct">}</span>

<span class="comment">// Lambdas use |args| body</span>
<span class="kw">let</span> <span class="fn">double</span> <span class="op">=</span> <span class="op">|</span><span class="fn">x</span><span class="op">|</span> <span class="fn">x</span> <span class="op">*</span> <span class="num">2</span>`}</code></pre>
</section>

<section id="annotations">
	<h2>When Do I Need Type Annotations?</h2>
	<p>
		Baseline uses type inference. You must annotate exported and effectful functions,
		but can omit types on local variables, lambdas, and private helpers.
	</p>
	<pre><code>{@html `<span class="comment">// Exported: full annotations required</span>
<span class="kw">export fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Private helper: types inferred</span>
<span class="kw">fn</span> <span class="fn">double</span><span class="punct">(</span><span class="fn">x</span><span class="punct">)</span> <span class="op">=</span> <span class="fn">x</span> <span class="op">*</span> <span class="num">2</span>

<span class="comment">// Local variables and lambdas: inferred</span>
<span class="kw">let</span> <span class="fn">names</span> <span class="op">=</span> <span class="type">List</span><span class="punct">.</span><span class="fn">map</span><span class="punct">(</span><span class="fn">users</span><span class="punct">,</span> <span class="op">|</span><span class="fn">u</span><span class="op">|</span> <span class="fn">u</span><span class="punct">.</span><span class="fn">name</span><span class="punct">)</span>`}</code></pre>

	<h3>Effect Braces</h3>
	<p>
		The curly braces <code>{"{...}"}</code> after <code>-&gt;</code> declare which
		side effects a function uses. Pure functions don't have them at all.
	</p>
	<pre><code>{@html `<span class="comment">// Pure: no braces, no ! suffix</span>
<span class="kw">fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Effectful: ! suffix + braces declare effects</span>
<span class="kw">fn</span> <span class="fn">main!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="punct">()</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"hello"</span><span class="punct">)</span>

<span class="comment">// Internal effectful: effects can be inferred</span>
<span class="kw">fn</span> <span class="fn">log_request!</span><span class="punct">(</span><span class="fn">req</span><span class="punct">)</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">req</span><span class="punct">.</span><span class="fn">path</span><span class="punct">)</span>`}</code></pre>
	<p>
		The rule: exported and public functions must declare their effects explicitly.
		Private helpers get them inferred automatically. The <code>!</code> suffix
		always tells you a function has side effects, and the braces tell the compiler
		<em>which</em> ones.
	</p>
</section>

<section id="pipes">
	<h2>Pipes</h2>
	<p>
		<code>|&gt;</code> passes the left side as the first argument to the right
		side. You read the data flow left to right instead of inside out.
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">active_names</span><span class="punct">(</span><span class="fn">users</span><span class="op">:</span> <span class="type">List&lt;User&gt;</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">List&lt;String&gt;</span> <span class="op">=</span>
  <span class="fn">users</span>
  <span class="op">|&gt;</span> <span class="type">List</span><span class="punct">.</span><span class="fn">filter</span><span class="punct">(|</span><span class="fn">u</span><span class="op">|</span> <span class="fn">u</span><span class="punct">.</span><span class="fn">active</span><span class="punct">)</span>
  <span class="op">|&gt;</span> <span class="type">List</span><span class="punct">.</span><span class="fn">map</span><span class="punct">(|</span><span class="fn">u</span><span class="op">|</span> <span class="fn">u</span><span class="punct">.</span><span class="fn">name</span><span class="punct">)</span>`}</code></pre>
	<p>
		The compiler warns you (<code>STY_001</code>) if you nest single-argument
		calls instead of piping.
	</p>
</section>

<section id="pattern-matching">
	<h2>Pattern Matching</h2>
	<p>
		<code>match</code> is exhaustive. Miss a branch and the compiler tells you.
		Destructure variants to pull out data.
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Shape</span> <span class="op">=</span>
  <span class="op">|</span> <span class="type">Circle</span><span class="punct">(</span><span class="type">Int</span><span class="punct">)</span>
  <span class="op">|</span> <span class="type">Rect</span><span class="punct">(</span><span class="type">Int</span><span class="punct">,</span> <span class="type">Int</span><span class="punct">)</span>

<span class="kw">fn</span> <span class="fn">area</span><span class="punct">(</span><span class="fn">s</span><span class="op">:</span> <span class="type">Shape</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span>
  <span class="kw">match</span> <span class="fn">s</span>
    <span class="type">Circle</span><span class="punct">(</span><span class="fn">r</span><span class="punct">)</span>    <span class="op">-&gt;</span> <span class="num">3</span> <span class="op">*</span> <span class="fn">r</span> <span class="op">*</span> <span class="fn">r</span>
    <span class="type">Rect</span><span class="punct">(</span><span class="fn">w</span><span class="punct">,</span> <span class="fn">h</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="fn">w</span> <span class="op">*</span> <span class="fn">h</span>`}</code></pre>
</section>

<section id="errors">
	<h2>Error Handling</h2>
	<p>
		No exceptions. If a function can fail, it returns
		<code>Result&lt;T, E&gt;</code>. Use <code>?</code> to propagate
		or <code>match</code> to handle it yourself.
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">parse_port</span><span class="punct">(</span><span class="fn">s</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Result&lt;Int, String&gt;</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">n</span> <span class="op">=</span> <span class="type">Int</span><span class="punct">.</span><span class="fn">parse</span><span class="punct">(</span><span class="fn">s</span><span class="punct">)</span><span class="op">?</span>
  <span class="kw">if</span> <span class="fn">n</span> <span class="op">&gt;=</span> <span class="num">1</span> <span class="op">&amp;&amp;</span> <span class="fn">n</span> <span class="op">&lt;=</span> <span class="num">65535</span>
    <span class="kw">then</span> <span class="type">Ok</span><span class="punct">(</span><span class="fn">n</span><span class="punct">)</span>
    <span class="kw">else</span> <span class="type">Err</span><span class="punct">(</span><span class="str">"Port out of range"</span><span class="punct">)</span>`}</code></pre>
	<p>
		Optional values are <code>Option&lt;T&gt;</code>, either <code>Some(value)</code>
		or <code>None</code>. No <code>null</code>.
	</p>
</section>

<section id="testing">
	<h2>Testing</h2>
	<p>
		Tests go in <code>@test</code> sections right next to the code.
		Run them with <code>blc test --json</code>.
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">clamp</span><span class="punct">(</span><span class="fn">v</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">lo</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">hi</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span>
  <span class="kw">if</span> <span class="fn">v</span> <span class="op">&lt;</span> <span class="fn">lo</span> <span class="kw">then</span> <span class="fn">lo</span>
  <span class="kw">else</span> <span class="kw">if</span> <span class="fn">v</span> <span class="op">&gt;</span> <span class="fn">hi</span> <span class="kw">then</span> <span class="fn">hi</span>
  <span class="kw">else</span> <span class="fn">v</span>

<span class="annot">@test</span>
<span class="kw">test</span> <span class="str">"below"</span> <span class="op">=</span> <span class="fn">clamp</span><span class="punct">(</span><span class="num">-5</span><span class="punct">,</span> <span class="num">0</span><span class="punct">,</span> <span class="num">100</span><span class="punct">)</span> <span class="op">==</span> <span class="num">0</span>
<span class="kw">test</span> <span class="str">"in range"</span> <span class="op">=</span> <span class="fn">clamp</span><span class="punct">(</span><span class="num">50</span><span class="punct">,</span> <span class="num">0</span><span class="punct">,</span> <span class="num">100</span><span class="punct">)</span> <span class="op">==</span> <span class="num">50</span>
<span class="kw">test</span> <span class="str">"above"</span> <span class="op">=</span> <span class="fn">clamp</span><span class="punct">(</span><span class="num">200</span><span class="punct">,</span> <span class="num">0</span><span class="punct">,</span> <span class="num">100</span><span class="punct">)</span> <span class="op">==</span> <span class="num">100</span>`}</code></pre>
</section>

<section id="web">
	<h2>Web Server</h2>
	<p>
		Here's a full HTTP server. Switch to <code>@prelude(server)</code> to
		get routing, JSON, and database modules.
	</p>
	<pre><code>{@html `<span class="annot">@prelude(server)</span>

<span class="kw">fn</span> <span class="fn">list_users</span><span class="punct">(</span><span class="fn">req</span><span class="punct">)</span> <span class="op">=</span>
  <span class="type">Response</span><span class="punct">.</span><span class="fn">json</span><span class="punct">(</span>[{ <span class="fn">id</span><span class="op">:</span> <span class="num">1</span><span class="punct">,</span> <span class="fn">name</span><span class="op">:</span> <span class="str">"Alice"</span> }]<span class="punct">)</span>

<span class="kw">fn</span> <span class="fn">get_user</span><span class="punct">(</span><span class="fn">req</span><span class="punct">)</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">id</span> <span class="op">=</span> <span class="type">Request</span><span class="punct">.</span><span class="fn">param</span><span class="punct">(</span><span class="fn">req</span><span class="punct">,</span> <span class="str">"id"</span><span class="punct">)</span>
  <span class="type">Response</span><span class="punct">.</span><span class="fn">json</span><span class="punct">(</span>{ <span class="fn">id</span><span class="op">:</span> <span class="fn">id</span><span class="punct">,</span> <span class="fn">name</span><span class="op">:</span> <span class="str">"Alice"</span> }<span class="punct">)</span>

<span class="kw">fn</span> <span class="fn">main!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Http}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">app</span> <span class="op">=</span> <span class="type">Router</span><span class="punct">.</span><span class="fn">new</span><span class="punct">()</span>
    <span class="op">|&gt;</span> <span class="type">Router</span><span class="punct">.</span><span class="fn">get</span><span class="punct">(</span><span class="str">"/users"</span><span class="punct">,</span> <span class="fn">list_users</span><span class="punct">)</span>
    <span class="op">|&gt;</span> <span class="type">Router</span><span class="punct">.</span><span class="fn">get</span><span class="punct">(</span><span class="str">"/users/:id"</span><span class="punct">,</span> <span class="fn">get_user</span><span class="punct">)</span>
  <span class="type">Server</span><span class="punct">.</span><span class="fn">listen!</span><span class="punct">(</span><span class="fn">app</span><span class="punct">,</span> <span class="num">3000</span><span class="punct">)</span>`}</code></pre>
</section>

<section id="next">
	<h2>Next Steps</h2>
	<ul>
		<li><a href="/learn">Learn</a>: types, effects, syntax, and operators</li>
		<li><a href="/api">API</a>: every module and function</li>
		<li><a href="https://github.com/baseline-lang/baseline">Source</a>: contribute on GitHub</li>
	</ul>
</section>

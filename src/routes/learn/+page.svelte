<svelte:head>
	<title>Learn | Baseline</title>
	<meta name="description" content="Learn Baseline: types, effects, syntax, operators, and tooling." />
</svelte:head>

<nav class="on-this-page">
	<h4>On this page</h4>
	<ul>
		<li><a href="#core-types">Core Types</a></li>
		<li><a href="#refinements">Refinement Types</a></li>
		<li><a href="#sum-types">Sum Types</a></li>
		<li><a href="#records">Records</a></li>
		<li><a href="#tuples">Tuples</a></li>
		<li><a href="#effects">Effects</a></li>
		<li><a href="#effect-inference">Effect Inference</a></li>
		<li><a href="#transitive">Transitive Effects</a></li>
		<li><a href="#sandboxing">AI Sandboxing</a></li>
		<li><a href="#syntax">Syntax Summary</a></li>
		<li><a href="#operators">Operators</a></li>
		<li><a href="#modules">Modules</a></li>
		<li><a href="#tooling">Tooling</a></li>
		<li><a href="#not-supported">Not Supported</a></li>
	</ul>
</nav>

<h1>Learn</h1>
<p class="lead">
	Types, effects, syntax, operators, and the CLI.
</p>

<section id="core-types">
	<h2>Core Types</h2>
	<table>
		<thead>
			<tr><th>Type</th><th>Example</th><th>Notes</th></tr>
		</thead>
		<tbody>
			<tr><td><code>Int</code></td><td><code>42</code></td><td>64-bit integer</td></tr>
			<tr><td><code>String</code></td><td><code>"hello"</code></td><td>UTF-8, interpolation with <code>${"{}"}</code></td></tr>
			<tr><td><code>Boolean</code></td><td><code>true</code>, <code>false</code></td><td>Negation with <code>not</code>, not <code>!</code></td></tr>
			<tr><td><code>Unit</code></td><td><code>()</code></td><td>No meaningful value</td></tr>
			<tr><td><code>List&lt;T&gt;</code></td><td><code>[1, 2, 3]</code></td><td>Homogeneous, immutable</td></tr>
			<tr><td><code>Option&lt;T&gt;</code></td><td><code>Some(42)</code>, <code>None</code></td><td>Replaces null</td></tr>
			<tr><td><code>Result&lt;T,E&gt;</code></td><td><code>Ok(v)</code>, <code>Err(e)</code></td><td>Replaces exceptions</td></tr>
		</tbody>
	</table>
</section>

<section id="refinements">
	<h2>Refinement Types</h2>
	<p>
		A refinement type is a base type with a constraint the compiler proves
		at compile time. Define it once, and every function that takes that type
		can trust the constraint holds.
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Port</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;=</span> <span class="num">1</span> <span class="op">&amp;&amp;</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">65535</span>
<span class="kw">type</span> <span class="type">Percentage</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;=</span> <span class="num">0</span> <span class="op">&amp;&amp;</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">100</span>
<span class="kw">type</span> <span class="type">PositiveInt</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;</span> <span class="num">0</span>

<span class="kw">let</span> <span class="fn">port</span><span class="op">:</span> <span class="type">Port</span> <span class="op">=</span> <span class="num">8080</span>      <span class="comment">// OK</span>
<span class="kw">let</span> <span class="fn">bad</span><span class="op">:</span> <span class="type">Port</span> <span class="op">=</span> <span class="num">0</span>         <span class="comment">// Compile error</span>`}</code></pre>
	<p>
		<code>where</code> is only for refinements. You can use <code>&gt;</code>,
		<code>&gt;=</code>, <code>&lt;</code>, <code>&lt;=</code>, <code>==</code>,
		<code>!=</code>, and combine them with <code>&amp;&amp;</code>.
		No <code>||</code> yet. That's coming in v0.2.
	</p>
</section>

<section id="sum-types">
	<h2>Sum Types</h2>
	<p>
		A value can be one of several variants, each optionally carrying data.
		The compiler makes sure every <code>match</code> handles all of them.
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Shape</span> <span class="op">=</span>
  <span class="op">|</span> <span class="type">Circle</span><span class="punct">(</span><span class="type">Int</span><span class="punct">)</span>
  <span class="op">|</span> <span class="type">Rect</span><span class="punct">(</span><span class="type">Int</span><span class="punct">,</span> <span class="type">Int</span><span class="punct">)</span>
  <span class="op">|</span> <span class="type">Point</span>

<span class="kw">fn</span> <span class="fn">describe</span><span class="punct">(</span><span class="fn">s</span><span class="op">:</span> <span class="type">Shape</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">String</span> <span class="op">=</span>
  <span class="kw">match</span> <span class="fn">s</span>
    <span class="type">Circle</span><span class="punct">(</span><span class="fn">r</span><span class="punct">)</span>    <span class="op">-&gt;</span> <span class="str">"circle r=\${r}"</span>
    <span class="type">Rect</span><span class="punct">(</span><span class="fn">w</span><span class="punct">,</span> <span class="fn">h</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="str">"rect \${w}x\${h}"</span>
    <span class="type">Point</span>        <span class="op">-&gt;</span> <span class="str">"point"</span>`}</code></pre>
	<p>
		<code>Option&lt;T&gt;</code> and <code>Result&lt;T, E&gt;</code> are built-in
		sum types. No <code>null</code>, no <code>undefined</code>, no exceptions.
	</p>
</section>

<section id="records">
	<h2>Records</h2>
	<p>
		Records give you named fields, dot access, and an update syntax that
		creates a new record instead of mutating the old one.
	</p>
	<pre><code>{@html `<span class="kw">let</span> <span class="fn">user</span> <span class="op">=</span> { <span class="fn">name</span><span class="op">:</span> <span class="str">"Alice"</span><span class="punct">,</span> <span class="fn">age</span><span class="op">:</span> <span class="num">30</span> }

<span class="comment">// Dot access</span>
<span class="fn">user</span><span class="punct">.</span><span class="fn">name</span>  <span class="comment">// "Alice"</span>

<span class="comment">// Update syntax (creates a new record)</span>
<span class="kw">let</span> <span class="fn">older</span> <span class="op">=</span> { ..<span class="fn">user</span><span class="punct">,</span> <span class="fn">age</span><span class="op">:</span> <span class="num">31</span> }

<span class="comment">// Named record types</span>
<span class="kw">type</span> <span class="type">User</span> <span class="op">=</span> { <span class="fn">name</span><span class="op">:</span> <span class="type">String</span><span class="punct">,</span> <span class="fn">age</span><span class="op">:</span> <span class="type">Int</span> }
<span class="kw">let</span> <span class="fn">bob</span> <span class="op">=</span> <span class="type">User</span> { <span class="fn">name</span><span class="op">:</span> <span class="str">"Bob"</span><span class="punct">,</span> <span class="fn">age</span><span class="op">:</span> <span class="num">25</span> }`}</code></pre>
</section>

<section id="tuples">
	<h2>Tuples</h2>
	<p>
		Tuples hold mixed types. Destructure them with <code>let</code>.
	</p>
	<pre><code>{@html `<span class="kw">let</span> <span class="fn">pair</span> <span class="op">=</span> <span class="punct">(</span><span class="num">1</span><span class="punct">,</span> <span class="str">"hello"</span><span class="punct">)</span>
<span class="kw">let</span> <span class="punct">(</span><span class="fn">n</span><span class="punct">,</span> <span class="fn">s</span><span class="punct">)</span> <span class="op">=</span> <span class="fn">pair</span>`}</code></pre>
</section>

<section id="effects">
	<h2>Effects</h2>
	<p>
		Side effects are declared in the type signature. The <code>!</code> suffix
		marks a function as effectful, and the curly braces say which effects it uses.
		The compiler enforces this, not a linter.
	</p>
	<pre><code>{@html `<span class="comment">// Pure: no effects, can be called from anywhere</span>
<span class="kw">fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Effectful: requires {Console}</span>
<span class="kw">fn</span> <span class="fn">log_add!</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="type">Int</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"\${a} + \${b}"</span><span class="punct">)</span>
  <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Multiple effects: must declare all of them</span>
<span class="kw">fn</span> <span class="fn">fetch_and_log!</span><span class="punct">(</span><span class="fn">url</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Http, Console}</span> <span class="type">String</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">body</span> <span class="op">=</span> <span class="type">Http</span><span class="punct">.</span><span class="fn">get!</span><span class="punct">(</span><span class="fn">url</span><span class="punct">)</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">body</span><span class="punct">)</span>
  <span class="fn">body</span>`}</code></pre>

	<h3>Built-in Effects</h3>
	<table>
		<thead>
			<tr><th>Effect</th><th>Capability</th><th>Notes</th></tr>
		</thead>
		<tbody>
			<tr><td><code>Console</code></td><td>Terminal I/O</td><td>print, read</td></tr>
			<tr><td><code>Http</code></td><td>Network requests and servers</td><td></td></tr>
			<tr><td><code>Fs</code></td><td>Filesystem read/write</td><td></td></tr>
			<tr><td><code>Random</code></td><td>Random number generation</td><td>Must declare</td></tr>
			<tr><td><code>Env</code></td><td>Environment variables</td><td></td></tr>
			<tr><td><code>Sqlite</code></td><td>Sqlite database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Postgres</code></td><td>Postgres database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Mysql</code></td><td>Mysql database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Log</code></td><td>Structured logging</td><td>Ambient (no declaration needed)</td></tr>
			<tr><td><code>Time</code></td><td>Clock and timing</td><td>Ambient (no declaration needed)</td></tr>
		</tbody>
	</table>
	<p>
		<code>Log</code> and <code>Time</code> are <strong>ambient</strong>: you don't
		need to declare them. Everything else must appear in the effect set.
	</p>
</section>

<section id="effect-inference">
	<h2>Effect Inference</h2>
	<p>
		If you leave off the effect annotation, the compiler infers it from the
		function body. Only explicit annotations get checked against.
	</p>
	<pre><code>{@html `<span class="comment">// No annotation: effects inferred as {Console}</span>
<span class="kw">fn</span> <span class="fn">helper!</span><span class="punct">(</span><span class="fn">msg</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">msg</span><span class="punct">)</span>

<span class="comment">// Explicit annotation: compiler checks it</span>
<span class="kw">fn</span> <span class="fn">main!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="fn">helper!</span><span class="punct">(</span><span class="str">"hello"</span><span class="punct">)</span>`}</code></pre>
	<p>
		In practice: skip annotations on internal helpers, add them at public
		boundaries.
	</p>
</section>

<section id="transitive">
	<h2>Transitive Effects</h2>
	<p>
		Effects bubble up. If <code>foo!</code> calls <code>bar!</code>, then
		<code>foo</code> must declare everything <code>bar</code> uses too.
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">bar!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Http}</span> <span class="type">String</span> <span class="op">=</span>
  <span class="type">Http</span><span class="punct">.</span><span class="fn">get!</span><span class="punct">(</span><span class="str">"/data"</span><span class="punct">)</span>

<span class="comment">// Must include {Http} because bar! requires it</span>
<span class="kw">fn</span> <span class="fn">foo!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Http, Console}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">data</span> <span class="op">=</span> <span class="fn">bar!</span><span class="punct">()</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">data</span><span class="punct">)</span>`}</code></pre>
</section>

<section id="sandboxing">
	<h2>AI Sandboxing</h2>
	<p>
		You can use effects to sandbox AI-generated code. Grant
		<code>{"{Console}"}</code> for output but withhold <code>{"{Fs}"}</code>
		and <code>{"{Http}"}</code>. If the generated code tries to use an
		effect you didn't grant, the compiler rejects it before anything runs.
	</p>
</section>

<section id="syntax">
	<h2>Syntax Summary</h2>
	<pre><code>{@html `<span class="comment">// Functions</span>
<span class="kw">fn</span> <span class="fn">name</span><span class="punct">(</span><span class="fn">x</span><span class="op">:</span> <span class="type">Type</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">ReturnType</span> <span class="op">=</span> <span class="fn">body</span>
<span class="kw">fn</span> <span class="fn">effectful!</span><span class="punct">(</span><span class="fn">x</span><span class="op">:</span> <span class="type">Type</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Effect}</span> <span class="type">ReturnType</span> <span class="op">=</span> <span class="fn">body</span>

<span class="comment">// Variables</span>
<span class="kw">let</span> <span class="fn">x</span> <span class="op">=</span> <span class="num">42</span>
<span class="kw">let</span> <span class="fn">y</span><span class="op">:</span> <span class="type">String</span> <span class="op">=</span> <span class="str">"hello"</span>

<span class="comment">// Control flow</span>
<span class="kw">if</span> <span class="fn">condition</span> <span class="kw">then</span> <span class="fn">a</span> <span class="kw">else</span> <span class="fn">b</span>
<span class="kw">match</span> <span class="fn">value</span>
  <span class="type">Pattern</span> <span class="op">-&gt;</span> <span class="fn">result</span>

<span class="comment">// Types</span>
<span class="kw">type</span> <span class="type">Name</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;</span> <span class="num">0</span>
<span class="kw">type</span> <span class="type">Sum</span> <span class="op">=</span> <span class="op">|</span> <span class="type">A</span> <span class="op">|</span> <span class="type">B</span><span class="punct">(</span><span class="type">Int</span><span class="punct">)</span>

<span class="comment">// Pipes</span>
<span class="fn">x</span> <span class="op">|&gt;</span> <span class="fn">f</span> <span class="op">|&gt;</span> <span class="fn">g</span>

<span class="comment">// Lambdas</span>
<span class="op">|</span><span class="fn">x</span><span class="op">|</span> <span class="fn">x</span> <span class="op">+</span> <span class="num">1</span>
<span class="op">|</span><span class="fn">x</span><span class="punct">,</span> <span class="fn">y</span><span class="op">|</span> <span class="fn">x</span> <span class="op">*</span> <span class="fn">y</span>

<span class="comment">// Error propagation</span>
<span class="kw">let</span> <span class="fn">value</span> <span class="op">=</span> <span class="fn">may_fail</span><span class="punct">()</span><span class="op">?</span>

<span class="comment">// Tests</span>
<span class="annot">@test</span>
<span class="kw">test</span> <span class="str">"name"</span> <span class="op">=</span> <span class="fn">expr</span> <span class="op">==</span> <span class="fn">expected</span>

<span class="comment">// Modules</span>
<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">script</span><span class="punct">)</span>
<span class="annot">@module</span> <span class="type">MyModule</span>
<span class="kw">import</span> <span class="type">OtherModule</span>`}</code></pre>
</section>

<section id="operators">
	<h2>Operators</h2>
	<table>
		<tbody>
			<tr><td><code>+</code> <code>-</code> <code>*</code> <code>/</code> <code>%</code></td><td>Arithmetic</td></tr>
			<tr><td><code>==</code> <code>!=</code></td><td>Equality</td></tr>
			<tr><td><code>&lt;</code> <code>&lt;=</code> <code>&gt;</code> <code>&gt;=</code></td><td>Ordering</td></tr>
			<tr><td><code>&amp;&amp;</code> <code>||</code></td><td>Short-circuit logical</td></tr>
			<tr><td><code>not</code></td><td>Negation (keyword, not <code>!</code>)</td></tr>
			<tr><td><code>|&gt;</code></td><td>Pipe</td></tr>
			<tr><td><code>?</code></td><td>Error propagation</td></tr>
			<tr><td><code>..</code></td><td>Range (<code>1..10</code>)</td></tr>
		</tbody>
	</table>
</section>

<section id="modules">
	<h2>Modules</h2>
	<p>
		Functions live in modules. You call them with <code>Module.function(args)</code>.
		There's no <code>value.method()</code>.
	</p>
	<pre><code>{@html `<span class="comment">// Module.function(value) is the only call style</span>
<span class="type">String</span><span class="punct">.</span><span class="fn">to_upper</span><span class="punct">(</span><span class="str">"hello"</span><span class="punct">)</span>   <span class="comment">// "HELLO"</span>
<span class="type">List</span><span class="punct">.</span><span class="fn">length</span><span class="punct">(</span>[<span class="num">1</span><span class="punct">,</span> <span class="num">2</span><span class="punct">,</span> <span class="num">3</span>]<span class="punct">)</span>   <span class="comment">// 3</span>

<span class="comment">// Prelude levels control which modules are available</span>
<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">script</span><span class="punct">)</span>   <span class="comment">// Console, String, List, etc.</span>
<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">server</span><span class="punct">)</span>   <span class="comment">// + Router, Server, Sqlite, etc.</span>`}</code></pre>
</section>

<section id="tooling">
	<h2>Tooling</h2>
	<pre><code>{@html `<span class="comment"># Run a program</span>
blc run app.bl

<span class="comment"># Type check with structured output</span>
blc check app.bl --json

<span class="comment"># Run tests with structured output</span>
blc test app.bl --json

<span class="comment"># Look up API docs</span>
blc docs List.map
blc docs --search "filter" --json

<span class="comment"># Verification levels</span>
blc check app.bl --json --level types        <span class="comment"># Fast (~ms)</span>
blc check app.bl --json --level refinements  <span class="comment"># Default (~100ms)</span>
blc check app.bl --json --level full         <span class="comment"># SMT (~seconds)</span>`}</code></pre>
</section>

<section id="not-supported">
	<h2>Not Supported</h2>
	<p>
		These don't exist in Baseline. The compiler rejects them and tells
		you what to use instead.
	</p>
	<ul>
		<li>No <code>class</code> / <code>extends</code> / <code>implements</code>: use records + sum types + effects</li>
		<li>No <code>try</code> / <code>catch</code> / <code>throw</code>: use <code>Result&lt;T, E&gt;</code> with <code>?</code></li>
		<li>No <code>null</code> / <code>undefined</code> / <code>nil</code>: use <code>Option&lt;T&gt;</code></li>
		<li>No <code>async</code> / <code>await</code>: effects handle this</li>
		<li>No <code>+</code> for string concatenation: use <code>"$&#123;a&#125;$&#123;b&#125;"</code></li>
		<li>No <code>!</code> for boolean negation: use <code>not</code></li>
		<li>No <code>value.method()</code>: use <code>Module.method(value)</code></li>
		<li>No mutable variables, no <code>return</code> keyword, no semicolons</li>
	</ul>
</section>

<section id="resources">
	<h2>Resources</h2>
	<ul>
		<li><a href="https://github.com/baseline-lang/baseline">Source code</a>: GitHub</li>
		<li><a href="/llms.txt">llms.txt</a>: compact reference for AI agent context windows</li>
		<li><a href="/guide">Quickstart</a>: from install to web server</li>
		<li><a href="/api">API Reference</a>: every module and function</li>
	</ul>
</section>

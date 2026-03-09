<svelte:head>
	<title>Cheatsheet | Baseline</title>
	<meta name="description" content="Baseline language quick reference: syntax, operators, modules, and tooling." />
</svelte:head>

<h1>Cheatsheet</h1>
<p class="lead">
	Everything on one page. Syntax, operators, standard library, CLI.
</p>

<section id="syntax">
	<h2>Syntax Summary</h2>
	<pre><code>{@html `<span class="comment">-- Functions</span>
<span class="kw">fn</span> <span class="fn">name</span><span class="punct">(</span><span class="fn">x</span><span class="op">:</span> <span class="type">Type</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">ReturnType</span> <span class="op">=</span> <span class="fn">body</span>
<span class="kw">fn</span> <span class="fn">effectful!</span><span class="punct">(</span><span class="fn">x</span><span class="op">:</span> <span class="type">Type</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Effect}</span> <span class="type">ReturnType</span> <span class="op">=</span> <span class="fn">body</span>

<span class="comment">-- Variables</span>
<span class="kw">let</span> <span class="fn">x</span> <span class="op">=</span> <span class="num">42</span>
<span class="kw">let</span> <span class="fn">y</span><span class="op">:</span> <span class="type">String</span> <span class="op">=</span> <span class="str">"hello"</span>

<span class="comment">-- Control flow</span>
<span class="kw">if</span> <span class="fn">condition</span> <span class="kw">then</span> <span class="fn">a</span> <span class="kw">else</span> <span class="fn">b</span>
<span class="kw">match</span> <span class="fn">value</span>
  <span class="type">Pattern</span> <span class="op">-&gt;</span> <span class="fn">result</span>

<span class="comment">-- Types</span>
<span class="kw">type</span> <span class="type">Name</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;</span> <span class="num">0</span>
<span class="kw">type</span> <span class="type">Sum</span> <span class="op">=</span> <span class="op">|</span> <span class="type">A</span> <span class="op">|</span> <span class="type">B</span><span class="punct">(</span><span class="type">Int</span><span class="punct">)</span>

<span class="comment">-- Pipes</span>
<span class="fn">x</span> <span class="op">|&gt;</span> <span class="fn">f</span> <span class="op">|&gt;</span> <span class="fn">g</span>

<span class="comment">-- Lambdas</span>
<span class="op">|</span><span class="fn">x</span><span class="op">|</span> <span class="fn">x</span> <span class="op">+</span> <span class="num">1</span>
<span class="op">|</span><span class="fn">x</span><span class="punct">,</span> <span class="fn">y</span><span class="op">|</span> <span class="fn">x</span> <span class="op">*</span> <span class="fn">y</span>

<span class="comment">-- Error propagation</span>
<span class="kw">let</span> <span class="fn">value</span> <span class="op">=</span> <span class="fn">may_fail</span><span class="punct">()</span><span class="op">?</span>

<span class="comment">-- Tests</span>
<span class="annot">@test</span>
<span class="kw">test</span> <span class="str">"name"</span> <span class="op">=</span> <span class="fn">expr</span> <span class="op">==</span> <span class="fn">expected</span>

<span class="comment">-- Modules</span>
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
	<h2>Standard Library</h2>
	<h4>Language (pure prelude)</h4>
	<table>
		<tbody>
			<tr><td><code>List</code></td><td>map, filter, fold, head, tail, length, find, flatten, zip, sort</td></tr>
			<tr><td><code>String</code></td><td>length, to_upper, to_lower, trim, split, contains, slice, starts_with</td></tr>
			<tr><td><code>Int</code></td><td>parse, abs, min, max, to_string</td></tr>
			<tr><td><code>Option</code></td><td>map, unwrap, is_some, is_none, or_else</td></tr>
			<tr><td><code>Result</code></td><td>map, unwrap, is_ok, is_err, or_else</td></tr>
			<tr><td><code>Map</code></td><td>new, get, set, has, delete, keys, values, size</td></tr>
		</tbody>
	</table>
	<h4>Server (server prelude)</h4>
	<table>
		<tbody>
			<tr><td><code>Router</code></td><td>new, get, post, put, delete, patch, any, group, resources, use</td></tr>
			<tr><td><code>Server</code></td><td>listen!</td></tr>
			<tr><td><code>Request</code></td><td>method, header, body_json, param, query, state</td></tr>
			<tr><td><code>Response</code></td><td>ok, json, status, redirect, not_found, with_header</td></tr>
			<tr><td><code>Sqlite</code></td><td>connect!, query!, execute!</td></tr>
			<tr><td><code>Postgres</code></td><td>connect!, query!, execute!</td></tr>
		</tbody>
	</table>
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
		<li>No <code>class</code> / <code>extends</code> / <code>implements</code> — use records + sum types + effects</li>
		<li>No <code>try</code> / <code>catch</code> / <code>throw</code> — use <code>Result&lt;T, E&gt;</code> with <code>?</code></li>
		<li>No <code>null</code> / <code>undefined</code> / <code>nil</code> — use <code>Option&lt;T&gt;</code></li>
		<li>No <code>async</code> / <code>await</code> — effects handle this</li>
		<li>No <code>+</code> for string concatenation — use <code>"$&#123;a&#125;$&#123;b&#125;"</code></li>
		<li>No <code>!</code> for boolean negation — use <code>not</code></li>
		<li>No <code>value.method()</code> — use <code>Module.method(value)</code></li>
		<li>No mutable variables, no <code>return</code> keyword, no semicolons</li>
	</ul>
</section>

<section id="resources">
	<h2>Resources</h2>
	<ul>
		<li><a href="https://github.com/baseline-lang/baseline">Source code</a> — GitHub</li>
		<li><a href="/llms.txt">llms.txt</a> — compact reference for AI agent context windows</li>
		<li><a href="/guide">Quickstart</a> — from install to web server</li>
	</ul>
</section>

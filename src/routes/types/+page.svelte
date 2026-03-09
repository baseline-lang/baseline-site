<svelte:head>
	<title>Types | Baseline</title>
	<meta name="description" content="Baseline's type system: refinements, sum types, records, Option, Result, and pattern matching." />
</svelte:head>

<h1>Types</h1>
<p class="lead">
	Baseline's types do more than prevent mismatches. Refinements prove
	constraints at compile time, and sum types make illegal states unrepresentable.
</p>

<section id="core">
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

<span class="kw">let</span> <span class="fn">port</span><span class="op">:</span> <span class="type">Port</span> <span class="op">=</span> <span class="num">8080</span>      <span class="comment">-- OK</span>
<span class="kw">let</span> <span class="fn">bad</span><span class="op">:</span> <span class="type">Port</span> <span class="op">=</span> <span class="num">0</span>         <span class="comment">-- Compile error</span>`}</code></pre>
	<p>
		<code>where</code> is only for refinements. You can use <code>&gt;</code>,
		<code>&gt;=</code>, <code>&lt;</code>, <code>&lt;=</code>, <code>==</code>,
		<code>!=</code>, and combine them with <code>&amp;&amp;</code>.
		No <code>||</code> yet — that's coming in v0.2.
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

<span class="comment">-- Dot access</span>
<span class="fn">user</span><span class="punct">.</span><span class="fn">name</span>  <span class="comment">-- "Alice"</span>

<span class="comment">-- Update syntax (creates a new record)</span>
<span class="kw">let</span> <span class="fn">older</span> <span class="op">=</span> { ..<span class="fn">user</span><span class="punct">,</span> <span class="fn">age</span><span class="op">:</span> <span class="num">31</span> }

<span class="comment">-- Named record types</span>
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

<section id="modules">
	<h2>Modules</h2>
	<p>
		Functions live in modules. You call them with <code>Module.function(args)</code>.
		There's no <code>value.method()</code>.
	</p>
	<pre><code>{@html `<span class="comment">-- Module.function(value) is the only call style</span>
<span class="type">String</span><span class="punct">.</span><span class="fn">to_upper</span><span class="punct">(</span><span class="str">"hello"</span><span class="punct">)</span>   <span class="comment">-- "HELLO"</span>
<span class="type">List</span><span class="punct">.</span><span class="fn">length</span><span class="punct">(</span>[<span class="num">1</span><span class="punct">,</span> <span class="num">2</span><span class="punct">,</span> <span class="num">3</span>]<span class="punct">)</span>   <span class="comment">-- 3</span>

<span class="comment">-- Prelude levels control which modules are available</span>
<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">script</span><span class="punct">)</span>   <span class="comment">-- Console, String, List, etc.</span>
<span class="annot">@prelude</span><span class="punct">(</span><span class="fn">server</span><span class="punct">)</span>   <span class="comment">-- + Router, Server, Sqlite, etc.</span>`}</code></pre>
</section>

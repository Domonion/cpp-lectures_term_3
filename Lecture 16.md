---


---

<h2 id="о-фичах">О фичах</h2>
<p><strong>Rvalue references for this</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> t <span class="token punctuation">{</span>
	<span class="token keyword">void</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// ... может быть и lvalue и rvalue</span>

<span class="token keyword">struct</span> t <span class="token punctuation">{</span>
	<span class="token keyword">void</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">&amp;</span><span class="token punctuation">;</span> <span class="token comment">//this только lvalue</span>
	<span class="token keyword">void</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">&amp;&amp;</span><span class="token punctuation">;</span> <span class="token comment">//только rvalue</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Non-static member initializers</strong><br>
Говорит просто во всех конструкторах. в которых не указано - скажи равно этому</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> point <span class="token punctuation">{</span>
	<span class="token keyword">float</span> x <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">.</span>f<span class="token punctuation">;</span>
	<span class="token keyword">float</span> y <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">.</span>f<span class="token punctuation">;</span> 
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Right angle brackets</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">vector<span class="token operator">&lt;</span>vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;&gt;</span> b <span class="token comment">// &gt;&gt; раньше было сдвигом вправо</span>
</code></pre>
<p><strong>Extern templates</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">struct</span> basic_string <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">extern</span> <span class="token keyword">template</span> <span class="token keyword">struct</span> basic_string<span class="token operator">&lt;</span><span class="token keyword">char</span><span class="token operator">&gt;</span><span class="token punctuation">;</span> <span class="token comment">//Подавление инстанцирования</span>
</code></pre>
<p><strong>Strongly-typed enums</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">enum</span> color <span class="token punctuation">{</span>
	red<span class="token punctuation">,</span> green<span class="token punctuation">,</span> blue
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">enum</span> state <span class="token punctuation">{</span>
	initial<span class="token punctuation">,</span> waiting<span class="token punctuation">,</span> running
<span class="token punctuation">}</span><span class="token punctuation">;</span>

state <span class="token function">next</span><span class="token punctuation">(</span>state s<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>

color n <span class="token operator">=</span> <span class="token function">next</span><span class="token punctuation">(</span>red<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Неявное преобразование - оч плохо</span>
</code></pre>
<p>Нельзя поменять <code>enum</code> сам по себе, т.к сломается си’шный код</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">enum</span> <span class="token keyword">class</span> <span class="token class-name">color</span><span class="token operator">:</span> uint8_t <span class="token punctuation">{</span> <span class="token comment">// Явно указать на базе какого типа</span>
	red<span class="token punctuation">,</span> green<span class="token punctuation">,</span> blue
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">enum</span> <span class="token keyword">class</span> <span class="token class-name">state</span> <span class="token punctuation">{</span>
	initial<span class="token punctuation">,</span> waiting<span class="token punctuation">,</span> running
<span class="token punctuation">}</span><span class="token punctuation">;</span>

color n <span class="token operator">=</span> <span class="token function">next</span><span class="token punctuation">(</span>color<span class="token operator">::</span>red<span class="token punctuation">)</span><span class="token punctuation">;</span> 
</code></pre>
<p>В такие <code>enum</code>'ах енумераторы находятся в <code>namespace</code> енума. По умолчанию нельзя применять операторы, нет неявного преобразования между собой и в <code>int</code><br>
Можно перегрузить операторы.<br>
Из-за возможности указать underline type появилась возможность forwarding declaration</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">enum</span> <span class="token keyword">class</span> <span class="token class-name">msg_id</span><span class="token operator">:</span> uint32_t<span class="token punctuation">;</span>
</code></pre>


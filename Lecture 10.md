---


---

<h3 id="optional">optional</h3>
<p>Используется когда хотим хранить что-то, или ничего (специальное значение)</p>
<pre class=" language-cpp"><code class="prism  language-cpp">optional<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> a<span class="token punctuation">;</span>
a <span class="token operator">=</span> <span class="token number">5</span><span class="token punctuation">;</span>
a <span class="token operator">=</span> nullopt<span class="token punctuation">;</span>

<span class="token keyword">if</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token comment">// есть ли там что-то</span>

<span class="token keyword">struct</span> foo <span class="token punctuation">{</span>
	<span class="token function">foo</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

optional<span class="token operator">&lt;</span>foo<span class="token operator">&gt;</span> <span class="token function">a</span><span class="token punctuation">(</span>in_place<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Создастся, вызвав конструктор</span>
a<span class="token punctuation">.</span><span class="token function">emplace</span><span class="token punctuation">(</span><span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="variant">Variant</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">variant<span class="token operator">&lt;</span>A<span class="token punctuation">,</span> B<span class="token punctuation">,</span> C<span class="token operator">&gt;</span> v<span class="token punctuation">;</span>
</code></pre>
<p>Хранит один из элементов типа <code>A</code>,  <code>B</code> или <code>C</code> (или больше) (конкретнее - типобезопасный union)<br>
По умолчанию имеет значение первого типа.</p>


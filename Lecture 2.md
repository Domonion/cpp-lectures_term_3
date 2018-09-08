---


---

<h2 id="multi-index-containers">Multi-index containers</h2>
<p><strong>bimap</strong> - пары (a, b), по элементу можно найти второй<br>
<strong>lru-cache</strong><br>
<strong>boost::multi_index</strong> - контейнер с определенным количеством индексов</p>
<h2 id="heterogeneous-lookup">Heterogeneous lookup</h2>
<p>Позволяет искать (в контейнере?)  не только по испомомо типу (н-р вместо <code>string</code> передавать <code>const char*</code>)</p>
<p>С помощью него же в массиве &lt;ключ, значение&gt; искать только по ключу</p>
<p><strong>TODO</strong> в каких контейнерах он есть</p>
<h2 id="shared_pointer">Shared_pointer</h2>
<p>Много указателей - объект один<br>
Все аккуратно удаляется</p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">p</span><span class="token punctuation">(</span><span class="token keyword">new</span> my_type<span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> q <span class="token operator">=</span> p<span class="token punctuation">;</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</code></pre>
<p>Нельзя хранить счетчик в самом поинтере - будет у каждого свой<br>
Выход - храним отдельный <code>shared_counter</code> и на него ссылаемся</p>
<pre class=" language-cpp"><code class="prism \ language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">struct</span> shared_ptr <span class="token punctuation">{</span>
	T<span class="token operator">*</span> obj<span class="token punctuation">;</span>
	size_t<span class="token operator">*</span> count<span class="token punctuation">;</span>

	<span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> D<span class="token operator">&gt;</span>
	<span class="token function">shared_ptr</span> <span class="token punctuation">(</span>T<span class="token operator">*</span> ptr<span class="token punctuation">,</span> D deleter<span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token function">obj</span><span class="token punctuation">(</span>ptr<span class="token punctuation">)</span><span class="token punctuation">,</span> 
						<span class="token keyword">try</span> <span class="token punctuation">{</span>
			 				count <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token function">size_t</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
						<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
							<span class="token function">deleter</span><span class="token punctuation">(</span>ptr<span class="token punctuation">)</span><span class="token punctuation">;</span>
							<span class="token keyword">throw</span><span class="token punctuation">;</span>
						<span class="token punctuation">}</span>
	<span class="token punctuation">{</span><span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

my_type <span class="token operator">*</span>t <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token function">my_type</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">p</span><span class="token punctuation">(</span>t<span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">q</span><span class="token punctuation">(</span>t<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// Очень плохо - 2 разных счетчика</span>
</code></pre>
<p><code>deleter</code> нужен, если мы хотим н-р вернуть объект в пулл, а не удалить его, когда все указатели удалятся</p>
<pre class=" language-cpp"><code class="prism  language-cpp">
<span class="token keyword">struct</span> wheel <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">struct</span> car <span class="token punctuation">{</span>
	array<span class="token operator">&lt;</span>weel<span class="token punctuation">,</span> <span class="token number">4</span><span class="token operator">&gt;</span> wheels<span class="token punctuation">;</span> <span class="token comment">//Беда</span>
	array<span class="token operator">&lt;</span>shared_ptr<span class="token operator">&lt;</span>wheel<span class="token operator">&gt;</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token operator">&gt;</span> wheels<span class="token punctuation">;</span> <span class="token comment">//Норм?</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Хотим <code>shared_ptr</code> на wheel</p>
<p>Что плохого может быть? Если мы откуда-то ссылаемся на объект, то это не значит что он должен жить. Н-р удалили машину, а колесо не знает что она удалена и продолжает жить</p>
<p><strong>Alliasing constructor</strong><br>
Выход - сделать общий счетчик у <code>car</code> и у <code>wheel</code>, а само <code>wheel</code> хранить внутри <code>car</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>car<span class="token operator">&gt;</span> p<span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>wheel<span class="token operator">&gt;</span> <span class="token function">q</span><span class="token punctuation">(</span>p<span class="token punctuation">,</span> <span class="token operator">&amp;</span>p<span class="token operator">-</span><span class="token operator">&gt;</span>wheels<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Идиоматическое использование <code>shared_ptr</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> q <span class="token operator">=</span> make_shared<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span>
</code></pre>
<p>Такое использование не делает 2 аллокации (на <code>counter</code> и <code>obj</code>), а сделает один общий объект -&gt; 1 аллокация</p>


---


---

<h2 id="фичи">Фичи?</h2>
<h3 id="auto">Auto</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>std<span class="token operator">::</span>string<span class="token operator">&gt;</span> v<span class="token punctuation">;</span>

<span class="token keyword">for</span><span class="token punctuation">(</span>std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>std<span class="token operator">::</span>string<span class="token operator">&gt;</span><span class="token operator">::</span>iterator i <span class="token operator">=</span> v<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i <span class="token operator">!=</span> v<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">auto</span> i <span class="token operator">=</span> v<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i <span class="token operator">!=</span> v<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
</code></pre>
<p><code>auto</code> выводится как шаблоны</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">auto</span> a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">b</span><span class="token punctuation">(</span>T<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">auto</span><span class="token operator">&amp;</span> a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">b</span><span class="token punctuation">(</span>T<span class="token operator">&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Чтобы не дублировать типы можно <code>typedef</code>'ать</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">typedef</span> std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>std<span class="token operator">::</span>string<span class="token operator">&gt;</span> strings<span class="token punctuation">;</span>
</code></pre>
<h3 id="анонимные-функции">Анонимные функции</h3>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> It<span class="token punctuation">,</span> <span class="token keyword">typename</span> Comp<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">sort</span><span class="token punctuation">(</span>It first<span class="token punctuation">,</span> It last<span class="token punctuation">,</span> Comp comp<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token function">comp</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>


<span class="token keyword">bool</span> <span class="token function">abs_less</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">abs</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token operator">&lt;</span> <span class="token function">abs</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">struct</span> abs_less1 <span class="token punctuation">{</span>
	<span class="token keyword">bool</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token function">abs</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token operator">&lt;</span> <span class="token function">abs</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token function">sort</span><span class="token punctuation">(</span>v<span class="token punctuation">,</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> v<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> abs_less<span class="token operator">/</span>abs_less1<span class="token operator">/</span>std<span class="token operator">::</span>less<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Начиная с <code>c++11</code> появились анонимные функции (лямбды) - это по сути функциональные объекты, которые за нас сгенерит компилятор</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">abs</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token operator">&lt;</span> <span class="token function">abs</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>В <code>[]</code> указывается, что лямбда должна захватывать из контекста<br>
Пусть мы ходим сравнивать по модулю</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> mod_less <span class="token punctuation">{</span>
	<span class="token function">mod_less</span><span class="token punctuation">(</span><span class="token keyword">int</span> n<span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token function">n</span><span class="token punctuation">(</span>n<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
	<span class="token keyword">bool</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> a <span class="token operator">%</span> n <span class="token operator">&lt;</span> b <span class="token operator">%</span> n<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">int</span> n<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">int</span> k<span class="token punctuation">;</span>
<span class="token function">sort</span><span class="token punctuation">(</span>v<span class="token punctuation">,</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> v<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">mod_less</span><span class="token punctuation">(</span>k<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Т.е нужны еще какие-то локальные данные. Тогда лямбда выглядит так:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span>k<span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> a <span class="token operator">%</span> k<span class="token punctuation">,</span> b <span class="token operator">%</span> k<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>

<table>
<thead>
<tr>
<th>Что можно</th>
<th>Какой эффект</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>[a, b, c]</code></td>
<td>Берутся следующие переменные по значению</td>
</tr>
<tr>
<td><code>[&amp;d, &amp;e, &amp;f]</code></td>
<td>Берутся по ссылке</td>
</tr>
<tr>
<td><code>[=, &amp;d, &amp;e, &amp;f]</code></td>
<td>Все по значению, а какие-то конкретные по ссылке</td>
</tr>
<tr>
<td><code>[&amp;, a, b, c]</code></td>
<td>Все по ссылке, а какие-то конкретные по значению</td>
</tr>
<tr>
<td><code>[this]</code></td>
<td><code>this</code> - объемлющего чувака</td>
</tr>
</tbody>
</table><p>В следующем примере тип не может быть выведен тип, поскольку тип выводится по первому <code>return</code>, а для остальных проверяет, что он совпадает ( для лямбд работает так же)</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">auto</span> <span class="token function">fractional</span> <span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span><span class="token punctuation">(</span>a <span class="token operator">!=</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token function">fractional</span><span class="token punctuation">(</span>a <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token number">1</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Иногда в лямбдах хочется указать тип возвращаемого значения</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">bool</span> <span class="token comment">//Возвращает bool</span>
</code></pre>
<p>Иногда хочется, чтобы лямбда была не <code>const</code> (<code>operator()</code> не <code>const</code>)</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">mutable</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">bool</span><span class="token punctuation">;</span>
</code></pre>
<p>А еще так можно сделать</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">auto</span> f <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>И так, тогда это будет шаблонный функциональный объект</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">auto</span> a<span class="token punctuation">,</span> <span class="token keyword">auto</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">abs</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token operator">&lt;</span> <span class="token function">abs</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>Что делать если хочется что-то такое?</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">auto</span> cmp<span class="token punctuation">;</span>
<span class="token keyword">if</span> <span class="token punctuation">(</span>flag<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	cmp <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token keyword">return</span> a <span class="token operator">&lt;</span> b<span class="token punctuation">;</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
	cmp <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token keyword">return</span> b <span class="token operator">&lt;</span> a<span class="token punctuation">;</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Объявим <code>cmp</code> как</p>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>function<span class="token operator">&lt;</span><span class="token keyword">bool</span> <span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token operator">&gt;</span> cmp<span class="token punctuation">;</span>
</code></pre>
<p><strong>Оффтоп</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">bool</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">bool</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">bool</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>f<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>g<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>h<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">struct</span> F<span class="token operator">/</span>G<span class="token operator">/</span>H <span class="token punctuation">{</span>
	<span class="token keyword">bool</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">}</span>
<span class="token punctuation">}</span>
<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token function">F</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token function">G</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">sort</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token function">H</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Второе работает быстрее (?), потому что новая структура - новый тип, новый сорт, можно инлайнить, а тип функций (в примере) одинаковый.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">for</span><span class="token punctuation">(</span><span class="token punctuation">;</span><span class="token punctuation">;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span><span class="token punctuation">(</span>cmp <span class="token operator">==</span> <span class="token operator">&amp;</span>f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token keyword">if</span><span class="token punctuation">(</span>cmp <span class="token operator">==</span> <span class="token operator">&amp;</span>g<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">g</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
		<span class="token function">cmp</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

Перепишется в 
<span class="token keyword">if</span> <span class="token punctuation">(</span>cmp <span class="token operator">==</span> <span class="token operator">&amp;</span>f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token punctuation">;</span><span class="token punctuation">;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token keyword">if</span> <span class="token punctuation">(</span>cmp <span class="token operator">==</span> <span class="token operator">&amp;</span>g<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token punctuation">;</span><span class="token punctuation">;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">g</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
	<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token punctuation">;</span><span class="token punctuation">;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">cmp</span><span class="token punctuation">(</span>a<span class="token punctuation">,</span> b<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>


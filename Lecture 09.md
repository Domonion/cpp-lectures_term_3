---


---

<h2 id="еще-о-фичах">Еще о фичах</h2>
<h3 id="decltype">Decltype</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token keyword">int</span> <span class="token keyword">float</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">char</span> <span class="token function">g</span><span class="token punctuation">(</span>string<span class="token operator">&amp;</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
T<span class="token operator">*</span> <span class="token function">g</span><span class="token punctuation">(</span>T<span class="token punctuation">,</span> T<span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token operator">?</span><span class="token operator">?</span><span class="token operator">?</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Что ставить на место <code>???</code></p>
<p>С простыми типами можно:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">typename</span> std<span class="token operator">::</span>common_type<span class="token operator">&lt;</span>U<span class="token punctuation">,</span> V<span class="token operator">&gt;</span><span class="token operator">::</span>type <span class="token function">max</span><span class="token punctuation">(</span>U <span class="token keyword">const</span><span class="token operator">&amp;</span> u<span class="token punctuation">,</span> V <span class="token keyword">const</span><span class="token operator">&amp;</span> v<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> u <span class="token operator">&lt;</span> v <span class="token operator">?</span> v <span class="token operator">:</span> u<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Есть встроенная возможность:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">decltype</span><span class="token punctuation">(</span><span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Но вот проблема, <code>args</code> используется до объявления<br>
В <code>c++11</code> появилась возможность указать тип возвращаемого значения после объявления функции (<strong>trailing return type</strong>)</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">auto</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">decltype</span><span class="token punctuation">(</span><span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>А что если мы не хотим использовать <code>args</code>?</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">auto</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">decltype</span><span class="token punctuation">(</span><span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token function">T</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Не работает из-за: 1) может отсутствовать конструктор по умолчанию, 2) <code>args - lvalue</code>, <code>T() - rvalue</code></p>
<p>Выход?</p>
<pre class=" language-cpp"><code class="prism  language-cpp">tepmlate<span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
T<span class="token operator">&amp;</span> <span class="token function">declval</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">auto</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">decltype</span><span class="token punctuation">(</span><span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token function">declval</span><span class="token punctuation">(</span>T<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Такая штука может использоваться только в <strong>non-evaluating context</strong></p>
<p>В <code>c++14</code> появилась возможность делать просто</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">auto</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Хоть и компактно, но перестает работать для <code>SFINAE</code></p>
<p><strong>Существует 2 формы decltype</strong></p>
<ol>
<li><strong>decltype(var)</strong> - возвращает просто тип переменной</li>
<li><strong>decltype(expr)</strong><br>
НЕОЖИДАННО НАМ ВРАЛИ<br>
Что возвращает этот <strong>decltype</strong>?<br>
<code>prvalue - T, decltype(a) = int</code><br>
<code>lvalue - T&amp;, decltype((a)) = int&amp;</code><br>
<code>xvalue - T&amp;&amp;</code> (rvalue находящийся на грани уничтожения)</li>
</ol>
<pre class=" language-cpp"><code class="prism  language-cpp">	T <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
	T a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//f() - prvalue, можем не вызывать конструктор</span>
		 
	T<span class="token operator">&amp;&amp;</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	T a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// f() - xvalue, не можем не вызывать конструктор</span>
</code></pre>
<h3 id="nullptr">nullptr</h3>
<p>Можно инициализировать нулевой указатель нулем<br>
Но тут возникают проблемы с перегрузками по <code>int</code> и <code>void*</code>, а также с <code>perfect forwarding</code> по тем же параметрам<br>
Появился <strong>nullptr</strong>, который имеет свой собственный тип <strong>nullptr_t</strong>, который может приводиться к указателю на любой тип</p>
<h3 id="указатель-на-member-функцию">Указатель на member функцию</h3>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> foo <span class="token punctuation">{</span>  
    <span class="token keyword">int</span> <span class="token function">bar</span><span class="token punctuation">(</span><span class="token keyword">int</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>  
        <span class="token keyword">return</span> a<span class="token punctuation">;</span>  
  <span class="token punctuation">}</span>  
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">int</span> <span class="token punctuation">(</span>foo<span class="token operator">::</span><span class="token operator">*</span> p<span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">)</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token operator">&amp;</span>foo<span class="token operator">::</span>bar<span class="token punctuation">)</span><span class="token punctuation">;</span>  
foo v<span class="token punctuation">;</span>  
<span class="token punctuation">(</span>v<span class="token punctuation">.</span><span class="token operator">*</span>p<span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

foo<span class="token operator">*</span> <span class="token function">p</span>
<span class="token punctuation">(</span>v<span class="token operator">-</span><span class="token operator">&gt;</span><span class="token operator">*</span>p<span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>


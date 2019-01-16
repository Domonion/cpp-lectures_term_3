---


---

<h2 id="rvalue-move">Rvalue, move</h2>
<h3 id="передаваемые-значения">Передаваемые значения</h3>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> big_struct <span class="token punctuation">{</span>
	std<span class="token operator">::</span>array<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token number">10</span><span class="token operator">&gt;</span> data<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span><span class="token keyword">int</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>int, int*, int&amp;</strong> - передается через регистры/стек<br>
А если передать структурку?</p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct a<span class="token punctuation">;</span>
<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> 
</code></pre>
<p>Надо скопировать, а в функцию передается ссылка</p>
<p>Как можно реализовать?</p>
<p><strong>I</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct a<span class="token punctuation">;</span>

<span class="token punctuation">{</span>
	big_struct copy <span class="token operator">=</span> a<span class="token punctuation">;</span>
	<span class="token function">f</span><span class="token punctuation">(</span>copy<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span>big_struct<span class="token operator">&amp;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>II</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct a<span class="token punctuation">;</span>
<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span>big_struct <span class="token keyword">const</span><span class="token operator">&amp;</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	big_struct copy <span class="token operator">=</span> a<span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>
</code></pre>
<p>В <strong>с, с++</strong> используется <strong>I</strong> вариант, так как в некоторых случаях можно не делать копию.</p>
<p>Структуры занимающие <strong>1, 2</strong> числа передаются обычно через регистры</p>
<h3 id="возвращаемые-значения">Возвращаемые значения</h3>
<p>По умолчанию результат записывается в специальный(ну не очень) регистр<br>
Маленькие структуры возвращаются аналогично передаваемым</p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	big_struct a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Транслируется в:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">void</span><span class="token operator">*</span>  result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token function">ctor_big_integer</span><span class="token punctuation">(</span>result<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Конструктор</span>
<span class="token punctuation">}</span>

<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">char</span> a_buf<span class="token punctuation">[</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span>big_struct<span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
	<span class="token function">f</span><span class="token punctuation">(</span>a_buf<span class="token punctuation">)</span><span class="token punctuation">;</span>
	big_struct<span class="token operator">&amp;</span> a <span class="token operator">=</span> <span class="token punctuation">(</span>big_struct<span class="token operator">&amp;</span><span class="token punctuation">)</span>a_buf<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Еще примерчик</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">f</span><span class="token punctuation">(</span><span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

________________

<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token keyword">void</span> <span class="token operator">*</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">void</span> <span class="token function">h</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">char</span> tmp_buf<span class="token punctuation">[</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span>big_struct<span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
	<span class="token function">g</span><span class="token punctuation">(</span>tmp_buf<span class="token punctuation">)</span><span class="token punctuation">;</span>
	big_struct<span class="token operator">&amp;</span> tmp <span class="token operator">=</span> <span class="token punctuation">(</span>big_struct<span class="token operator">&amp;</span><span class="token punctuation">)</span> tmp_buf<span class="token punctuation">;</span>
	<span class="token function">f</span><span class="token punctuation">(</span>tmp<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="return-value-optimization-rvo">Return value optimization (RVO)</h3>
<p>И еще</p>
<pre class=" language-cpp"><code class="prism  language-cpp">strict big_struct <span class="token punctuation">{</span>
	<span class="token function">big_struct</span><span class="token punctuation">(</span><span class="token keyword">int</span> <span class="token punctuation">,</span><span class="token keyword">int</span><span class="token punctuation">,</span> <span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

big_struct <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token keyword">return</span> <span class="token function">big_strust</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

big_struct <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p><strong>Наивный вариант</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token keyword">void</span><span class="token operator">*</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">char</span> tmp<span class="token punctuation">[</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span>big_struct<span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
	<span class="token function">big_struct_ctor</span><span class="token punctuation">(</span>tmp<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token function">big_struct_ctor</span><span class="token punctuation">(</span>result<span class="token punctuation">,</span> <span class="token punctuation">(</span>big_struct<span class="token operator">&amp;</span><span class="token punctuation">)</span>tmp<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Нормальный</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token keyword">void</span><span class="token operator">*</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">big_struct_ctor</span><span class="token punctuation">(</span>result<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>И ещее<br>
<strong>NamedRVO</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	big_struct <span class="token function">tmp</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	tmp<span class="token punctuation">.</span><span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token keyword">return</span> result
<span class="token punctuation">}</span>
</code></pre>
<p>Зачем делать лишнее копирование?</p>
<pre class=" language-cpp"><code class="prism  language-cpp">big_struct <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">big_struct_ctor</span><span class="token punctuation">(</span>result<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token keyword">try</span> <span class="token punctuation">{</span>
		<span class="token punctuation">(</span>big_struct<span class="token operator">*</span><span class="token punctuation">)</span>result <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token punctuation">(</span>big_struct<span class="token operator">*</span><span class="token punctuation">)</span>result<span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token operator">~</span><span class="token function">big_struct</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">throw</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Неприменимо, если на момент создания <code>tmp</code>, будет ли точно оно возвращено</p>
<h2 id="ну-погнали-rvalue">Ну погнали rvalue</h2>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>std<span class="token operator">::</span>string<span class="token operator">&gt;</span> v<span class="token punctuation">;</span>
std<span class="token operator">:</span>string s<span class="token punctuation">;</span>
v<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">;</span>

И
std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>file_descriptor<span class="token operator">&gt;</span> b<span class="token punctuation">;</span>
</code></pre>
<p>В <code>c++03</code> превращаем в</p>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>file_descriptor<span class="token operator">*</span><span class="token operator">&gt;</span>
std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>boost<span class="token operator">::</span>shared_ptr<span class="token operator">&lt;</span>file_descroptor<span class="token operator">&gt;&gt;</span> v<span class="token punctuation">;</span>
</code></pre>
<p>Если передать в функцию <code>rvalue</code>, то можно портить передаваемый элемент<br>
С <code>с++11</code> можно перегружать функции по <code>lvalue</code> И <code>rvalue</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> a <span class="token operator">=</span> <span class="token number">5</span><span class="token punctuation">;</span>

<span class="token keyword">int</span><span class="token operator">&amp;</span>  b <span class="token operator">=</span> a<span class="token punctuation">;</span> <span class="token comment">//ok</span>
<span class="token keyword">int</span><span class="token operator">&amp;&amp;</span> c <span class="token operator">=</span> a<span class="token punctuation">;</span> <span class="token comment">//error</span>
<span class="token keyword">int</span><span class="token operator">&amp;</span>  d <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//error</span>
<span class="token keyword">int</span><span class="token operator">&amp;&amp;</span> e <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//ok</span>


<span class="token keyword">void</span> <span class="token function">push_back</span><span class="token punctuation">(</span>T <span class="token keyword">const</span><span class="token operator">&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">void</span> <span class="token function">push_back</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Есть новый конструктор</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">string</span><span class="token punctuation">(</span>string<span class="token operator">&amp;&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Именованная переменная <strong>всегда</strong> <code>lvalue</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">push_back</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">foo</span><span class="token punctuation">(</span><span class="token keyword">static_cast</span><span class="token operator">&lt;</span>T<span class="token operator">&amp;&amp;</span><span class="token operator">&gt;</span> <span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Но получается слишком частая операция, поэтому есть встроенная функция</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
T<span class="token operator">&amp;&amp;</span> <span class="token function">move</span><span class="token punctuation">(</span>T<span class="token operator">&amp;</span> obj<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">static_cast</span><span class="token operator">&lt;</span>T<span class="token operator">&amp;&amp;</span><span class="token operator">&gt;</span><span class="token punctuation">(</span>obj<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Типичные ошибки</strong></p>
<ol>
<li>
<p>Написав так, можно подавить <strong>NRVO</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">string <span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	string res <span class="token operator">=</span> <span class="token string">"hello"</span><span class="token punctuation">;</span>
	res <span class="token operator">+</span><span class="token operator">=</span> <span class="token string">"world"</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token function">move</span><span class="token punctuation">(</span>res<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
<li>
<p><code>T</code> не поддерживает <code>rvalue</code>, тогда будет вызван просто конструктор копирования</p>
<pre class=" language-cpp"><code class="prism  language-cpp">	T a<span class="token punctuation">;</span>
	T b <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">move</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p><code>move</code> <strong>не указ, а подсказка</strong></p>
</li>
<li>
<pre class=" language-cpp"><code class="prism  language-cpp">T<span class="token operator">*</span> new_data <span class="token operator">=</span> <span class="token keyword">operator</span> <span class="token keyword">new</span><span class="token punctuation">(</span><span class="token keyword">sizeof</span><span class="token punctuation">(</span>T<span class="token punctuation">)</span> <span class="token operator">*</span> new_capacity<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">for</span> <span class="token punctuation">(</span>size_t i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">!=</span> size<span class="token punctuation">;</span> <span class="token operator">++</span>i<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">new</span><span class="token punctuation">(</span><span class="token operator">&amp;</span>new_data<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token function">T</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">move</span><span class="token punctuation">(</span>old_data<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Не <strong>exception-safety</strong> (н-р <code>T</code> не поддерживает <code>move</code>)<br>
Поэтому просто статически проверяем является ли <code>move</code> <strong>noexcept</strong>, иначе не используем.</p>
<p><code>enable_if&lt;noexcept(a + b)&gt;::type</code> - статическая проверка noexcept</p>
</li>
</ol>
<p>Можно писать в <code>noexcept</code> любое логическое выражение</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">bool</span> <span class="token function">foo</span><span class="token punctuation">(</span>T<span class="token punctuation">)</span> <span class="token keyword">noexcept</span><span class="token punctuation">(</span>enable_if<span class="token operator">&lt;</span><span class="token keyword">noexcept</span><span class="token punctuation">(</span>T<span class="token punctuation">)</span><span class="token operator">&gt;</span><span class="token operator">::</span>type<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Для работы <code>move</code> при создании объекта (н-р добавление в вектор) требуется <code>noexcept</code> конструктора. Еще это важно для <code>hashCode()</code>(иначе проблемы с рехэшэм и надо хранить хэш рядом с объектом, а спрашивать его только при вставке)</p>
<h3 id="еще-применение-rvalue">Еще применение rvalue</h3>
<p><strong>Forwarding</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">char</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span>T a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Плохо, копирования, возможно снятие <code>const</code> и т.д.<br>
Можно сделать 2 перегрузки - с <code>const</code> и без <code>const</code>. Но для 2 аргументов это уже 4 перегрузки. O(2^n) перегрузок - не оч.</p>
<p><strong>Perfect forwarding</strong><br>
Когда сохраняем тип, константность и lvalue/rvalue.</p>
<p>Доопределили вывод:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">int</span> a<span class="token punctuation">;</span>
<span class="token keyword">int</span> <span class="token keyword">const</span> b <span class="token operator">=</span> <span class="token number">5</span><span class="token punctuation">;</span>
<span class="token function">f</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//T -&gt; int&amp;</span>
<span class="token function">f</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//T -&gt; int const&amp;</span>

<span class="token function">f</span><span class="token punctuation">(</span><span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//T -&gt; int</span>
</code></pre>
<p>Еще работают следующие правила:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token operator">&amp;</span> <span class="token operator">&amp;</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token operator">&amp;</span>
<span class="token operator">&amp;</span> <span class="token operator">&amp;&amp;</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token operator">&amp;</span>
<span class="token operator">&amp;&amp;</span> <span class="token operator">&amp;</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token operator">&amp;</span>
<span class="token operator">&amp;&amp;</span> <span class="token operator">&amp;&amp;</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token operator">&amp;&amp;</span>
</code></pre>
<p><strong>perfect</strong>:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">static_cast</span><span class="token operator">&lt;</span>T<span class="token operator">&amp;&amp;</span><span class="token operator">&gt;</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>

<span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token function">f</span><span class="token punctuation">(</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Наши <code>move</code> и <code>forward</code> полностью совпадают. В реале же они написаны немного по разному, так как у <code>forward</code> есть шаблонный параметр, чтобы всегда происходил <code>move</code> (вроде??)</p>
<p>Для многих аргументов</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> T<span class="token operator">&gt;</span>
<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span>T<span class="token operator">&amp;&amp;</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">f</span><span class="token punctuation">(</span>forward<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>


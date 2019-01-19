---


---

<h3 id="динамические-библиотеки">Динамические библиотеки</h3>
<p><strong>Проблемы:</strong><br>
Команда чтения из памяти по определенному адресу</p>
<pre><code>mov rax, [0x12345678]
</code></pre>
<p>подразумевает знание этого адреса на этапе компиляции и это не подходит для динамических библиотек</p>
<p><strong>Способы решить:</strong></p>
<ol>
<li>Специальный регистр, чтобы знать где мы находимся (не очень применяется)</li>
<li>Переправить все адреса, если она загружена в другом месте (Винда)</li>
<li>Сгенерировать такой код, который куда бы не загрузился, работал бы (линукс)</li>
</ol>
<p><strong>Новая проблема:</strong> как у одной DLL вызвать другую DLL?</p>
<p><strong>Как создается DLL?</strong></p>
<p><strong>Простая схема (винда)</strong><br>
Берем набор объектников, они линкуются в <code>.dll</code>, одновременно с этим создается <code>.lib</code> (import library). Когда хотим в своей программе использовать DLL, то передаем ей объектники и <code>.lib</code>.</p>
<p>Помимо этого надо передать линковщику DLL’ки <code>.def</code>, в котором написаны названия функций, который мы хотим экспортировать из библиотеки. Проблема — как передавать перегруженные функции? Писать их полное ассемблерное имя?? Микромягкие попытались это смягчить и есть модификатор</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">__declspec</span><span class="token punctuation">(</span>dllexport<span class="token punctuation">)</span> <span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>Теперь как ссылаются DLL’ки друг на друга. Создается таблица <strong>Import Address Table</strong> (GOT в линукс)</p>
<p>Теперь пусть в DLL объявлена функция <code>void foo()</code>, а в другой программе мы ее вызываем, компилятор понятия не имеет что это за функция, из DLL она, не из DLL, так как же компилятор ее вызывает? Какой тип <code>call</code> использует?</p>
<pre class=" language-cpp"><code class="prism  language-cpp">call foo        E8xxxxxxxx 
</code></pre>
<p>Можно сказать что происходит</p>
<pre class=" language-cpp"><code class="prism  language-cpp">push <span class="token operator">&amp;</span>next_instr
rip <span class="token operator">=</span> rip <span class="token operator">+</span> xxxxxxxx
</code></pre>
<p>То есть для DLL не подходит, так как DLL для каждой программы имеет свой адрес в виртуальной памяти (хотя чаще всего имеет один в физической)</p>
<p>Есть другая форма</p>
<pre class=" language-cpp"><code class="prism  language-cpp">call <span class="token punctuation">[</span>rip <span class="token operator">+</span> xxxxxxxx<span class="token punctuation">]</span>

Что равносильно

push <span class="token operator">&amp;</span>next_instr
rip <span class="token operator">=</span> <span class="token punctuation">[</span>rip <span class="token operator">+</span> xxxxxxxx<span class="token punctuation">]</span>
</code></pre>
<p>Но компилятор генерирует не это, это подменяет линковщик следующим образом:</p>
<pre class=" language-cpp"><code class="prism  language-cpp">foo<span class="token operator">:</span>
	jmp <span class="token punctuation">[</span>__jmp__foo<span class="token punctuation">]</span>
</code></pre>
<p>Но получается многовато вызовов, поэтому микромягкие опять решили сделать оптимизацию — функцию можно пометить, что она из DLL’ки</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">__declspec</span><span class="token punctuation">(</span>dllimport<span class="token punctuation">)</span> <span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>В каком случае DLL копируется? Если мы хотим изменять память, то чтобы это не влияло на другие программы, происходит <strong>copy on write</strong></p>
<p><strong>Сложная схема (линукс)</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token number">1</span><span class="token punctuation">.</span>cpp
<span class="token macro property">#<span class="token directive keyword">include</span> <span class="token string">&lt;iostream&gt;</span></span>
<span class="token keyword">void</span> foo <span class="token punctuation">{</span>
	std<span class="token operator">::</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Hello, world!"</span>
<span class="token punctuation">}</span>

<span class="token number">2</span><span class="token punctuation">.</span>cpp
<span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Компиляция библиотеки</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">g<span class="token operator">++</span> <span class="token operator">-</span>fpic <span class="token operator">-</span>shared <span class="token number">1</span><span class="token punctuation">.</span>cpp <span class="token operator">-</span>o <span class="token number">1</span><span class="token punctuation">.</span>so
</code></pre>
<p><code>-fpic</code> — разрешить шарить (position independent code) (команда компилятору)<br>
<code>-shared</code> — выдать <code>.so</code> (команда линковщику)</p>
<p><strong>Компиляция программы</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">g<span class="token operator">++</span> <span class="token number">2</span><span class="token punctuation">.</span>cpp <span class="token number">1</span><span class="token punctuation">.</span>so <span class="token operator">-</span>o <span class="token number">2</span>
</code></pre>
<p><strong>Запуск</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">LD_LIBRARY_PATH<span class="token operator">=</span><span class="token punctuation">.</span> <span class="token punctuation">.</span><span class="token operator">/</span><span class="token number">2</span>
</code></pre>
<p><code>LD_LIBRARY_PATH</code> — по умолчанию <code>.so</code> не ищется в текущем каталоге. Поэтому мы и говорим: “Бери <code>.so</code> и из этого каталоге тоже”</p>
<p><strong>Заметим</strong></p>
<p>Тут мы не писали, какие функции экспортировать. Это имеет негативный эффект: таблицы становятся просто огромными, хоть и удобно не писать всякие <code>__declspec(...)</code></p>
<p>Также мы можем перекрывать функции из DLL, при этом это будет не ошибка, а просто перекрытие</p>
<p>Если хотим чтобы функция не торчала наружу dll’ки надо указать у функции <code>__attribute__((visibility("hidden")))</code></p>
<p><strong>Ленивая линковка (PLT (Procedure Linkage Table))</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp">foo<span class="token operator">:</span>
	jmp <span class="token punctuation">[</span>__imp__foo<span class="token punctuation">]</span>
	push yyyyyyyy
	jmp <span class="token punctuation">[</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">]</span> inter position
</code></pre>
<p>При первом запуске функции в [__imp__foo] лежит адрес push, а в 2-3 строке записывается настоящая позиция функции<br>
При следующем запуске на первом jmp уже сразу будет переходить в правильную функцию</p>
<p>Ускоряет время запуска программы</p>
<p><strong>Best practice</strong><br>
Если заюзать статическую либу в нескольких динамических — будет много копий статической. И между собой динамические не могут нормально пользоваться функциями статической.<br>
Можно либо запрещать взаимодействие вроде “выделить память в одной dll’ке, а освободить в другой”, либо просто сделать статическую динамической</p>


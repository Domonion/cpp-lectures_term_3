---


---

<h2 id="еще-о-фичах">Еще о фичах</h2>
<p><strong>foreach</strong><br>
Как раскрыть? <code>v.begin()</code>, <code>v.end()</code> не применим для массивов например. Выход простой — запилить внешнюю перегрузку <code>std::begin(v)</code>, <code>std::end(v)</code>, <code>std::size(v)</code></p>
<p><strong>Когда расширяешь язык, можно многое забыть</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token operator">::</span>const_iterator <span class="token function">f</span><span class="token punctuation">(</span>std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token keyword">const</span> <span class="token operator">&amp;</span>v<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

<span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span>std<span class="token operator">::</span>vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token operator">&amp;</span>v<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	std<span class="token operator">::</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token function">f</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> v<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Не работает, ибо разные типы итераторов, поэтому потом добавили <code>.cend()</code>, <code>.cbegin()</code> и т.д.</p>
<p>Но забыли добавить <code>std::cbegin(v)</code> И т.д, хех (вроде в с++11)</p>
<p><strong>Еще</strong> забыли в лямбдах разрешать <strong>move</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token punctuation">[</span>a<span class="token punctuation">]</span>  <span class="token punctuation">{</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">}</span>
<span class="token punctuation">[</span><span class="token operator">&amp;</span>a<span class="token punctuation">]</span> <span class="token punctuation">{</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">}</span>
</code></pre>
<p>Потом добавили <code>[a = std::move(a)] {}</code>, где инициализатором может быть все что угодно</p>
<p><strong>Еще</strong> в <code>initializer_list</code> не предусмотрен <strong>move</strong>, если написать</p>
<pre class=" language-cpp"><code class="prism  language-cpp">string s<span class="token punctuation">;</span>
std<span class="token operator">::</span>vector<span class="token operator">&lt;</span>string<span class="token operator">&gt;</span> a <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token string">"foo"</span><span class="token punctuation">,</span> <span class="token string">"bar"</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">move</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">}</span>
</code></pre>
<p>то в итоге <strong>s</strong> скопируется сначала в лист, а потом в вектор — два копирования вместо мува, хыхы</p>
<h2 id="как-работают-динамические-либы">Как работают динамические либы</h2>
<p><strong>.so</strong> загружается не всегда по одному адресу. Проблема — мы не можем в машинном коде знать адрес переменных. Для статики легко — просто смотрим на разницу адресов.</p>
<p>Можно попробовать хранить в регистре в какой адрес мы загрузились, но тогда надо аккуратно все перетирать регистры при вызове длл из длл (вроде), плюс может использоваться прослойка для вызова функции и иногда вместо адреса функции мы получим адрес прослойки</p>
<p>Можно хранить таблицу релокаций — просто загрузить программу, пройтись по всем внешним функциям, переправить адреса, но это замедляет старт программы (в винде вроде что-то такое, но они просто инкрементят на <code>address - base_address</code>). Еще проблема — если загрузилось в <code>base_address</code>, то шарится между всеми процессами, иначе у каждого процесса своя копия дллки.</p>
<p>В линухе есть <strong>GOT (global offset table)</strong> — чтобы не править всю программу собирается то, что зависит от адресов программы, и правим мы уже GOT. Но вот проблема — как обращаться к GOT’у? (используется для чего-то другого, хз для чего)</p>
<p>Еще один выход. У нас есть <code>jmp</code>, казалось бы для него такая же проблема с адресами? Нет, он использует относительный адрес, поэтому ему пофиг (и <code>call</code> тоже). Так а почему бы и память не читать относительно? На 64-битных системах появились такие команды, поэтому мы можем сгенерить код на относительных адресах (<strong>PIC (position independent code)</strong>).<br>
А что делать с 32-битными системами?( В линуксе такой код генерится и для 32-бит, но обращение делается неэффективно. Делают <code>call</code> на некоторую функцию, которая возвращает адрес этой функции и зная относительные адреса херачим.</p>
<p>Когда запускаете программу — запускается не ваша программа, а динамический загрузчик (в линухе что-то вроде <code>ld-linux.so</code>) и ваша программа передается ему как аргумент. И в линухе просят “дайте мне кто-нибудь эту функцию”, а динамический загрузчик как-то выбирает из <code>.so</code> приоритеты.</p>


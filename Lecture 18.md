---


---

<h2 id="offtop-text-encoding">Offtop, Text encoding</h2>
<p><strong>Unicode</strong><br>
Таблица 0…0x10FFFF - <strong>code point</strong><br>
Их преобразуют в различные последовательности символов (<strong>code unit</strong>)</p>
<p>В байты - <strong>UTF-8</strong><br>
В 2-байтовые числа - <strong>UTF-16</strong><br>
В 32-битные числа - <strong>UTF-32</strong></p>
<p><strong>code units</strong> кодируются в байты, записанные либо в <strong>big endian</strong>,  либо в <strong>little endian</strong></p>
<p><strong>UTF-16</strong></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">if</span> <span class="token punctuation">(</span>cp <span class="token operator">&lt;</span> <span class="token number">0x10000</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token punctuation">{</span>cp<span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
	cp <span class="token operator">-</span><span class="token operator">=</span> <span class="token number">0x10000</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token punctuation">{</span><span class="token number">0xD800</span> <span class="token operator">|</span> <span class="token punctuation">(</span>cp <span class="token operator">&gt;&gt;</span> <span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
			<span class="token number">0xDC00</span> <span class="token operator">|</span> <span class="token punctuation">(</span>cp <span class="token operator">&amp;</span> <span class="token number">0x3FF</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>UFT-8</strong></p>

<table>
<thead>
<tr>
<th>cp</th>
<th>cu/byte</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>cp&lt;0x80:</strong></td>
<td>0xxxxxxx</td>
</tr>
<tr>
<td><strong>cp&lt;0x2000:</strong></td>
<td>110xxxxx 10xxxxxx</td>
</tr>
<tr>
<td><strong>cp&lt;0x10000:</strong></td>
<td>1110xxxx 10xxxxxx 10xxxxxx</td>
</tr>
<tr>
<td><strong>cp&lt;0x200000:</strong></td>
<td>11110xxx 10xxxxxx 10xxxxxx 10xxxxxx</td>
</tr>
</tbody>
</table>

<div class="inner">
<h2>一道 Java 面试题</h2>
<p>关于程序员对 Java 类型系统的理解，比较高级的一个面试问题是这样：</p>
<div class="language-java highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">public</span> <span class="kd">static</span> <span class="kt">void</span> <span class="nf">f</span><span class="o">()</span> <span class="o">{</span>
<span class="nc">String</span><span class="o">[]</span> <span class="n">a</span> <span class="o">=</span> <span class="k">new</span> <span class="nc">String</span><span class="o">[</span><span class="mi">2</span><span class="o">];</span>
<span class="nc">Object</span><span class="o">[]</span> <span class="n">b</span> <span class="o">=</span> <span class="n">a</span><span class="o">;</span>
<span class="n">a</span><span class="o">[</span><span class="mi">0</span><span class="o">]</span> <span class="o">=</span> <span class="s">"hi"</span><span class="o">;</span>
<span class="n">b</span><span class="o">[</span><span class="mi">1</span><span class="o">]</span> <span class="o">=</span> <span class="nc">Integer</span><span class="o">.</span><span class="na">valueOf</span><span class="o">(</span><span class="mi">42</span><span class="o">);</span>
<span class="o">}</span>
</code></pre></div></div>
<p>这段代码里面到底哪一行错了？为什么？如果某个 Java 版本能顺利运行这段代码，那么如何让这个错误暴露得更致命一些？</p>
<p>注意这里所谓的「错了」是本质上，原理上的，而不一定是 Java 编译器，IDE 或者运行时报给你的。也就是说，你用的 Java 实现，IDE 都可能是错的，没找对真正错误的地方，或者没告诉你真正的原因。</p>
<p>如果你知道哪里错了，并且知道「为什么」错了，可以联系我。</p>
</div>
<!--
<div class="ad-banner" style="margin-top: 5px">
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
                    style="display:inline-block;width:100%;height:90px"
                    data-ad-client="ca-pub-1331524016319584"
                    data-ad-slot="6657867155"></ins>
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
</div>
        -->
<script data-ad-client="ca-pub-1331524016319584" async
            src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js">
</script>
    
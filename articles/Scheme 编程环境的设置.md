<div class="inner">
<h2>Scheme 编程环境的设置</h2>
<p><img src="http://www.yinwang.org/images/paredit1.gif" width="80%" /></p>
<p>介绍了这么久的 Scheme，却没有讲过如何配置一个高效的 Scheme 的编程环境。有些人开始学习 Scheme 的时候感觉无从下手，所以今天讲一下它的配置。</p>
<p>Scheme 的配置有很多种方式，我不想介绍太多东西，免得有人看花了眼，所以这里只介绍一下我自己的配置。我不大喜欢像 <a href="http://www.neilvandyke.org/quack">Quack</a> 一类的复杂的环境，因为它们经常有很多多余的功能，却缺少我想要的功能。一旦我想修改它们，又到处出问题。我的配置很简约，我用它写了几千行的超高难度的代码，翻来覆去的改，感觉效率非常高，也没有觉得缺少什么特别重要的东西。</p>
<p>现在我就一步一步的介绍我的配置。</p>
<h3 id="安装-scheme">安装 Scheme</h3>
<h4 id="chez-scheme">Chez Scheme</h4>
<p>世界上最快，最成熟可靠的 Scheme 实现是 R. Kent Dybvig 所作的 Chez Scheme。它可以把 Scheme 编译成机器代码，运行速度非常高。Chez Scheme 曾经是商业软件，价格昂贵，然而现在却开源了，并且可以免费使用。你可以在这里下载 Chez Scheme 的源代码：</p>
<p><a href="https://github.com/cisco/ChezScheme">https://github.com/cisco/ChezScheme</a></p>
<p>编译安装很快很方便，在 Linux 和 Mac 系统基本就是这样：</p>
<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>./configure
make
sudo make install
</code></pre></div></div>
<p>整个编译安装过程只需要30秒。这是世界上最快编译自己全套系统的编译器。</p>
<h4 id="racket">Racket</h4>
<p>如果你对性能没有特别高的需求，主要用于学习，也可以用 Racket。它可以在这里下载：</p>
<p><a href="http://racket-lang.org">http://racket-lang.org</a></p>
<p>安装应该很容易。Ubuntu 也自带了 Racket，所以可以直接让系统安装它。</p>
<h3 id="设置-paredit-mode">设置 ParEdit mode</h3>
<p>我编辑 Scheme 的时候都用 Emacs。我使用一个叫做 ParEdit mode 的插件。它可以让你“半结构化”式的编辑 Scheme 和其它的 Lisp 文件。开头你可能会有点不习惯，可是一旦习惯了，你就再也离不开它。</p>
<p>ParEdit mode 可以在这里下载：</p>
<p><a href="http://mumble.net/~campbell/emacs/paredit.el">http://mumble.net/~campbell/emacs/paredit.el</a></p>
<p>下载之后，把它放到一个目录里，比如 ~/.emacs.d，然后打开 ~/.emacs 配置文件，加入如下设置：</p>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nv">add-to-list</span> <span class="ss">'load-path</span> <span class="s">"~/.emacs.d"</span><span class="p">)</span>
<span class="p">(</span><span class="nv">autoload</span> <span class="ss">'paredit-mode</span> <span class="s">"paredit"</span>
<span class="s">"Minor mode for pseudo-structurally editing Lisp code."</span>
<span class="no">t</span><span class="p">)</span>
</code></pre></div></div>
<p>这样，只要你使用 M-x paredit-mode 就可以自动载入这个模式。具体的操作方式可以看它的说明（按 C-h m 查看“模式帮助”），我下面也会简单说一下。</p>
<h3 id="设置-scheme-mode">设置 scheme mode</h3>
<p>我一般就用系统自带的 Scheme 模式，叫 cmuscheme。但是为了方便，我自己写了几个函数，用于在执行 Scheme 代码的时候自动启动解释器，并且打开解释器窗口。你基本只需要把下面的代码拷贝到你的 .emacs 文件里就行：</p>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">;;;;;;;;;;;;</span>
<span class="c1">;; Scheme </span>
<span class="c1">;;;;;;;;;;;;</span>
<span class="p">(</span><span class="nb">require</span> <span class="ss">'cmuscheme</span><span class="p">)</span>
<span class="p">(</span><span class="k">setq</span> <span class="nv">scheme-program-name</span> <span class="s">"racket"</span><span class="p">)</span>         <span class="c1">;; 如果用 Petite 就改成 "petite"</span>
</code></pre></div></div>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="c1">;; bypass the interactive question and start the default interpreter</span>
<span class="p">(</span><span class="nb">defun</span> <span class="nv">scheme-proc</span> <span class="p">()</span>
<span class="s">"Return the current Scheme process, starting one if necessary."</span>
<span class="p">(</span><span class="nb">unless</span> <span class="p">(</span><span class="nb">and</span> <span class="nv">scheme-buffer</span>
<span class="p">(</span><span class="nv">get-buffer</span> <span class="nv">scheme-buffer</span><span class="p">)</span>
<span class="p">(</span><span class="nv">comint-check-proc</span> <span class="nv">scheme-buffer</span><span class="p">))</span>
<span class="p">(</span><span class="nv">save-window-excursion</span>
<span class="p">(</span><span class="nv">run-scheme</span> <span class="nv">scheme-program-name</span><span class="p">)))</span>
<span class="p">(</span><span class="nb">or</span> <span class="p">(</span><span class="nv">scheme-get-process</span><span class="p">)</span>
<span class="p">(</span><span class="nb">error</span> <span class="s">"No current process. See variable `scheme-buffer'"</span><span class="p">)))</span>
</code></pre></div></div>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nb">defun</span> <span class="nv">scheme-split-window</span> <span class="p">()</span>
<span class="p">(</span><span class="nb">cond</span>
<span class="p">((</span><span class="nb">=</span> <span class="mi">1</span> <span class="p">(</span><span class="nv">count-windows</span><span class="p">))</span>
<span class="p">(</span><span class="nv">delete-other-windows</span><span class="p">)</span>
<span class="p">(</span><span class="nv">split-window-vertically</span> <span class="p">(</span><span class="nb">floor</span> <span class="p">(</span><span class="nb">*</span> <span class="mf">0.68</span> <span class="p">(</span><span class="nv">window-height</span><span class="p">))))</span>
<span class="p">(</span><span class="nv">other-window</span> <span class="mi">1</span><span class="p">)</span>
<span class="p">(</span><span class="nv">switch-to-buffer</span> <span class="s">"*scheme*"</span><span class="p">)</span>
<span class="p">(</span><span class="nv">other-window</span> <span class="mi">1</span><span class="p">))</span>
<span class="p">((</span><span class="nb">not</span> <span class="p">(</span><span class="nb">find</span> <span class="s">"*scheme*"</span>
<span class="p">(</span><span class="nb">mapcar</span> <span class="p">(</span><span class="k">lambda</span> <span class="p">(</span><span class="nv">w</span><span class="p">)</span> <span class="p">(</span><span class="nv">buffer-name</span> <span class="p">(</span><span class="nv">window-buffer</span> <span class="nv">w</span><span class="p">)))</span>
<span class="p">(</span><span class="nv">window-list</span><span class="p">))</span>
<span class="ss">:test</span> <span class="ss">'equal</span><span class="p">))</span>
<span class="p">(</span><span class="nv">other-window</span> <span class="mi">1</span><span class="p">)</span>
<span class="p">(</span><span class="nv">switch-to-buffer</span> <span class="s">"*scheme*"</span><span class="p">)</span>
<span class="p">(</span><span class="nv">other-window</span> <span class="mi">-1</span><span class="p">))))</span>
</code></pre></div></div>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nb">defun</span> <span class="nv">scheme-send-last-sexp-split-window</span> <span class="p">()</span>
<span class="p">(</span><span class="nv">interactive</span><span class="p">)</span>
<span class="p">(</span><span class="nv">scheme-split-window</span><span class="p">)</span>
<span class="p">(</span><span class="nv">scheme-send-last-sexp</span><span class="p">))</span>
</code></pre></div></div>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nb">defun</span> <span class="nv">scheme-send-definition-split-window</span> <span class="p">()</span>
<span class="p">(</span><span class="nv">interactive</span><span class="p">)</span>
<span class="p">(</span><span class="nv">scheme-split-window</span><span class="p">)</span>
<span class="p">(</span><span class="nv">scheme-send-definition</span><span class="p">))</span>
<span class="p">(</span><span class="nv">add-hook</span> <span class="ss">'scheme-mode-hook</span>
<span class="p">(</span><span class="k">lambda</span> <span class="p">()</span>
<span class="p">(</span><span class="nv">paredit-mode</span> <span class="mi">1</span><span class="p">)</span>
<span class="p">(</span><span class="nv">define-key</span> <span class="nv">scheme-mode-map</span> <span class="p">(</span><span class="nv">kbd</span> <span class="s">"&lt;f5&gt;"</span><span class="p">)</span> <span class="ss">'scheme-send-last-sexp-split-window</span><span class="p">)</span>
<span class="p">(</span><span class="nv">define-key</span> <span class="nv">scheme-mode-map</span> <span class="p">(</span><span class="nv">kbd</span> <span class="s">"&lt;f6&gt;"</span><span class="p">)</span> <span class="ss">'scheme-send-definition-split-window</span><span class="p">)))</span>
</code></pre></div></div>
<p>我的配置会在加载 Scheme 文件的时候自动载入 ParEdit mode，并且把 F5 键绑定到“执行前面的S表达式”。这样设置的目的是，我只要把光标移动到一个S表达式之后，然后用一根手指头按 F5，就可以执行程序。够懒吧。</p>
<h3 id="paredit-mode-的简单使用方法">ParEdit mode 的简单使用方法</h3>
<p>ParEdit mode 是一个很特殊的模式。它起作用的时候，你不能直接修改括号。这样所有的括号都保持完整的匹配，不可能出现语法错误。但是这样有一个问题，如果你要把一块代码放进另一块代码，或者从里面拿出来，就不是很方便了。</p>
<p>为此，ParEdit mode 提供了几个非常高效的编辑方式。我平时只使用两个：</p>
<ol>
<li>
<p><code class="language-plaintext highlighter-rouge">C-right</code>: 也就是按住 Ctrl 再按右箭头。它的作用是让光标右边的括号，“吞掉”下一个S表达式。</p>
<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>比如，`(a b c) (d e)`。你把光标放在 `(a b c)` 里面，然后按 `C-right`。结果就是 `(a b c (d e))`。也就是把 `(d e)` 被整个“吞进”了 `(a b c)` 里面。 
</code></pre></div>    </div>
</li>
<li>
<p><code class="language-plaintext highlighter-rouge">M-r</code>: 去掉外层代码。</p>
<p>这在你需要去掉外层的 let 等结构的时候非常有用。比如，如果你的代码看起来是这样：</p>
<div class="language-scheme highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="k">let</span> <span class="p">([</span><span class="nf">x</span> <span class="mi">10</span><span class="p">])</span>
<span class="p">(</span><span class="nb">*</span> <span class="nv">x</span> <span class="mi">2</span><span class="p">))</span>
</code></pre></div>    </div>
<p>当你把光标放在 <code class="language-plaintext highlighter-rouge">(* x 2)</code> 的最左边，然后按  <code class="language-plaintext highlighter-rouge">M-r</code>，结果就变成了</p>
<div class="language-scheme highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nb">*</span> <span class="nv">x</span> <span class="mi">2</span><span class="p">)</span>
</code></pre></div>    </div>
<p>也就是把外面的 <code class="language-plaintext highlighter-rouge">(let ([x 10]) ...)</code> 给“掀掉”了。</p>
<p>其它的一些按键虽然也有用，不过我觉得这两个是最有用的，甚至不可缺少的。有些其他的自动匹配括号的模式，没有提供这种按键，所以用起来很别扭。</p>
</li>
</ol>
<h3 id="设置括号颜色">设置括号颜色</h3>
<p>很多人看见 Lisp 就怕了，就是因为它看起来括号太多。可是这样的语法，却是有很大的好处的（参考这篇博文《谈语法》）。如果你真的觉得括号碍眼，你可以稍微调整一下括号的颜色，比如淡灰色。这样括号看起来就没有那么显眼了。</p>
<p>你只需要下载这个 el，放到你的 .emacs.d:</p>
<p><a href="https://www.dropbox.com/s/v0ejctd1agrt95x/parenface.el">https://www.dropbox.com/s/v0ejctd1agrt95x/parenface.el</a></p>
<p>然后在 .emacs 里面加入两行：</p>
<div class="language-lisp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="p">(</span><span class="nb">require</span> <span class="ss">'parenface</span><span class="p">)</span>
<span class="p">(</span><span class="nv">set-face-foreground</span> <span class="ss">'paren-face</span> <span class="s">"DimGray"</span><span class="p">)</span>
</code></pre></div></div>
<p>然后再打开 Scheme 代码的时候，你就会看到是这个样子：</p>
<p><img src="http://www.yinwang.org/images/scheme-paren.jpeg" width="95%" /></p>
<p>好了，这就是我写 Scheme 的所有配置了。希望这些有所帮助。</p>
</div>
<div class="ad-banner" style="margin-top: 5px">
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
                    style="display:inline-block;width:100%;height:90px"
                    data-ad-client="ca-pub-1331524016319584"
                    data-ad-slot="6657867155"></ins>
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
</div>
<script data-ad-client="ca-pub-1331524016319584" async
            src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js">
</script>
    
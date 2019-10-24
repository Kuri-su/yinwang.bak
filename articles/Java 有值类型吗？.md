<div class="inner">
<h2>Java 有值类型吗？</h2>
<p>有人看了我之前的文章『<a href="http://www.yinwang.org/blog-cn/2016/06/06/swift">Swift 语言的设计错误</a>』，问我：“你说 Java 只有引用类型（reference type），但是根据 Java 的<a href="http://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html">官方文档</a>，Java 也有值类型（value type）和引用类型的区别的。比如 int，boolean 等原始类型就是值类型。” 现在我来解释一下这个问题。</p>
<p>Java 有值类型，原始类型 int，boolean 等是值类型，其实是长久以来的一种误解，它混淆了实现和语义的区别。不要以为 Java 的官方文档那样写就是权威定论，就可以说“王垠不懂” :) 当你认为王垠不懂一个初级问题的时候，都需要三思，因为他可能是大智若愚…… 看了我下面的论述，也许你会发现自己应该怀疑的是，Java 的设计者到底有没有搞明白这个问题 :P</p>
<p>胡扯结束，现在来说正事。Java，Scheme 等语言的原始类型，比如 char，int，boolean，double 等，在“实现”上确实是通过值（而不是引用，或者叫指针）直接传递的，然而这完全是一种为了效率的优化（叫做 inlining）。这种优化对于程序员应该是不可见的。Java 继承了 Scheme/Lisp 的衣钵，它们在“语义”上其实是没有值类型的。</p>
<p>这不是天方夜谭，为了理解这一点，你可以做一个很有意思的思维实验。现在你把 Java 里面所有的原始类型都“想象”成引用类型，也就是说，所有的 int, boolean 等原始类型的变量都不包含实际的数据，而是引用（或者叫指针），指向堆上分配的数据。然后你会发现这样“改造后”的 Java，仍然符合现有 Java 代码里能看到的一切现象。也就是说，原始类型被作为值类型还是引用类型，对于程序员完全没有区别。</p>
<p>举个简单的例子，如果我们把 int 的实现变成完全的引用，然后来看这段代码：</p>
<div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>int x = 1;    // x指向内存地址A，内容是整数1
int y = x;    // y指向同样的内存地址A，内容是整数1
x = 2;        // x指向另一个内存地址B，内容是整数2。y仍然指向地址A，内容是1。
</code></pre></div></div>
<p>由于我们改造后的 Java 里面 int 全部是引用，所以第一行定义的 <code class="language-plaintext highlighter-rouge">x</code> 并不包含一个整数，而是一个引用，它指向堆里分配的一块内存，这个空间的内容是整数 1。在第二行，我们定 int 变量 <code class="language-plaintext highlighter-rouge">y</code>，当然它也是一个引用，它的值跟 <code class="language-plaintext highlighter-rouge">x</code> 一样，所以 <code class="language-plaintext highlighter-rouge">y</code> 也指向同一个地址，里面的内容是同一个整数：1。在第三行，我们对 <code class="language-plaintext highlighter-rouge">x</code> 这个引用赋值。你会发现一个很有意思的现象，虽然 <code class="language-plaintext highlighter-rouge">x</code> 指向了 2，<code class="language-plaintext highlighter-rouge">y</code> 却仍然指向 1。对 <code class="language-plaintext highlighter-rouge">x</code> 赋值并没能改变 <code class="language-plaintext highlighter-rouge">y</code> 指向的内容，这种情况就跟 int 是值类型的时候一模一样！所以现在虽然 int 变量全部是引用，你却不能实现共享地址的引用能做的事情：对 <code class="language-plaintext highlighter-rouge">x</code> 进行某种操作，导致 <code class="language-plaintext highlighter-rouge">y</code> 指向的内容也发生改变。</p>
<p>出现这个现象的原因是，虽然现在 int 成了引用类型，你却并不能对它进行引用类型所特有（而值类型没有）的操作。这样的操作包括：</p>
<ol>
<li>deref。就像 C 语言里的 <code class="language-plaintext highlighter-rouge">*</code> 操作符。</li>
<li>成员赋值。就像对 C struct 成员的 <code class="language-plaintext highlighter-rouge">x.foo = 2</code> 。</li>
</ol>
<p>在 Java 里，你没法写像 C 语言的 <code class="language-plaintext highlighter-rouge">*x = 2</code> 这样的代码，因为 Java 没有提供 deref 操作符 <code class="language-plaintext highlighter-rouge">*</code>。你也没法通过 <code class="language-plaintext highlighter-rouge">x.foo = 2</code> 这样的语句改变 <code class="language-plaintext highlighter-rouge">x</code> 所指向的内存数据（内容是1）的一部分，因为 int 是一个原始类型。最后你发现，你只能写 <code class="language-plaintext highlighter-rouge">x = 2</code>，也就是改变 <code class="language-plaintext highlighter-rouge">x</code> 这个引用本身的指向。<code class="language-plaintext highlighter-rouge">x = 2</code> 执行之后，原来数字 1 所在的内存空间并没有变成 2，只不过 x 指向了新的地址，那里装着数字 2 而已。指向 1 的其它引用变量比如 <code class="language-plaintext highlighter-rouge">y</code>，不会因为你进行了 <code class="language-plaintext highlighter-rouge">x = 2</code> 这个操作而看到 2，它们仍然看到原来那个1……</p>
<p>在这种 int 是引用的 Java 里，你对 int 变量 <code class="language-plaintext highlighter-rouge">x</code> 能做的事情只有两种：</p>
<ol>
<li>读出它的值。</li>
<li>对它进行赋值，使它指向另一个地方。</li>
</ol>
<p>这两种事情，就跟你能对值类型能做的两件事情没有区别。这就是为什么你没法通过对 <code class="language-plaintext highlighter-rouge">x</code> 的操作而改变 <code class="language-plaintext highlighter-rouge">y</code> 表示的值。所以不管 int 在实现上是传递值还是传递引用，它们在语义上都是等价的。也就是说，原始类型是值类型还是引用类型，对于程序员来说完全没有区别。你完全可以把 Java 所有的原始类型都想成引用类型，之后你能对它们做的事情，你的编程思路和方式，都不会因此有任何的改变。</p>
<p>从这个角度来看，Java 在语义上是没有值类型的。值类型和引用类型如果同时并存，程序员必须能够在语义上感觉到它们的不同，然而不管原始类型是值类型还是引用类型，作为程序员，你无法感觉到任何的不同。所以你完全可以认为 Java 只有引用类型，把原始类型全都当成引用类型来用，虽然它们确实是用值实现的。</p>
<p>一个在语义上有值类型的语言（比如 C#，Go 和 Swift）必须具有以下两种特性之一（或者两者都有），程序员才能感觉到值类型的存在：</p>
<ol>
<li>deref 操作。这使得你可以用 <code class="language-plaintext highlighter-rouge">*x = 2</code> 这样的语句来改变引用指向的内容，导致共享地址的其它引用看到新的值。你没法通过 <code class="language-plaintext highlighter-rouge">x = 2</code> 让其他值变量得到新的值，所以你感觉到值类型的存在。</li>
<li>像 struct 这样的“值组合类型”。你可以通过 <code class="language-plaintext highlighter-rouge">x.foo = 2</code> 这样的成员赋值改变引用数据（比如 class object）的一部分，使得共享地址的其它引用看到新的值。你没法通过成员赋值让另一个 struct 变量得到新的值，所以你感觉到值类型的存在。</li>
</ol>
<p>实际上，所有的数据都是引用类型就是 Scheme 和 Java 最初的设计原理。原始类型用值来传递数据只是一种性能优化（叫做 inlining），它对于程序员应该是透明（看不见）的。那些在面试时喜欢问“Java 是否所有数据都是引用”，然后当你回答“是”的时候纠正你说“int，boolean 是值类型”的人，都是本本主义者。</p>
<h3 id="思考题">思考题</h3>
<p>有人指出，Java 的引用类型可以是 null，而原始类型不行，所以引用类型和值类型还是有区别的。但是其实这并不能否认本文指出的观点，你可以想想这是为什么吗？</p>
</div>
    
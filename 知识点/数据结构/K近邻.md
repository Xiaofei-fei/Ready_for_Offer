<div id="content_views" class="htmledit_views">
                    <blockquote> 
 <blockquote> 
  <h3><a name="t0"></a>&nbsp;&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;从K近邻算法、距离度量谈到KD树、SIFT+B<a href="https://so.csdn.net/so/search?q=BF%E7%AE%97%E6%B3%95&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=BF%E7%AE%97%E6%B3%95&amp;spm=1001.2101.3001.7020&quot;}">BF算法</a></h3> 
 </blockquote> 
</blockquote> 
<p></p> 
<h2><a name="t1"></a>前言</h2> 
<p>&nbsp; &nbsp; 前两日，在<a href="http://weibo.com/1580904460/z5AvZoes0">微博</a>上说：“到今天为止，我至少亏欠了3篇文章待写：1、<a href="http://blog.csdn.net/v_july_v/article/details/8203674">KD树</a>；2、<a href="https://so.csdn.net/so/search?q=%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C&amp;spm=1001.2101.3001.7020&quot;}">神经网络</a>；3、编程艺术第28章。你看到，blog内的文章与你于别处所见的任何都不同。于是，等啊等，等一台电脑，只好等待..”。得益于田，借了我一台电脑（借他电脑的时候，我连表示感谢，他说“能找到工作全靠你的博客，这点儿小忙还说，不地道”，有的时候，稍许感受到受人信任也是一种压力，愿我不辜负大家对我的信任），于是今天开始<a href="http://blog.csdn.net/v_july_v/article/category/1061301">Top 10 Algorithms in Data Mining</a>系列第三篇文章，即本文「从K近邻算法谈到KD树、SIFT+BBF算法」的创作。</p> 
<p>&nbsp; &nbsp; 一个人坚持自己的兴趣是比较难的，因为太多的人太容易为外界所动了，而尤其当你无法从中得到多少实际性的回报时，所幸，我能一直坚持下来。毕达哥拉斯学派有句名言：“万物皆数”，最近读完「微积分概念发展史」后也感受到了这一点。同时，从算法到数据挖掘、机器学习，再到数学，其中每一个领域任何一个细节都值得探索终生，或许，这就是“终生为学”的意思。</p> 
<p>&nbsp; &nbsp; 本文各部分内容分布如下：</p> 
<ol><li>第一部分讲K近邻算法，其中重点阐述了相关的距离度量表示法，</li><li>第二部分着重讲K近邻算法的实现--KD树，和KD树的插入，删除，最近邻查找等操作，及KD树的一系列相关改进(包括BBF，M树等)；</li><li>第三部分讲KD树的应用：SIFT+kd_BBF搜索算法。</li></ol>
<p>&nbsp; &nbsp; 同时，你将看到，K近邻算法同本系列的前两篇文章所讲的决策树分类贝叶斯分类，及支持向量机SVM一样，也是用于解决分类问题的算法</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <blockquote> 
    <p>&nbsp;&nbsp;<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjMvMTM1MzY2NDU2MV83NjA0LmpwZw?x-oss-process=image/format,png"></p> 
   </blockquote> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 而本数据挖掘十大算法系列也会按照分类，聚类，关联分析，预测回归等问题依次展开阐述。</p> 
<p>&nbsp; &nbsp; OK，行文仓促，本文若有任何漏洞，问题或者错误，欢迎朋友们随时不吝指正，各位的批评也是我继续写下去的动力之一。感谢。</p> 
<p></p> 
<h2><a name="t2"></a>第一部分、K近邻算法</h2> 
<h3><a name="t3"></a>1.1、什么是K近邻算法</h3> 
<p>&nbsp; &nbsp; 何谓K近邻算法，即K-Nearest Neighbor algorithm，简称KNN算法，单从名字来猜想，可以简单粗暴的认为是：K个最近的邻居，当K=1时，算法便成了最近邻算法，即寻找最近的那个邻居。为何要找邻居？打个比方来说，假设你来到一个陌生的村庄，现在你要找到与你有着相似特征的人群融入他们，所谓入伙。</p> 
<p>&nbsp; &nbsp; 用官方的话来说，所谓K近邻算法，即是给定一个训练数据集，对新的输入实例，在训练数据集中找到与该实例最邻近的K个实例（也就是上面所说的K个邻居），这K个实例的多数属于某个类，就把该输入实例分类到这个类中。根据这个说法，咱们来看下引自维基百科上的一幅图：</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <blockquote> 
    <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5NTMzNV82OTg3LnBuZw?x-oss-process=image/format,png"></p> 
   </blockquote> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<blockquote></blockquote> 
<p>&nbsp; &nbsp; 如上图所示，有两类不同的样本数据，分别用蓝色的小正方形和红色的小三角形表示，而图正中间的那个绿色的圆所标示的数据则是待分类的数据。也就是说，现在，我们不知道中间那个绿色的数据是从属于哪一类（蓝色小正方形or红色小三角形），下面，我们就要解决这个问题：给这个绿色的圆分类。<br> &nbsp; &nbsp; 我们常说，物以类聚，人以群分，判别一个人是一个什么样品质特征的人，常常可以从他/她身边的朋友入手，所谓观其友，而识其人。我们不是要判别上图中那个绿色的圆是属于哪一类数据么，好说，从它的邻居下手。但一次性看多少个邻居呢？从上图中，你还能看到：</p> 
<ul><li>如果K=3，绿色圆点的最近的3个邻居是2个红色小三角形和1个蓝色小正方形，少数从属于多数，基于统计的方法，判定绿色的这个待分类点属于红色的三角形一类。</li><li>如果K=5，绿色圆点的最近的5个邻居是2个红色三角形和3个蓝色的正方形，还是少数从属于多数，基于统计的方法，判定绿色的这个待分类点属于蓝色的正方形一类。</li></ul>
<p>&nbsp; &nbsp; 于此我们看到，当无法判定当前待分类点是从属于已知分类中的哪一类时，我们可以依据统计学的理论看它所处的位置特征，衡量它周围邻居的权重，而把它归为(或分配)到权重更大的那一类。这就是K近邻算法的核心思想。</p> 
<h3><a name="t4"></a>1.2、近邻的距离度量表示法</h3> 
<p>&nbsp; &nbsp; 上文第一节，我们看到，K近邻算法的核心在于找到实例点的邻居，这个时候，问题就接踵而至了，如何找到邻居，邻居的判定标准是什么，用什么来度量。这一系列问题便是下面要讲的距离度量表示法。但有的读者可能就有疑问了，我是要找邻居，找相似性，怎么又跟距离扯上关系了？</p> 
<p>&nbsp; &nbsp; 这是因为特征空间中两个实例点的距离可以反应出两个实例点之间的相似性程度。K近邻模型的特征空间一般是n维实数向量空间，使用的距离可以使欧式距离，也是可以是其它距离，既然扯到了距离，下面就来具体阐述下都有哪些距离度量的表示法，权当扩展。</p> 
<ul><li><strong>1. 欧氏距离</strong>，最常见的两点之间或多点之间的距离表示法，又称之为欧几里得度量，它定义于欧几里得空间中，如点 x = (x1,...,xn) 和 y = (y1,...,yn) 之间的距离为：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5ODc3N183NjM4LnBuZw?x-oss-process=image/format,png"></li></ul>
<blockquote> 
 <blockquote> 
  <p>(1)二维平面上两点a(x1,y1)与b(x2,y2)间的欧氏距离：</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTU1Ml80MTA3LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
 <blockquote> 
  <p>(2)三维空间两点a(x1,y1,z1)与b(x2,y2,z2)间的欧氏距离：</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTYwMV85Njc1LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
 <blockquote> 
  <p>(3)两个n维向量a(x11,x12,…,x1n)与 b(x21,x22,…,x2n)间的欧氏距离：</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTY0NF8zODA5LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
 <blockquote> 
  <p>也可以用表示成向量运算的形式：</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTY2NF8yMjU1LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<p>其上，二维平面上两点欧式距离，代码可以如下编写：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">//unixfy：计算欧氏距离</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">double</span> <span class="hljs-title">euclideanDistance</span><span class="hljs-params">(<span class="hljs-type">const</span> vector&lt;<span class="hljs-type">double</span>&gt;&amp; v1, <span class="hljs-type">const</span> vector&lt;<span class="hljs-type">double</span>&gt;&amp; v2)</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     <span class="hljs-built_in">assert</span>(v1.<span class="hljs-built_in">size</span>() == v2.<span class="hljs-built_in">size</span>());</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     <span class="hljs-type">double</span> ret = <span class="hljs-number">0.0</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     <span class="hljs-keyword">for</span> (vector&lt;<span class="hljs-type">double</span>&gt;::size_type i = <span class="hljs-number">0</span>; i != v1.<span class="hljs-built_in">size</span>(); ++i)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     {</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">         ret += (v1[i] - v2[i]) * (v1[i] - v2[i]);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     }</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     <span class="hljs-keyword">return</span> <span class="hljs-built_in">sqrt</span>(ret);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> }</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<ul><li><strong>2. 曼哈顿距离</strong>，我们可以定义曼哈顿距离的正式意义为L1-距离或城市区块距离，也就是在欧几里得空间的固定直角坐标系上两点所形成的线段对轴产生的投影的距离总和。例如在平面上，坐标（x1,&nbsp;y1）的点P1与坐标（x2,&nbsp;y2）的点P2的曼哈顿距离为：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5ODk1NV83NjI3LnBuZw?x-oss-process=image/format,png">，要注意的是，曼哈顿距离依赖座标系统的转度，而非系统在座标轴上的平移或映射。&nbsp;</li></ul>
<div> 
 <div>
  &nbsp; &nbsp; &nbsp;通俗来讲，想象你在曼哈顿要从一个十字路口开车到另外一个十字路口，驾驶距离是两点间的直线距离吗？显然不是，除非你能穿越大楼。而实际驾驶距离就是这个“曼哈顿距离”，此即曼哈顿距离名称的来源， 同时，曼哈顿距离也称为城市街区距离(City&nbsp;Block&nbsp;distance)。
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <p></p> 
  <div> 
   <div>
    (1)二维平面两点a(x1,y1)与b(x2,y2)间的曼哈顿距离&nbsp;
   </div> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTkwOF83ODQ1LnBuZw?x-oss-process=image/format,png">
   </div> 
  </div> 
 </blockquote> 
 <blockquote> 
  <p>(2)两个n维向量a(x11,x12,…,x1n)与&nbsp;b(x21,x22,…,x2n)间的曼哈顿距离&nbsp;</p> 
  <p>&nbsp;&nbsp;<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTkyNF8yMzA0LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<ul><li><strong>3. 切比雪夫距离，</strong>若二个向量或二个点p&nbsp;、and&nbsp;q，其座标分别为<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTE1Nl82MTY5LnBuZw?x-oss-process=image/format,png">及<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTE2N184NzY4LnBuZw?x-oss-process=image/format,png">，则两者之间的切比雪夫距离定义如下：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTE4MV80Nzc0LnBuZw?x-oss-process=image/format,png">，</li></ul>
<div>
 &nbsp; &nbsp; 这也等于以下Lp度量的极值：
 <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTE5N18xMzU5LnBuZw?x-oss-process=image/format,png">，因此切比雪夫距离也称为L∞度量。
</div> 
<div></div> 
<div>
 &nbsp; &nbsp; 以数学的观点来看，切比雪夫距离是由一致范数（uniform&nbsp;norm）（或称为上确界范数）所衍生的度量，也是超凸度量（injective&nbsp;metric&nbsp;space）的一种。
</div> 
<div>
 &nbsp; &nbsp; 在平面几何中，若二点p及q的直角坐标系坐标为
 <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTIzNF82NTUzLnBuZw?x-oss-process=image/format,png">及
 <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTI0NV85MjYyLnBuZw?x-oss-process=image/format,png">，则切比雪夫距离为：
 <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzM5OTIyNV85MDgzLnBuZw?x-oss-process=image/format,png">。
</div> 
<div></div> 
<div> 
 <div>
  &nbsp; &nbsp; 玩过国际象棋的朋友或许知道，国王走一步能够移动到相邻的8个方格中的任意一个。那么国王从格子(x1,y1)走到格子(x2,y2)最少需要多少步？。你会发现最少步数总是max(&nbsp;|&nbsp;x2-x1&nbsp;|&nbsp;,&nbsp;|&nbsp;y2-y1&nbsp;|&nbsp;)&nbsp;步&nbsp;。有一种类似的一种距离度量方法叫切比雪夫距离。
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <div> 
   <div>
    (1)二维平面两点a(x1,y1)与b(x2,y2)间的切比雪夫距离&nbsp;
   </div> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwMDE0Ml84NjYwLnBuZw?x-oss-process=image/format,png">
   </div> 
  </div> 
 </blockquote> 
 <blockquote> 
  <p>(2)两个n维向量a(x11,x12,…,x1n)与&nbsp;b(x21,x22,…,x2n)间的切比雪夫距离&nbsp;　　</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwMDE1OV81NzA2LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
 <blockquote> 
  <p>这个公式的另一种等价形式是&nbsp;</p> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwMDE3NV8zOTc2LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <ul><li><strong>4.&nbsp;闵可夫斯基距离(Minkowski&nbsp;Distance)，</strong>闵氏距离不是一种距离，而是一组距离的定义。</li></ul>
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <div> 
   <div>
    (1)&nbsp;闵氏距离的定义&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
   </div> 
   <div>
    两个n维变量a(x11,x12,…,x1n)与&nbsp;b(x21,x22,…,x2n)间的闵可夫斯基距离定义为：&nbsp;
   </div> 
  </div> 
 </blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwMDM1Nl82MjI1LnBuZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
 <blockquote> 
  <p>其中p是一个变参数。</p> 
  <p>当p=1时，就是曼哈顿距离</p> 
  <p>当p=2时，就是欧氏距离</p> 
  <p>当p→∞时，就是切比雪夫距离&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> 
  <p>根据变参数的不同，闵氏距离可以表示一类的距离。&nbsp;</p> 
 </blockquote> 
</blockquote> 
<div> 
 <ul><li> 
   <div>
    <strong>5.&nbsp;标准化欧氏距离&nbsp;(Standardized&nbsp;Euclidean&nbsp;distance&nbsp;)</strong>，标准化欧氏距离是针对简单欧氏距离的缺点而作的一种改进方案。标准欧氏距离的思路：既然数据各维分量的分布不一样，那先将各个分量都“标准化”到均值、方差相等。至于均值和方差标准化到多少，先复习点统计学知识。
   </div> 
   <div></div> 
   <div>
    假设样本集X的数学期望或均值(mean)为m，标准差(standard&nbsp;deviation，方差开根)为s，那么X的“标准化变量”X*表示为：(X-m）/s，而且标准化变量的数学期望为0，方差为1。
    <br> 即，样本集的标准化过程(standardization)用公式描述就是： 
    <blockquote> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzQ2ODkyN180NjQ1LnBuZw?x-oss-process=image/format,png">
      </div> 
     </blockquote> 
    </blockquote> 
    <p>标准化后的值&nbsp;=&nbsp;&nbsp;(&nbsp;标准化前的值&nbsp;&nbsp;－&nbsp;分量的均值&nbsp;)&nbsp;/分量的标准差　　</p> 
    <p><br> 经过简单的推导就可以得到两个n维向量a(x11,x12,…,x1n)与&nbsp;b(x21,x22,…,x2n)间的标准化欧氏距离的公式：　　</p> 
    <blockquote> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzQ2ODk0NF81Mjk0LnBuZw?x-oss-process=image/format,png">
      </div> 
     </blockquote> 
    </blockquote> 如果将方差的倒数看成是一个权重，这个公式可以看成是一种加权欧氏距离(Weighted&nbsp;Euclidean&nbsp;distance)。&nbsp;
   </div> </li><li> 
   <div>
    <strong>6.&nbsp;马氏距离(Mahalanobis&nbsp;Distance)</strong>
   </div> 
   <div> 
    <p>（1）马氏距离定义&nbsp; &nbsp; &nbsp; &nbsp;<br> 有M个样本向量X1~Xm，<a href="http://zh.wikipedia.org/wiki/%E5%8D%8F%E6%96%B9%E5%B7%AE%E7%9F%A9%E9%98%B5">协方差矩阵</a>记为S，均值记为向量μ，则其中样本向量X到u的马氏距离表示为：&nbsp;</p> 
    <blockquote> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzQ2OTEwN181NTQwLnBuZw?x-oss-process=image/format,png">
      </div> 
     </blockquote> 
    </blockquote> 
    <div>
     （协方差矩阵中每个元素是各个矢量元素之间的协方差Cov(X,Y)，Cov(X,Y) =&nbsp;E{ [X-E(X)] [Y-E(Y)]}，其中E为数学期望）
    </div> 
    <div></div> 而其中向量Xi与Xj之间的马氏距离定义为：&nbsp; &nbsp;&nbsp; 
    <blockquote> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzQ2OTEyMl8zNTM5LnBuZw?x-oss-process=image/format,png">
      </div> 
     </blockquote> 
    </blockquote> 
    <blockquote>
     若协方差矩阵是单位矩阵（各个样本向量之间独立同分布）,则公式就成了：&nbsp; &nbsp; &nbsp; &nbsp;
    </blockquote> 
    <blockquote> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzQ2OTEzM18xMTc5LnBuZw?x-oss-process=image/format,png">
      </div> 
     </blockquote> 
    </blockquote> 
    <blockquote>
     也就是欧氏距离了。　　
    </blockquote> 若协方差矩阵是对角矩阵，公式变成了标准化欧氏距离。
    <br> (2)马氏距离的优缺点：量纲无关，排除变量之间的相关性的干扰。&nbsp;
   </div> 
   <div>
    「微博上的seafood高清版点评道：原来马氏距离是根据协方差矩阵演变，一直被老师误导了，怪不得看Killian在05年NIPS发表的LMNN论文时候老是看到协方差矩阵和半正定，原来是这回事」
   </div> </li><li><strong>7、巴氏距离（Bhattacharyya&nbsp;<strong>Distance</strong>），</strong>在统计中，Bhattacharyya距离测量两个离散或连续概率分布的相似性。它与衡量两个统计样品或种群之间的重叠量的Bhattacharyya系数密切相关。Bhattacharyya距离和Bhattacharyya系数以20世纪30年代曾在印度统计研究所工作的一个统计学家A. Bhattacharya命名。同时，Bhattacharyya系数可以被用来确定两个样本被认为相对接近的，它是用来测量中的类分类的可分离性。</li></ul>
</div> 
<blockquote> 
 <div> 
  <div>
   （1）巴氏距离的定义
  </div> 
 </div> 
 <div> 
  <div>
   对于离散概率分布&nbsp;p和q在同一域&nbsp;X，它被定义为：
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTIxMV83NTgyLnBuZw?x-oss-process=image/format,png">
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   其中：
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTIyM180ODMyLnBuZw?x-oss-process=image/format,png">
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   是Bhattacharyya系数。
  </div> 
 </div> 
 <div> 
  <div>
   对于连续概率分布，Bhattacharyya系数被定义为：
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTIzMl81MzgzLnBuZw?x-oss-process=image/format,png">
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   在
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNjQ2MF8xODUzLmpwZw?x-oss-process=image/format,png">这两种情况下，巴氏距离
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNjUxMF8xMjU4LnBuZw?x-oss-process=image/format,png">并没有服从三角不等式.（值得一提的是，Hellinger距离不服从三角不等式
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTQxMl85OTQyLmpwZw?x-oss-process=image/format,png">）。&nbsp;
  </div> 
 </div> 
 <div> 
  <div>
   对于多变量的高斯分布&nbsp;
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTQ1MF83NDUwLnBuZw?x-oss-process=image/format,png">，
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTQ2OF8xMDA0LnBuZw?x-oss-process=image/format,png">，
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <div> 
  <div></div> 
 </div> 
 <div> 
  <div>
   和是手段和协方差的分布
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTQ4Ml85NTIzLnBuZw?x-oss-process=image/format,png">。
  </div> 
 </div> 
 <div> 
  <div>
   需要注意的是，在这种情况下，第一项中的Bhattacharyya距离与马氏距离有关联。&nbsp;
  </div> 
 </div> 
 <div> 
  <div>
   （2）Bhattacharyya系数
  </div> 
 </div> 
 <div> 
  <div>
   Bhattacharyya系数是两个统计样本之间的重叠量的近似测量，可以被用于确定被考虑的两个样本的相对接近。
  </div> 
 </div> 
 <div> 
  <div>
   计算Bhattacharyya系数涉及集成的基本形式的两个样本的重叠的时间间隔的值的两个样本被分裂成一个选定的分区数，并且在每个分区中的每个样品的成员的数量，在下面的公式中使用
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNTUxMV85NTQ0LnBuZw?x-oss-process=image/format,png">
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <div> 
  <div>
   考虑样品a&nbsp;和&nbsp;b&nbsp;，n是的分区数，并且
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNjcyMV8yNTUyLnBuZw?x-oss-process=image/format,png">，
   <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNjczOF85Mzc3LnBuZw?x-oss-process=image/format,png">被一个&nbsp;和&nbsp;b&nbsp;i的日分区中的样本数量的成员。更多介绍请参看：
   <a href="http://en.wikipedia.org/wiki/Bhattacharyya_coefficient">http://en.wikipedia.org/wiki/Bhattacharyya_coefficient</a>。
  </div> 
 </div> 
</blockquote> 
<div> 
 <ul><li><strong>8. 汉明距离(Hamming distance)</strong>， 两个等长字符串s1与s2之间的汉明距离定义为将其中一个变为另外一个所需要作的最小替换次数。例如字符串“1111”与“1001”之间的汉明距离为2。应用：信息编码（为了增强容错性，应使得编码间的最小汉明距离尽可能大）。</li></ul>
</div> 
<blockquote> 
 <div> 
  <div>
   或许，你还没明白我再说什么，不急，看下
   <a href="http://blog.csdn.net/v_july_v/article/details/7974418">上篇blog</a>
   <span style="color:#333333;">中第78题的第3小题</span>整理的一道面试题目，便一目了然了。如下图所示：
  </div> 
 </div> 
 <blockquote> 
  <div> 
   <div>
    <img alt="" height="130" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMjczM18yNzk3LmpwZw?x-oss-process=image/format,png" width="506">
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">//动态规划：  </span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">//f[i,j]表示s[0...i]与t[0...j]的最小编辑距离。  </span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">f[i,j] = min { f[i<span class="hljs-number">-1</span>,j]+<span class="hljs-number">1</span>,  f[i,j<span class="hljs-number">-1</span>]+<span class="hljs-number">1</span>,  f[i<span class="hljs-number">-1</span>,j<span class="hljs-number">-1</span>]+(s[i]==t[j]?<span class="hljs-number">0</span>:<span class="hljs-number">1</span>) }  </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">//分别表示：添加1个，删除1个，替换1个（相同就不用替换）。 </span></div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
 </div> 
</div> 
<blockquote> 
 <div>
  &nbsp; &nbsp; 与此同时，
  <span style="color:#333333;">面试官还可以继续问下去：那么，请问，如何设计一个比较两篇文章相似性的算法？（</span>
  <span style="color:#333333;">这个问题的讨论可以看看这里：</span>
  <a href="http://t.cn/zl82CAH">http://t.cn/zl82CAH</a>，及这里关于simhash算法的介绍：
  <a href="http://www.cnblogs.com/linecong/archive/2010/08/28/simhash.html">http://www.cnblogs.com/linecong/archive/2010/08/28/simhash.html</a>
  <span style="color:#333333;">），接下来，便引出了下文关于夹角余弦的讨论。</span>
 </div> 
 <div>
  <span style="color:#333333;">（<a href="http://blog.csdn.net/v_july_v/article/details/7974418">上篇blog</a>中第78题的第3小题给出了多种方法，读者可以参看之。同时，程序员编程艺术系列第二十八章将详细阐述这个问题）</span>
 </div> 
</blockquote> 
<ul><li><span style="color:#333333;"><strong>9.&nbsp;夹角余弦(Cosine) </strong>，</span><span style="color:#333333;">几何中夹角余弦可用来衡量两个向量方向的差异，机器学习中借用这一概念来衡量样本向量之间的差异。</span></li></ul>
<blockquote> 
 <p><span style="color:#333333;">(1)在二维空间中向量A(x1,y1)与向量B(x2,y2)的夹角余弦公式：</span></p> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <p><span style="color:#333333;"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMzU5OF85ODU4LnBuZw?x-oss-process=image/format,png"></span></p> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <p><span style="color:#333333;">(2)&nbsp;两个n维样本点a(x11,x12,…,x1n)和b(x21,x22,…,x2n)的夹角余弦</span></p> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <p><span style="color:#333333;"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMzYwN183NzM0LnBuZw?x-oss-process=image/format,png">&nbsp;&nbsp; &nbsp; &nbsp;&nbsp;</span></p> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <p><span style="color:#333333;">类似的，对于两个n维样本点a(x11,x12,…,x1n)和b(x21,x22,…,x2n)，可以使用类似于夹角余弦的概念来衡量它们间的相似程度，</span><span style="color:#333333;">即：</span><span style="color:#333333;">&nbsp; &nbsp; &nbsp; &nbsp;</span></p> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <p><span style="color:#333333;"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMzYzMV82NzM0LnBuZw?x-oss-process=image/format,png"></span></p> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <p><span style="color:#333333;">夹角余弦取值范围为[-1,1]。夹角余弦越大表示两个向量的夹角越小，夹角余弦越小表示两向量的夹角越大。当两个向量的方向重合时夹角余弦取最大值1，当两个向量的方向完全相反夹角余弦取最小值-1。&nbsp;</span></p> 
</blockquote> 
<div> 
 <div> 
  <div> 
   <ul><li><strong>10.&nbsp;杰卡德相似系数(Jaccard&nbsp;similarity&nbsp;coefficient)</strong></li></ul>
  </div> 
 </div> 
</div> 
<blockquote> 
 <div> 
  <div> 
   <div>
    (1)&nbsp;杰卡德相似系数&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    两个集合A和B的交集元素在A，B的并集中所占的比例，称为两个集合的杰卡德相似系数，用符号J(A,B)表示。　
   </div> 
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <div>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMzc2OF84NzU0LnBuZw?x-oss-process=image/format,png">　
    </div> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <div> 
  <div> 
   <div>
    杰卡德相似系数是衡量两个集合的相似度一种指标。
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    (2)&nbsp;杰卡德距离&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    与杰卡德相似系数相反的概念是杰卡德距离(Jaccard&nbsp;distance)。
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    杰卡德距离可用如下公式表示：　　
   </div> 
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <div>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwMzgwNl80NzU0LnBuZw?x-oss-process=image/format,png">
    </div> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <div> 
  <div> 
   <div>
    杰卡德距离用两个集合中不同元素占所有元素的比例来衡量两个集合的区分度。
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    (3)&nbsp;杰卡德相似系数与杰卡德距离的应用&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
   </div> 
  </div> 
 </div> 可将杰卡德相似系数用在衡量样本的相似度上。 
 <div>
  举例：样本A与样本B是两个n维向量，而且所有维度的取值都是0或1，例如：A(0111)和B(1011)。我们将样本看成是一个集合，1表示集合包含该元素，0表示集合不包含该元素。
 </div> 
 <div> 
  <div> 
   <div>
    M11 ：样本A与B都是1的维度的个数
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    M01：样本A是0，样本B是1的维度的个数
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    M10：样本A是1，样本B是0 的维度的个数
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    M00：样本A与B都是0的维度的个数
   </div> 
  </div> 
 </div> 
 <div> 
  <div> 
   <div>
    依据上文给的杰卡德相似系数及杰卡德距离的相关定义，样本A与B的杰卡德相似系数J可以表示为：
   </div> 
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <div>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTIvMTQvMTM1NTQ2NzE0Nl8xMTU3LnBuZw?x-oss-process=image/format,png">
    </div> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <div> 
  <div> 
   <div>
    这里M11+M01+M10可理解为A与B的并集的元素个数，而M11是A与B的交集的元素个数。而样本A与B的杰卡德距离表示为J'：
   </div> 
  </div> 
 </div> 
</blockquote> 
<blockquote> 
 <blockquote>
  <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTIvMTQvMTM1NTQ2NzE1N184NTU1LnBuZw?x-oss-process=image/format,png">
 </blockquote> 
</blockquote> 
<div> 
 <div></div> 
 <div> 
  <ul><li><strong>11.皮尔逊系数(Pearson Correlation Coefficient)</strong></li></ul> &nbsp; &nbsp; 在具体阐述皮尔逊相关系数之前，有必要解释下什么是相关系数 ( Correlation coefficient )与相关距离(Correlation distance)。
 </div> 
 <div>
  &nbsp; &nbsp; 相关系数&nbsp;(&nbsp;Correlation&nbsp;coefficient&nbsp;)的定义是：
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <div>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUxMzM2NF85NTA2LnBuZw?x-oss-process=image/format,png">
    </div> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>(其中，E为数学期望或均值，D为方差，D开根号为标准差，E{ [X-E(X)] [Y-E(Y)]}称为随机变量X与Y的协方差，记为Cov(X,Y)，即Cov(X,Y) =&nbsp;E{ [X-E(X)] [Y-E(Y)]}，而两个变量之间的协方差和标准差的商则称为随机变量X与Y的相关系数，记为<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTIvMDUvMTM1NDcxMDY5MV84MzA1LmpwZw?x-oss-process=image/format,png">)</p> 
<div> 
 <div>
  &nbsp; &nbsp;相关系数衡量随机变量X与Y相关程度的一种方法，相关系数的取值范围是[-1,1]。相关系数的绝对值越大，则表明X与Y相关度越高。当X与Y线性相关时，相关系数取值为1（正线性相关）或-1（负线性相关）。
 </div> 
 <div>
  &nbsp; &nbsp; 具体的，如果有两个变量：X、Y，最终计算出的相关系数的含义可以有如下理解： 
  <ol><li>当相关系数为0时，X和Y两变量无关系。</li><li>当X的值增大（减小），Y值增大（减小），两个变量为正相关，相关系数在0.00与1.00之间。</li><li>当X的值增大（减小），Y值减小（增大），两个变量为负相关，相关系数在-1.00与0.00之间。</li></ol>
 </div> 
 <div>
  &nbsp; &nbsp;相关距离的定义是：
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <div> 
     <blockquote> 
      <div>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUxMzM4N180NDI4LnBuZw?x-oss-process=image/format,png">
      </div> 
      <div></div> 
     </blockquote> 
    </div> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <blockquote> 
   <div>
    OK，接下来，咱们来重点了解下皮尔逊相关系数。
   </div> 
   <div>
    &nbsp; &nbsp; 在统计学中，皮尔逊积矩相关系数（英语：Pearson product-moment correlation coefficient，又称作 PPMCC或PCCs, 用r表示）用于度量两个变量X和Y之间的相关（线性相关），其值介于-1与1之间。
   </div> 
   <p><span style="color:#333333;">通常情况下通过以下取值范围判断变量的相关强度：</span><br><span style="color:#333333;">相关系数&nbsp;&nbsp;&nbsp;&nbsp; 0.8-1.0&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;极强相关</span><br><span style="color:#333333;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0.6-0.8&nbsp;&nbsp;&nbsp;&nbsp; 强相关</span><br><span style="color:#333333;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0.4-0.6&nbsp;&nbsp;&nbsp;&nbsp; 中等程度相关</span><br><span style="color:#333333;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0.2-0.4&nbsp;&nbsp;&nbsp;&nbsp; 弱相关</span><br><span style="color:#333333;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0.0-0.2&nbsp;&nbsp;&nbsp;&nbsp; 极弱相关或无相关</span></p> 
   <div>
    在自然科学领域中，该系数广泛用于度量两个变量之间的相关程度。它是由卡尔·皮尔逊从弗朗西斯·高尔顿在19世纪80年代提出的一个相似却又稍有不同的想法演变而来的。这个相关系数也称作“皮尔森相关系数r”。
   </div> 
   <div>
    <strong>(1)皮尔逊系数的定义</strong>：
   </div> 
   <div>
    两个变量之间的皮尔逊相关系数定义为两个变量之间的协方差和标准差的商：
   </div> 
  </blockquote> 
 </div> 
 <blockquote> 
  <blockquote> 
   <blockquote> 
    <blockquote>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzY2NF85NDc4LnBuZw?x-oss-process=image/format,png">
    </blockquote> 
   </blockquote> 
  </blockquote> 
 </blockquote> 
 <div> 
  <blockquote>
   以上方程定义了总体相关系数, 一般表示成希腊字母ρ(rho)。基于样本对协方差和方差进行估计，可以得到样本标准差, 一般表示成r：
  </blockquote> 
 </div> 
 <blockquote> 
  <blockquote> 
   <blockquote> 
    <blockquote>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzY3NF84MDA1LnBuZw?x-oss-process=image/format,png">
    </blockquote> 
   </blockquote> 
  </blockquote> 
 </blockquote> 
 <div> 
  <blockquote>
   一种等价表达式的是表示成标准分的均值。基于(Xi, Yi)的样本点，样本皮尔逊系数是
  </blockquote> 
 </div> 
 <blockquote> 
  <blockquote> 
   <blockquote> 
    <blockquote>
     <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzY4M18yMTU0LnBuZw?x-oss-process=image/format,png">
    </blockquote> 
   </blockquote> 
  </blockquote> 
 </blockquote> 
 <div> 
  <blockquote> 
   <div>
    <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;其中
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzY5NF85MDQ0LnBuZw?x-oss-process=image/format,png">、
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzcwNl83Mzk1LnBuZw?x-oss-process=image/format,png">&nbsp;及&nbsp;
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzcxNl8yMjA0LnBuZw?x-oss-process=image/format,png">，分别是标准分、样本平均值和样本标准差。
    <br> &nbsp;
   </div> 
   <div>
    或许上面的讲解令你头脑混乱不堪，没关系，我换一种方式讲解，如下：
   </div> 
   <div> 
    <p>假设有两个变量X、Y，那么两变量间的皮尔逊相关系数可通过以下公式计算：</p> 
    <p></p> 
    <ul><li>公式一：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3OTc0M182MDUxLmpwZw?x-oss-process=image/format,png"></li></ul>
   </div> 
  </blockquote> 
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <div> 
     <div>
      注：勿忘了上面说过，“皮尔逊相关系数定义为两个变量之间的协方差和标准差的商”，其中标准差的计算公式为：
      <img alt="" height="60" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3NDQ2MF8xMjg0LmpwZw?x-oss-process=image/format,png" width="320">
     </div> 
    </div> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <blockquote> 
   <div> 
    <ul><li>公式二：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3OTc1NF82NDY1LmpwZw?x-oss-process=image/format,png"></li><li>公式三：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3OTc2MV81MzgxLmpwZw?x-oss-process=image/format,png"></li><li>公式四：<img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3OTc2OF82NzE1LmpwZw?x-oss-process=image/format,png"></li></ul>
    <p></p> 
    <p>以上列出的四个公式等价，其中E是<a href="http://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%A6%E6%9C%9F%E6%9C%9B">数学期望</a>，cov表示<a href="http://zh.wikipedia.org/wiki/%E5%8D%8F%E6%96%B9%E5%B7%AE">协方差</a>，N表示变量取值的个数。</p> 
   </div> 
  </blockquote> 
 </div> 
 <div> 
  <blockquote>
   <strong>(2)皮尔逊相关系数的适用范围</strong>
   <br> 当两个变量的标准差都不为零时，相关系数才有定义，皮尔逊相关系数适用于： 
   <ol><li>两个变量之间是线性关系，都是连续数据。</li><li>两个变量的总体是正态分布，或接近正态的单峰分布。</li><li>两个变量的观测值是成对的，每对观测值之间相互独立。</li></ol>
  </blockquote> 
  <blockquote>
   <strong>(3)如何理解皮尔逊相关系数</strong>
  </blockquote> 
  <blockquote> 
   <p>rubyist：皮尔逊相关系数理解有两个角度</p> 
   <p>其一, 按照高中数学水平来理解, 它很简单, 可以看做将<strong>两组数据首先做Z分数处理之后, 然后两组数据的乘积和除以样本数，</strong>Z分数一般代表正态分布中, 数据偏离中心点的距离.等于变量减掉平均数再除以标准差.(就是高考的标准分类似的处理)</p> 
   <p>样本标准差则等于变量减掉平均数的平方和，再除以样本数，最后再开方，也就是说，方差开方即为标准差，<span style="color:#333333;">样本标准差</span>计算公式为：</p> 
  </blockquote> 
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <div> 
     <blockquote> 
      <p><img alt="" height="47" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjIvMTM1MzU3NTMzOF8yMzA0LmpwZw?x-oss-process=image/format,png" width="100"></p> 
     </blockquote> 
    </div> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <blockquote> 
   <p>所以, 根据这个最朴素的理解,我们可以将公式依次精简为:</p> 
  </blockquote> 
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <div> 
     <blockquote> 
      <p><img alt="" height="300" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUxMTQ1NF84OTU2LnBuZw?x-oss-process=image/format,png" width="250"></p> 
     </blockquote> 
    </div> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <blockquote> 
   <p><span style="color:#333333;">其二, 按照大学的线性数学水平来理解, 它比较复杂一点,可以看做是两组数据的向量夹角的余弦。</span><span style="color:#333333;">下面是关于此皮尔逊系数的</span><span style="color:#333333;">几何学的解释，先来看一幅图，如下所示：</span></p> 
   <p></p> 
  </blockquote> 
 </div> 
</div> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <div> 
     <blockquote> 
      <blockquote>
       <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzg3Nl8xNDkxLnBuZw?x-oss-process=image/format,png">
      </blockquote> 
     </blockquote> 
    </div> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<blockquote> 
 <blockquote> 
  <div> 
   <div> 
    <blockquote> 
     <blockquote> 
      <blockquote>
       回归直线： y=gx(x) [红色] 和 x=gy(y) [蓝色]
      </blockquote> 
     </blockquote> 
    </blockquote> 
   </div> 
  </div> 
 </blockquote> 
</blockquote> 
<div> 
 <div> 
  <blockquote> 
   <div>
    <span style="color:#333333;">如上图，对于没有中心化的数据, 相关系数与两条可能的回归线y=gx(x) 和 x=gy(y) 夹角的余弦值一致。</span> 
    <blockquote></blockquote> 
    <span style="color:#333333;">对于没有中心化的数据 (也就是说, 数据移动一个样本平均值以使其均值为0), 相关系数也可以被视作由两个随机变量 向量 夹角 的 余弦值（见下方）。</span> 
    <blockquote></blockquote> 
    <span style="color:#333333;">举个例子，例如，有5个国家的国民生产总值分别为 10, 20, 30, 50 和 80 亿美元。 假设这5个国家 (顺序相同) 的贫困百分比分别为 11%, 12%, 13%, 15%, and 18% 。 令 x 和 y 分别为包含上述5个数据的向量: x = (1, 2, 3, 5, 8) 和 y = (0.11, 0.12, 0.13, 0.15, 0.18)。</span>
    <br> 利用通常的方法计算两个向量之间的夹角 &nbsp;(参见 数量积), 未中心化 的相关系数是:
   </div> 
   <blockquote> 
    <blockquote> 
     <blockquote>
      <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzg5NF80NzY2LnBuZw?x-oss-process=image/format,png">
     </blockquote> 
    </blockquote> 
   </blockquote> 
   <div>
    <br> 我们发现以上的数据特意选定为完全相关: y = 0.10 + 0.01 x。 于是，皮尔逊相关系数应该等于1。将数据中心化 (通过E(x) = 3.8移动 x 和通过 E(y) = 0.138 移动 y ) 得到 x = (−2.8, −1.8, −0.8, 1.2, 4.2) 和 y = (−0.028, −0.018, −0.008, 0.012, 0.042), 从中
   </div> 
   <blockquote> 
    <blockquote> 
     <blockquote>
      <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjEvMTM1MzUwNzkxMV81ODAyLnBuZw?x-oss-process=image/format,png">
     </blockquote> 
    </blockquote> 
   </blockquote> 
   <p><strong>(4)皮尔逊相关的约束条件</strong></p> 
   <p>从以上解释, 也可以理解皮尔逊相关的约束条件:</p> 
   <ul><li>1 两个变量间有线性关系</li><li>2 变量是连续变量</li><li>3 变量均符合正态分布,且二元分布也符合正态分布</li><li>4 两变量独立</li></ul>
   <p>在实践统计中,一般只输出两个系数,一个是相关系数,也就是计算出来的相关系数大小,在-1到1之间;另一个是独立样本检验系数,用来检验样本一致性。</p> 
  </blockquote> 
 </div> 
 <div>
  &nbsp; &nbsp; &nbsp;简单说来，各种“距离”的应用场景简单概括为，空间：欧氏距离，路径：曼哈顿距离，国际象棋国王：切比雪夫距离，以上三种的统一形式:闵可夫斯基距离，加权：标准化欧氏距离，排除量纲和依存：马氏距离，向量差距：夹角余弦，编码差别：汉明距离，集合近似度：杰卡德类似系数与距离，相关：相关系数与相关距离。
 </div> 
</div> 
<div></div> 
<h3><a name="t5"></a>1.3、K值的选择</h3> 
<p>&nbsp; &nbsp; 除了上述1.2节如何定义邻居的问题之外，还有一个选择多少个邻居，即K值定义为多大的问题。不要小看了这个K值选择问题，因为它对K近邻算法的结果会产生重大影响。如李航博士的一书「统计学习方法」上所说：</p> 
<ol><li>如果选择较小的K值，就相当于用较小的领域中的训练实例进行预测，“学习”近似误差会减小，只有与输入实例较近或相似的训练实例才会对预测结果起作用，与此同时带来的问题是“学习”的估计误差会增大，换句话说，K值的减小就意味着整体模型变得复杂，容易发生过拟合；</li><li>如果选择较大的K值，就相当于用较大领域中的训练实例进行预测，其优点是可以减少学习的估计误差，但缺点是学习的近似误差会增大。这时候，与输入实例较远（不相似的）训练实例也会对预测器作用，使预测发生错误，且K值的增大就意味着整体的模型变得简单。</li><li>K=N，则完全不足取，因为此时无论输入实例是什么，都只是简单的预测它属于在训练实例中最多的累，模型过于简单，忽略了训练实例中大量有用信息。</li></ol>
<div>
 &nbsp; &nbsp; 在实际应用中，K值一般取一个比较小的数值，例如采用
 <a href="http://zh.wikipedia.org/zh/%E4%BA%A4%E5%8F%89%E9%A9%97%E8%AD%89">交叉验证</a>法（简单来说，就是一部分样本做训练集，一部分做测试集）来选择最优的K值。
</div> 
<p></p> 
<h2><a name="t6"></a>第二部分、K近邻算法的实现：KD树</h2> 
<h3><a name="t7"></a>2.0、背景</h3> 
<p>&nbsp; &nbsp; 之前blog内曾经介绍过SIFT特征匹配算法，特征点匹配和数据库查、图像检索本质上是同一个问题，都可以归结为一个通过距离函数在高维矢量之间进行相似性检索的问题，如何快速而准确地找到查询点的近邻，不少人提出了很多高维空间索引结构和近似查询的算法。</p> 
<p>&nbsp; &nbsp; 一般说来，索引结构中相似性查询有两种基本的方式：</p> 
<ol><li>一种是范围查询，范围查询时给定查询点和查询距离阈值，从数据集中查找所有与查询点距离小于阈值的数据</li><li>另一种是K近邻查询，就是给定查询点及正整数K，从数据集中找到距离查询点最近的K个数据，当K=1时，它就是最近邻查询。</li></ol>
<p>&nbsp; &nbsp; 同样，针对特征点匹配也有两种方法：</p> 
<ul><li>最容易的办法就是线性扫描，也就是我们常说的穷举搜索，依次计算样本集E中每个样本到输入实例点的距离，然后抽取出计算出来的最小距离的点即为最近邻点。此种办法简单直白，但当样本集或训练集很大时，它的缺点就立马暴露出来了，举个例子，在物体识别的问题中，可能有数千个甚至数万个SIFT特征点，而去一一计算这成千上万的特征点与输入实例点的距离，明显是不足取的。</li><li>另外一种，就是构建数据索引，因为实际数据一般都会呈现簇状的聚类形态，因此我们想到建立数据索引，然后再进行快速匹配。索引树是一种树结构索引方法，其基本思想是对搜索空间进行层次划分。根据划分的空间是否有混叠可以分为Clipping和Overlapping两种。前者划分空间没有重叠，其代表就是k-d树；后者划分空间相互有交叠，其代表为R树。</li></ul>
<p>&nbsp; &nbsp; 而关于<a href="http://blog.csdn.net/v_JULY_v/article/details/6530142">R树</a>本blog内之前已有介绍(同时，<span style="color:#333333;">关于基于R树的最近邻查找，还可以看下这篇文章：</span><a href="http://blog.sina.com.cn/s/blog_72e1c7550101dsc3.html">http://blog.sina.com.cn/s/blog_72e1c7550101dsc3.html</a>)，本文着重介绍k-d树。</p> 
<p>&nbsp; &nbsp; 1975年，来自斯坦福大学的Jon Louis Bentley在ACM杂志上发表的一篇论文：Multidimensional Binary Search Trees Used for Associative Searching 中正式提出和阐述的了如下图形式的把空间划分为多个部分的k-d树。</p> 
<blockquote> 
 <blockquote> 
  <p></p> 
  <blockquote> 
   <p><img alt="" height="265" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg5Nzc5Nl82NzYzLmpwZw?x-oss-process=image/format,png" width="325"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<h3><a name="t8"></a>2.1、什么是KD树</h3> 
<p>&nbsp; &nbsp; Kd-树是K-dimension tree的缩写，是对数据点在k维空间（如二维(x，y)，三维(x，y，z)，k维(x1，y，z..)）中划分的一种数据结构，主要应用于多维空间关键数据的搜索（如：范围搜索和最近邻搜索）。本质上说，Kd-树就是一种平衡二叉树。</p> 
<p>&nbsp; &nbsp; 首先必须搞清楚的是，k-d树是一种空间划分树，说白了，就是把整个空间划分为特定的几个部分，然后在特定空间的部分内进行相关搜索操作。想像一个三维(多维有点为难你的想象力了)空间，kd树按照一定的划分规则把这个三维空间划分了多个空间，如下图所示：</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p><img alt="" height="300" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwNDI1NV8yNDYxLnBuZw?x-oss-process=image/format,png" width="320"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<h3><a name="t9"></a>2.2、KD树的构建</h3> 
<p>&nbsp; &nbsp; kd树构建的伪代码如下图所示（伪代码来自<span style="color:#333333;">《图像局部不变特性特征与描述》王永明 王贵锦 编著</span>）：</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjQvMTM1MzczMjA5MV80MjI1LmpwZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<p></p> 
<p>&nbsp; &nbsp; 再举一个简单直观的实例来介绍k-d树构建算法。假设有6个二维数据点{(2,3)，(5,4)，(9,6)，(4,7)，(8,1)，(7,2)}，数据点位于二维空间内，如下图所示。为了能有效的找到最近邻，k-d树采用分而治之的思想，即将整个空间划分为几个小部分，首先，粗黑线将空间一分为二，然后在两个子空间中，细黑直线又将整个空间划分为四部分，最后虚黑直线将这四部分进一步划分。</p> 
<blockquote> 
 <p><img alt="" height="352" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwNTkyMV8zMDY2LmpwZw?x-oss-process=image/format,png" width="560"></p> 
</blockquote> 
<p>&nbsp; &nbsp; 6个二维数据点{(2,3)，(5,4)，(9,6)，(4,7)，(8,1)，(7,2)}构建kd树的具体步骤为：</p> 
<ol><li>确定：split域=x。具体是：6个数据点在x，y维度上的数据方差分别为39，28.63，所以在x轴上<a href="http://zh.wikipedia.org/zh/%E6%96%B9%E5%B7%AE">方差</a>更大，故split域值为x；</li><li>确定：Node-data = （7,2）。具体是：根据x维上的值将数据排序，6个数据的中值(所谓中值，即中间大小的值)为7，所以Node-data域位数据点（7,2）。这样，该节点的分割超平面就是通过（7,2）并垂直于：split=x轴的直线x=7；</li><li>确定：左子空间和右子空间。具体是：分割超平面x=7将整个空间分为两部分：x&lt;=7的部分为左子空间，包含3个节点={(2,3),(5,4),(4,7)}；另一部分为右子空间，包含2个节点={(9,6)，(8,1)}；</li></ol>
<div>
 &nbsp; &nbsp; 如上算法所述，kd树的构建是一个递归过程，我们对左子空间和右子空间内的数据重复根节点的过程就可以得到一级子节点（5,4）和（9,6），同时将空间和数据集进一步细分，如此往复直到空间中只包含一个数据点。
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjQvMTM1MzczNTQ2Ml80NTIyLmpwZw?x-oss-process=image/format,png">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<p>&nbsp; &nbsp; 与此同时，经过对上面所示的空间划分之后，我们可以看出，点(7,2)可以为根结点，从根结点出发的两条红粗斜线指向的(5,4)和(9,6)则为根结点的左右子结点，而(2,3)，(4,7)则为(5,4)的左右孩子(通过两条细红斜线相连)，最后，(8,1)为(9,6)的左孩子(通过细红斜线相连)。如此，便形成了下面这样一棵k-d树：</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" height="228" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwNjI3Nl80MDk1LmpwZw?x-oss-process=image/format,png" width="434"></p> 
 </blockquote> 
</blockquote> 
<blockquote></blockquote> 
<p>&nbsp; &nbsp; k-d树的数据结构</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwNDY0OF8yMDg2LmpwZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 针对上表给出的kd树的数据结构，转化成具体代码如下所示(<span style="color:#333333;">注，本文以下代码分析基于Rob Hess维护的sift库)</span>：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">/** a node in a k-d tree */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> ki;                      <span class="hljs-comment">/**&lt; partition key index */</span><span class="hljs-comment">//关键点直方图方差最大向量系列位置</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> kv;                   <span class="hljs-comment">/**&lt; partition key value */</span><span class="hljs-comment">//直方图方差最大向量系列中最中间模值</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> leaf;                    <span class="hljs-comment">/**&lt; 1 if node is a leaf, 0 otherwise */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span>* features;    <span class="hljs-comment">/**&lt; features at this node */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> n;                       <span class="hljs-comment">/**&lt; number of features */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* kd_left;     <span class="hljs-comment">/**&lt; left child */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* kd_right;    <span class="hljs-comment">/**&lt; right child */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">};</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 也就是说，如之前所述，kd树中，kd代表k-dimension，每个节点即为一个k维的点。每个非叶节点可以想象为一个分割超平面，用垂直于坐标轴的超平面将空间分为两个部分，这样递归的从根节点不停的划分，直到没有实例为止。经典的构造k-d tree的规则如下：</p> 
<ol><li>随着树的深度增加，循环的选取坐标轴，作为分割超平面的法向量。对于3-d tree来说，根节点选取x轴，根节点的孩子选取y轴，根节点的孙子选取z轴，根节点的曾孙子选取x轴，这样循环下去。</li><li>每次均为所有对应实例的中位数的实例作为切分点，切分点作为父节点，左右两侧为划分的作为左右两子树。</li></ol>
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjQvMTM1MzczMDY5NF84OTQxLmdpZg">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<p>&nbsp; &nbsp; 对于n个实例的k维数据来说，建立kd-tree的时间复杂度为O(k*n*logn)。</p> 
<p></p> 
<p>&nbsp; &nbsp; 以下是构建k-d树的代码：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* <span class="hljs-built_in">kdtree_build</span>( <span class="hljs-keyword">struct</span> feature* features, <span class="hljs-type">int</span> n )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* kd_root;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( ! features  ||  n &lt;= <span class="hljs-number">0</span> )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: kdtree_build(): no features, %s, line %d\n"</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				__FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span> <span class="hljs-literal">NULL</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//初始化</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	kd_root = <span class="hljs-built_in">kd_node_init</span>( features, n );  <span class="hljs-comment">//n--number of features,initinalize root of tree.</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">expand_kd_node_subtree</span>( kd_root );  <span class="hljs-comment">//kd tree expand</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> kd_root;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 上面的涉及初始化操作的两个函数kd_node_init，及expand_kd_node_subtree代码分别如下所示：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-type">static</span> <span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* <span class="hljs-built_in">kd_node_init</span>( <span class="hljs-keyword">struct</span> feature* features, <span class="hljs-type">int</span> n )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{                                     <span class="hljs-comment">//n--number of features</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* kd_node;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	kd_node = (<span class="hljs-keyword">struct</span> kd_node*)(<span class="hljs-built_in">malloc</span>( <span class="hljs-built_in">sizeof</span>( <span class="hljs-keyword">struct</span> kd_node ) ));</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">memset</span>( kd_node, <span class="hljs-number">0</span>, <span class="hljs-built_in">sizeof</span>( <span class="hljs-keyword">struct</span> kd_node ) ); <span class="hljs-comment">//0填充</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	kd_node-&gt;ki = <span class="hljs-number">-1</span>; <span class="hljs-comment">//???????</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	kd_node-&gt;features = features;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	kd_node-&gt;n = n;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> kd_node;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">static</span> <span class="hljs-type">void</span> <span class="hljs-title">expand_kd_node_subtree</span><span class="hljs-params">( <span class="hljs-keyword">struct</span> kd_node* kd_node )</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">/* base case: leaf node */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( kd_node-&gt;n == <span class="hljs-number">1</span>  ||  kd_node-&gt;n == <span class="hljs-number">0</span> )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{   <span class="hljs-comment">//叶节点               //伪叶节点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		kd_node-&gt;leaf = <span class="hljs-number">1</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">assign_part_key</span>( kd_node ); <span class="hljs-comment">//get ki,kv</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">partition_features</span>( kd_node ); <span class="hljs-comment">//creat left and right children,特征点ki位置左树比右树模值小,kv作为分界模值</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">                                 <span class="hljs-comment">//kd_node中关键点已经排序</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( kd_node-&gt;kd_left )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">expand_kd_node_subtree</span>( kd_node-&gt;kd_left );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( kd_node-&gt;kd_right )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">expand_kd_node_subtree</span>( kd_node-&gt;kd_right );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 构建完kd树之后，如今进行最近邻搜索呢？从下面这个动态gif图中（摘自：<a href="http://dl2.iteye.com/upload/attachment/0042/4128/3f19162d-13ab-326b-9315-85e111de37ed.gif">http://dl2.iteye.com/upload/attachment/0042/4128/3f19162d-13ab-326b-9315-85e111de37ed.gif</a>），你是否能看出些许端倪呢？</p> 
<p style="text-align:center;"><img alt="" src="https://img-blog.csdnimg.cn/20210627233513745.gif"></p> 
<p>&nbsp; &nbsp; k-d树算法可以分为两大部分，除了上部分有关k-d树本身这种数据结构建立的算法，另一部分是在建立的k-d树上各种诸如插入，删除，查找(最邻近查找)等操作涉及的算法。下面，咱们依次来看kd树的插入、删除、查找操作。</p> 
<p></p> 
<h3><a name="t10"></a>2.3、KD树的插入</h3> 
<p>&nbsp; &nbsp; 元素插入到一个K-D树的方法和二叉检索树类似。本质上，在偶数层比较x坐标值，而在奇数层比较y坐标值。当我们到达了树的底部，（也就是当一个空指针出现），我们也就找到了结点将要插入的位置。生成的K-D树的形状依赖于结点插入时的顺序。给定N个点，其中一个结点插入和检索的平均代价是O(log2N)。</p> 
<p>&nbsp; &nbsp; 下面4副图(<span style="color:#333333;">来源：中国地质大学电子课件</span>)说明了插入顺序为(a) Chicago, (b) Mobile, (c) Toronto, and (d) Buffalo，建立空间K-D树的示例：</p> 
<p><img alt="" height="380" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjUvMTM1Mzg1NzcyM184MDc5LmpwZw?x-oss-process=image/format,png" width="330"><img alt="" height="380" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjUvMTM1Mzg1NzczM181MjE2LmpwZw?x-oss-process=image/format,png" width="330"><img alt="" height="380" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjUvMTM1Mzg1Nzc0NF8yNTQxLmpwZw?x-oss-process=image/format,png" width="330"><img alt="" height="380" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjUvMTM1Mzg1Nzc1MF8xMzQ0LmpwZw?x-oss-process=image/format,png" width="330"></p> 
<p>&nbsp; &nbsp; 应该清楚，这里描述的插入过程中，每个结点将其所在的平面分割成两部分。因比，Chicago 将平面上所有结点分成两部分，一部分所有的结点x坐标值小于35，另一部分结点的x坐标值大于或等于35。同样Mobile将所有x坐标值大于35的结点以分成两部分，一部分结点的Y坐标值是小于10，另一部分结点的Y坐标值大于或等于10。后面的Toronto、Buffalo也按照一分为二的规则继续划分。</p> 
<h3><a name="t11"></a>2.4、KD树的删除</h3> 
<div>
 &nbsp; &nbsp; KD树的删除可以用递归程序来实现。我们假设希望从K-D树中删除结点（a,b）。如果（a,b）的两个子树都为空，则用空树来代替（a,b）。否则，在（a,b）的子树中寻找一个合适的结点来代替它，譬如(c,d)，则递归地从K-D树中删除（c,d）。一旦(c,d)已经被删除，则用（c,d）代替（a,b）。假设(a,b)是一个X识别器，那么，它得替代节点要么是（a,b）左子树中的X坐标最大值的结点，要么是（a,b）右子树中x坐标最小值的结点。
</div> 
<div>
 &nbsp; &nbsp; 也就是说，跟普通二叉树(包括如下图所示的
 <a href="http://blog.csdn.net/v_JULY_v/article/details/6105630">红黑树</a>)结点的删除是同样的思想：用被删除节点A的左子树的最右节点或者A的右子树的最左节点作为替代A的节点(比如，下图红黑树中，若要删除根结点26，第一步便是用23或28取代根结点26)。
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="150" src="https://imgconvert.csdnimg.cn/aHR0cDovL2hpLmNzZG4ubmV0L2F0dGFjaG1lbnQvMjAxMDEyLzI5LzgzOTQzMjNfMTI5MzYxMzMwNkNHekUuanBn?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp;当(a,b)的右子树为空时，找到（a,b）左子树中具有x坐标最大的结点，譬如（c,d），将(a,b)的左子树放到(c,d)的右子树中，且在树中从它的上一层递归地应用删除过程（也就是（a,b）的左子树） 。
</div> 
<div>
 &nbsp; &nbsp; 下面来举一个实际的例子(来源：中国地质大学电子课件，原课件错误已经在下文中订正)，如下图所示，原始图像及对应的kd树，现在要删除图中的A结点，请看一系列删除步骤：
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjUvMTM1Mzg1ODY5OV80ODU0LmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp; 要删除上图中结点A，选择结点A的右子树中X坐标值最小的结点，这里是C，C成为根，如下图：
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg2MTAwOF80NTM3LmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp;&nbsp;&nbsp;从C的右子树中找出一个结点代替先前C的位置，
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg2MTE0NV8zNjIxLmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp;&nbsp;这里是D，并将D的左子树转为它的右子树，D代替先前C的位置，如下图：
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg2MTAyMF8yNDg4LmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp;&nbsp;在D的新右子树中，找X坐标最小的结点，这里为H，H代替D的位置，
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg2MTAyOF85MTc0LmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp; 在D的右子树中找到一个Y坐标最小的值，这里是I，将I代替原先H的位置，从而A结点从图中顺利删除，如下图所示：
</div> 
<blockquote> 
 <div> 
  <blockquote> 
   <div>
    <img alt="" height="250" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjYvMTM1Mzg2MDUzNl8yMTg1LmpwZw?x-oss-process=image/format,png" width="600">
   </div> 
  </blockquote> 
 </div> 
</blockquote> 
<div>
 &nbsp; &nbsp; 从一个K-D树中删除结点(a,b)的问题变成了在(a,b)的子树中寻找x坐标为最小的结点。不幸的是寻找最小x坐标值的结点比二叉检索树中解决类似的问题要复杂得多。特别是虽然最小x坐标值的结点一定在x识别器的左子树中，但它同样可在y识别器的两个子树中。因此关系到检索，且必须注意检索坐标，以使在每个奇数层仅检索2个子树中的一个。
 <br> &nbsp; &nbsp; 从K-D树中删除一个结点是代价很高的，很清楚删除子树的根受到子树中结点个数的限制。用TPL（T）表示树T总的路径长度。可看出树中子树大小的总和为TPL（T）+N。 以随机方式插入N个点形成树的TPL是O(N*log2N),这就意味着从一个随机形成的K-D树中删除一个随机选取的结点平均代价的上界是O(log2N) 。
</div> 
<h3><a name="t12"></a>2.5、KD树的最近邻搜索算法</h3> 
<p style="margin-left:10px;">&nbsp; &nbsp; 现实生活中有许多问题需要在多维数据的快速分析和快速搜索，对于这个问题最常用的方法是所谓的kd树。在k-d树中进行数据的查找也是特征匹配的重要环节，其目的是检索在k-d树中与查询点距离最近的数据点。在一个N维的笛卡儿空间在两个点之间的距离是由下述公式确定：</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p style="margin-left:10px;"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjQvMTM1MzczMDM3NV84NDA5LmdpZg"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p><span style="color:#444444;">2.5.1、k-d树查询算法的伪代码</span></p> 
<p style="margin-left:10px;">&nbsp; &nbsp; k-d树查询算法的伪代码如下所示：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">算法：k-d树最邻近查找</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">输入：Kd，    <span class="hljs-comment">//k-d tree类型</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     target  <span class="hljs-comment">//查询数据点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">输出：nearest， <span class="hljs-comment">//最邻近数据点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">     dist      <span class="hljs-comment">//最邻近数据点和查询点间的距离</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-number">1.</span> If Kd为<span class="hljs-literal">NULL</span>，则设dist为infinite并返回</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-number">2.</span> <span class="hljs-comment">//进行二叉查找，生成搜索路径</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   Kd_point = &amp;Kd；                   <span class="hljs-comment">//Kd-point中保存k-d tree根节点地址</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   nearest = Kd_point -&gt; Node-data；  <span class="hljs-comment">//初始化最近邻点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   <span class="hljs-keyword">while</span>（Kd_point）</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　push（Kd_point）到search_path中； <span class="hljs-comment">//search_path是一个堆栈结构，存储着搜索路径节点指针</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">      If Dist（nearest，target） &gt; Dist（Kd_point -&gt; Node-data，target）</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　nearest  = Kd_point -&gt; Node-data；    <span class="hljs-comment">//更新最近邻点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　Min_dist = <span class="hljs-built_in">Dist</span>(Kd_point，target）；  <span class="hljs-comment">//更新最近邻点与查询点间的距离  ***/</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　s = Kd_point -&gt; split；                       <span class="hljs-comment">//确定待分割的方向</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　If target[s] &lt;= Kd_point -&gt; Node-data[s]     <span class="hljs-comment">//进行二叉查找</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　Kd_point = Kd_point -&gt; left；</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　<span class="hljs-keyword">else</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="23"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　Kd_point = Kd_point -&gt;right；</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="24"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   End <span class="hljs-keyword">while</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="25"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="26"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-number">3.</span> <span class="hljs-comment">//回溯查找</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="27"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   <span class="hljs-keyword">while</span>（search_path != <span class="hljs-literal">NULL</span>）</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="28"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　back_point = 从search_path取出一个节点指针；   <span class="hljs-comment">//从search_path堆栈弹栈</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="29"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　s = back_point -&gt; split；                      <span class="hljs-comment">//确定分割方向</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="30"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="31"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　If Dist（target[s]，back_point -&gt; Node-data[s]） &lt; Max_dist   <span class="hljs-comment">//判断还需进入的子空间</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="32"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　If target[s] &lt;= back_point -&gt; Node-data[s]</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="33"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　　　Kd_point = back_point -&gt; right；  <span class="hljs-comment">//如果target位于左子空间，就应进入右子空间</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="34"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　<span class="hljs-keyword">else</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="35"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　　　Kd_point = back_point -&gt; left;    <span class="hljs-comment">//如果target位于右子空间，就应进入左子空间</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="36"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　将Kd_point压入search_path堆栈；</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="37"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="38"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　If Dist（nearest，target） &gt; Dist（Kd_Point -&gt; Node-data，target）</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="39"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　nearest  = Kd_point -&gt; Node-data；                 <span class="hljs-comment">//更新最近邻点</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="40"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   　　　　Min_dist = Dist（Kd_point -&gt; Node-data,target）；  <span class="hljs-comment">//更新最近邻点与查询点间的距离的</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="41"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">   End <span class="hljs-keyword">while</span> </div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p style="margin-left:10px;">&nbsp; &nbsp; <strong>读者来信点评@yhxyhxyhx</strong>，在“将Kd_point压入search_path堆栈；”这行代码后，应该是调到步骤2再往下走二分搜索的逻辑一直到叶结点，我写了一个递归版本的二维kd tree的搜索函数你对比的看看：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">void</span> <span class="hljs-title">innerGetClosest</span><span class="hljs-params">(NODE* pNode, PT point, PT&amp; res, <span class="hljs-type">int</span>&amp; nMinDis)</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span> (<span class="hljs-literal">NULL</span> == pNode)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> nCurDis = <span class="hljs-built_in">abs</span>(point.x - pNode-&gt;pt.x) + <span class="hljs-built_in">abs</span>(point.y - pNode-&gt;pt.y);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span> (nMinDis &lt; <span class="hljs-number">0</span> || nCurDis &lt; nMinDis)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		nMinDis = nCurDis;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		res = pNode-&gt;pt;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span> (pNode-&gt;splitX &amp;&amp; point.x &lt;= pNode-&gt;pt.x || !pNode-&gt;splitX &amp;&amp; point.y &lt;= pNode-&gt;pt.y)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">innerGetClosest</span>(pNode-&gt;pLft, point, res, nMinDis);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">else</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">innerGetClosest</span>(pNode-&gt;pRgt, point, res, nMinDis);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> rang = pNode-&gt;splitX ? <span class="hljs-built_in">abs</span>(point.x - pNode-&gt;pt.x) : <span class="hljs-built_in">abs</span>(point.y - pNode-&gt;pt.y);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span> (rang &gt; nMinDis)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	NODE* pGoInto = pNode-&gt;pLft;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span> (pNode-&gt;splitX &amp;&amp; point.x &gt; pNode-&gt;pt.x || !pNode-&gt;splitX &amp;&amp; point.y &gt; pNode-&gt;pt.y)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		pGoInto = pNode-&gt;pRgt;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">innerGetClosest</span>(pGoInto, point, res, nMinDis);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p style="margin-left:10px;">&nbsp; &nbsp; 下面，以两个简单的实例<span style="color:#444444;">(</span><span style="color:#444444;">例子来自图像局部不变特性特征与描述一书</span><span style="color:#444444;">)</span>来描述最邻近查找的基本思路。</p> 
<p>2.5.2、举例：查询点<span style="color:#444444;">（2.1,3.1）</span></p> 
<p style="margin-left:10px;">&nbsp; &nbsp; 星号表示要查询的点（2.1,3.1）。通过二叉搜索，顺着搜索路径很快就能找到最邻近的近似点，也就是叶子节点（2,3）。而找到的叶子节点并不一定就是最邻近的，最邻近肯定距离查询点更近，应该位于以查询点为圆心且通过叶子节点的圆域内。为了找到真正的最近邻，还需要进行相关的‘回溯'操作。也就是说，算法首先沿搜索路径反向查找是否有距离查询点更近的数据点。</p> 
<p style="margin-left:10px;">&nbsp; &nbsp; 以查询<span style="color:#444444;">（2.1,3.1）为例：</span></p> 
<ol><li>二叉树搜索：先从（7,2）点开始进行二叉查找，然后到达（5,4），最后到达（2,3），此时搜索路径中的节点为&lt;(7,2)，(5,4)，(2,3)&gt;，首先以（2,3）作为当前最近邻点，计算其到查询点（2.1,3.1）的距离为0.1414，</li><li>回溯查找：在得到（2,3）为查询点的最近点之后，回溯到其父节点（5,4），并判断在该父节点的其他子节点空间中是否有距离查询点更近的数据点。以（2.1,3.1）为圆心，以0.1414为半径画圆，如下图所示。发现该圆并不和超平面y = 4交割，因此不用进入（5,4）节点右子空间中(图中灰色区域)去搜索；</li><li><span style="color:#444444;">最后，再回溯到（7,2），以（2.1,3.1）为圆心，以0.1414为半径的圆更不会与x = 7超平面交割，因此不用进入（7,2）右子空间进行查找。至此，搜索路径中的节点已经全部回溯完，结束整个搜索，返回最近邻点（2,3），最近距离为0.1414。</span></li></ol>
<p style="margin-left:10px;"><img alt="" height="365" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQxNDU5Nl85NjExLmpwZw?x-oss-process=image/format,png" width="750"></p> 
<p><span style="color:#444444;">2.5.3、举例：查询点</span><span style="color:#444444;">（<span style="color:#444444;">2，4.5</span>）</span></p> 
<p style="margin-left:10px;">&nbsp; &nbsp; 一个复杂点了例子如查找点为（2，4.5），具体步骤依次如下：</p> 
<ol><li>同样先进行二叉查找，先从（7,2）查找到（5,4）节点，在进行查找时是由y = 4为分割超平面的，由于查找点为y值为4.5，因此进入右子空间查找到（4,7），形成搜索路径&lt;(7,2)，(5,4)，(4,7)&gt;，但（4,7）与目标查找点的距离为3.202，而（5,4）与查找点之间的距离为3.041，所以（5,4）为查询点的最近点；</li><li>以（2，4.5）为圆心，以3.041为半径作圆，如下图所示。可见该圆和y = 4超平面交割，所以需要进入（5,4）左子空间进行查找，也就是将（2,3）节点加入搜索路径中得&lt;(7,2)，(2,3)&gt;；于是接着搜索至（2,3）叶子节点，（2,3）距离（2,4.5）比（5,4）要近，所以最近邻点更新为（2，3），最近距离更新为1.5；</li><li>回溯查找至（5,4），直到最后回溯到根结点（7,2）的时候，以（2,4.5）为圆心1.5为半径作圆，并不和x = 7分割超平面交割，如下图所示。至此，搜索路径回溯完，返回最近邻点（2,3），最近距离1.5。</li></ol>
<p style="margin-left:10px;"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQxNTI5OV8zMTI0LmpwZw?x-oss-process=image/format,png"></p> 
<p><span style="color:#444444;">&nbsp; &nbsp; 上述两次实例表明，当查询点的邻域与分割超平面两侧空间交割时，需要查找另一侧子空间，导致检索过程复杂，效率下降。</span></p> 
<p></p> 
<div>
 &nbsp; &nbsp; 一般来讲，最临近搜索只需要检测几个叶子结点即可，如下图所示：　　
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <blockquote> 
     <div>
      <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTIvMDQvMTM1NDYyNTUzMV80MDgyLmpwZw?x-oss-process=image/format,png">
     </div> 
    </blockquote> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<div>
 &nbsp; &nbsp; 但是，如果当实例点的分布比较糟糕时，几乎要遍历所有的结点，如下所示：
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <div> 
    <blockquote> 
     <div>
      <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTIvMDQvMTM1NDYyNTU1M18zNjUxLmpwZw?x-oss-process=image/format,png">
     </div> 
    </blockquote> 
   </div> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p><span style="color:#444444;">&nbsp; &nbsp; 研究表明N个节点的K维k-d树搜索过程时间复杂度为：t</span>worst<span style="color:#444444;">=O（kN</span>1-1/k<span style="color:#444444;">）。</span></p> 
<p>&nbsp; &nbsp; 同时，<span style="color:#444444;">以上为了介绍方便，讨论的是二维或三维情形。但在实际的应用中，如SIFT特征矢量128维，SURF特征矢量64维，维度都比较大，直接利用k-d树快速检索（维数不超过20）的性能急剧下降，几乎接近贪婪线性扫描。假设数据集的维数为D，一般来说要求数据的规模N满足N»2</span>D<span style="color:#444444;">，才能达到高效的搜索。所以这就引出了一系列对k-d树算法的改进：BBF算法，和一系列M树、VP树、MVP树等高维空间索引树(<span style="color:#444444;">下文2.6节</span><span style="color:#444444;">kd树近邻搜索算法的改进：BBF算法，与2.7节</span><span style="color:#444444;">球树、M树、VP树、MVP树</span>)。</span></p> 
<h3><a name="t13"></a>2.6、kd树近邻搜索算法的改进：BBF算法</h3> 
<p>&nbsp; &nbsp; 咱们顺着上一节的思路，参考《统计学习方法》一书上的内容，再来总结下kd树的最近邻搜索算法：</p> 
<p>输入：以构造的kd树，目标点x；<br> 输出：x 的最近邻<br> 算法步骤如下：</p> 
<ol><li>在kd树种找出包含目标点x的叶结点：从根结点出发，递归地向下搜索kd树。若目标点x当前维的坐标小于切分点的坐标，则移动到左子结点，否则移动到右子结点，直到子结点为叶结点为止。</li><li>以此叶结点为“当前最近点”。</li><li>递归的向上回溯，在每个结点进行以下操作：<br> （a）如果该结点保存的实例点比当前最近点距离目标点更近，则更新“当前最近点”，也就是说以该实例点为“当前最近点”。<br> （b）当前最近点一定存在于该结点一个子结点对应的区域，检查子结点的父结点的另一子结点对应的区域是否有更近的点。具体做法是，检查另一子结点对应的区域是否以目标点位球心，以目标点与“当前最近点”间的距离为半径的圆或超球体相交：<br> 如果相交，可能在另一个子结点对应的区域内存在距目标点更近的点，移动到另一个子结点，接着，继续递归地进行最近邻搜索；<br> 如果不相交，向上回溯。</li><li>当<strong>回退到根结点时，搜索结束</strong>，最后的“当前最近点”即为x 的最近邻点。</li></ol>
<p>&nbsp; &nbsp; 如果实例点是随机分布的，那么kd树搜索的平均计算复杂度是O（logN），这里的N是训练实例树。所以说，kd树更适用于训练实例数远大于空间维数时的k近邻搜索，当空间维数接近训练实例数时，它的效率会迅速下降，一降降到“解放前”：线性扫描的速度。</p> 
<p>&nbsp; &nbsp; 也正因为上述k最近邻搜索算法的第4个步骤中的所述：“<strong>回退到根结点时，搜索结束”</strong>，每个最近邻点的查询比较完成过程最终都要回退到根结点而结束，而导致了许多不必要回溯访问和比较到的结点，这些多余的损耗在高维度数据查找的时候，搜索效率将变得相当之地下，那有什么办法可以改进这个原始的kd树最近邻搜索算法呢？</p> 
<p>&nbsp; &nbsp; 从上述标准的kd树查询过程可以看出其搜索过程中的“回溯”是由“查询路径”决定的，并没有考虑查询路径上一些数据点本身的一些性质。一个简单的改进思路就是将“查询路径”上的结点进行排序，如按各自分割超平面（也称bin）与查询点的距离排序，也就是说，回溯检查总是从优先级最高（Best Bin）的树结点开始。</p> 
<p>&nbsp; &nbsp; 针对此BBF机制，读者Feng&amp;书童点评道：</p> 
<ol><li>在某一层，分割面是第ki维，分割值是kv，那么 abs(q[ki]-kv) 就是没有选择的那个分支的优先级，也就是计算的是那一维上的距离；</li><li>同时，从优先队列里面取节点只在某次搜索到叶节点后才发生，计算过距离的节点不会出现在队列的，比如1~10这10个节点，你第一次搜索到叶节点的路径是1-5-7，那么1，5，7是不会出现在优先队列的。换句话说，优先队列里面存的都是查询路径上节点对应的相反子节点，比如：搜索左子树，就把对应这一层的右节点存进队列。</li></ol>
<p>&nbsp; &nbsp; 如此，就引出了本节要讨论的kd树最近邻搜索算法的改进：BBF（Best-Bin-First）查询算法，它是由发明sift算法的David Lowe在1997的一篇文章中针对高维数据提出的一种近似算法，此算法能确保优先检索包含最近邻点可能性较高的空间，此外，BBF机制还设置了一个运行超时限定。采用了BBF查询机制后，kd树便可以有效的扩展到高维数据集上。</p> 
<p>&nbsp; &nbsp; 伪代码如下图所示（图取自图像局部不变特性特征与描述一书）：</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQyMDExOV8zNzA5LmpwZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 还是以上面的查询（2,4.5）为例，搜索的算法流程为：</p> 
<ol><li>将（7,2）压人优先队列中；</li><li>提取优先队列中的（7,2），由于（2,4.5）位于（7,2）分割超平面的左侧，所以检索其左子结点（5,4）。同时，根据BBF机制”搜索左/右子树，就把对应这一层的兄弟结点即右/左结点存进队列”，将其（5,4）对应的兄弟结点即右子结点（9,6）压人优先队列中，此时优先队列为{（9,6）}，最佳点为（7,2）；然后一直检索到叶子结点（4,7），此时优先队列为{（2,3），（9,6）}，“最佳点”则为（5,4）；</li><li>提取优先级最高的结点（2,3），重复步骤2，直到优先队列为空。</li></ol>
<p>&nbsp; &nbsp; 如你在下图所见到的那样（话说，用鼠标在图片上写字着实不好写）：</p> 
<p><img alt="" height="150" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQwNjI3Nl80MDk1LmpwZw?x-oss-process=image/format,png" width="250"><img alt="" height="206" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMjAvMTM1MzQyMDcwMF85NDIyLmpwZw?x-oss-process=image/format,png" width="330"></p> 
<p></p> 
<h3><a name="t14"></a>2.7、球树、M树、VP树、MVP树</h3> 
<p>2.7.1、球树</p> 
<p>&nbsp; &nbsp; 咱们来针对上文内容总结回顾下，针对下面这样一棵kd树：</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI2ODUyOV8yNjk5LmpwZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 现要找它的最近邻。</p> 
<p>&nbsp; &nbsp; 通过上文2.5节，总结来说，我们已经知道：</p> 
<p>1、为了找到一个给定目标点的最近邻，需要从树的根结点开始向下沿树找出目标点所在的区域，如下图所示，给定目标点，用星号标示，我们似乎一眼看出，有一个点离目标点最近，因为它落在以目标点为圆心以较小长度为半径的虚线圆内，但为了确定是否可能还村庄一个最近的近邻，我们会先检查叶节点的同胞结点，然叶节点的同胞结点在图中所示的阴影部分，虚线圆并不与之相交，所以确定同胞叶结点不可能包含更近的近邻。</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI3MDYwMF81MDQyLmpwZw?x-oss-process=image/format,png"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>2、于是我们回溯到父节点，并检查父节点的同胞结点，父节点的同胞结点覆盖了图中所有横线X轴上的区域。因为虚线圆与右上方的<strong>矩形(</strong>KD树把二维平面划分成一个一个矩形<strong>)</strong>相交...</p> 
<p>&nbsp; &nbsp; 如上，我们看到，KD树是可用于有效寻找最近邻的一个树结构，但这个树结构其实并不完美，当处理不均匀分布的数据集时便会呈现出一个基本冲突：既邀请树有完美的平衡结构，又要求待查找的区域近似方形，但不管是近似方形，还是矩形，甚至正方形，都不是最好的使用形状，因为他们都有角。</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p>&nbsp; &nbsp; &nbsp; &nbsp;&nbsp; <img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI2Nzg0MV84NDUzLmpwZw?x-oss-process=image/format,png"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 什么意思呢？就是说，在上图中，如果黑色的实例点离目标点星点再远一点，那么势必那个虚线圆会如红线所示那样扩大，以致与左上方矩形的右下角相交，既然相交了，那么势必又必须检查这个左上方矩形，而实际上，最近的点离星点的距离很近，检查左上方矩形区域已是多余。于此我们看见，KD树把二维平面划分成一个一个矩形，但矩形区域的角却是个难以处理的问题。</p> 
<p>&nbsp; &nbsp; 解决的方案就是使用如下图所示的球树：</p> 
<blockquote> 
 <blockquote> 
  <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI3MTY4Nl81NzkzLmpwZw?x-oss-process=image/format,png"></p> 
 </blockquote> 
</blockquote> 
<blockquote>
 先从球中选择一个离球的中心最远的点，然后选择第二个点离第一个点最远，将球中所有的点分配到离这两个聚类中心最近的一个上，然后计算每个聚类的中心，以及聚类能够包含它所有数据点所需的最小半径。这种方法的优点是分裂一个包含n个殊绝点的球的成本只是随n呈线性增加。
</blockquote> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI3MTc3NF83OTMxLmpwZw?x-oss-process=image/format,png"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<blockquote></blockquote> 
<p>&nbsp; &nbsp; 使用球树找出给定目标点的最近邻方法是，首先自上而下贯穿整棵树找出包含目标点所在的叶子，并在这个球里找出与目标点最靠近的点，这将确定出目标点距离它的最近邻点的一个上限值，然后跟KD树查找一样，检查同胞结点，如果目标点到同胞结点中心的距离超过同胞结点的半径与当前的上限值之和，那么同胞结点里不可能存在一个更近的点；否则的话，必须进一步检查位于同胞结点以下的子树。</p> 
<p>&nbsp; &nbsp; 如下图，目标点还是用一个星表示，黑色点是当前已知的的目标点的最近邻，灰色球里的所有内容将被排除，因为灰色球的中心点离的太远，所以它不可能包含一个更近的点，像这样，递归的向树的根结点进行回溯处理，检查所有可能包含一个更近于当前上限值的点的球。</p> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI3MTEwMV85NDY4LmpwZw?x-oss-process=image/format,png"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; 球树是自上而下的建立，和KD树一样，根本问题就是要找到一个好的方法将包含数据点集的球分裂成两个，在实践中，不必等到叶子结点只有两个胡数据点时才停止，可以采用和KD树一样的方法，一旦结点上的数据点打到预先设置的最小数量时，便可提前停止建树过程。</p> 
<p>&nbsp; &nbsp; 也就是上面所述，先从球中选择一个离球的中心最远的点，然后选择第二个点离第一个点最远，将球中所有的点分配到离这两个聚类中心最近的一个上，然后计算每个聚类的中心，以及聚类能够包含它所有数据点所需的最小半径。这种方法的优点是分裂一个包含n个殊绝点的球的成本只是随n呈线性增加(注：本小节内容主要来自参考条目19：<span style="color:#333333;">数据挖掘实用机器学习技术，[新西兰]Ian H.Witten 著，第4章4.7节</span>)。</p> 
<p>2.7.2、VP树与MVP树简介</p> 
<p>&nbsp; &nbsp;&nbsp;高维特征向量的距离索引问题是基于内容的图像检索的一项关键技术，目前经常采用的解决办法是首先对高维特征空间做降维处理，然后采用包括四叉树、kd树、R树族等在内的主流多维索引结构，这种方法的出发点是：目前的主流多维索引结构在处理维数较低的情况时具有比较好的效率，但对于维数很高的情况则显得力不从心(即所谓的维数危机) 。</p> 
<p>&nbsp; &nbsp; 实验结果表明当特征空间的维数超过20 的时候，效率明显降低，而可视化特征往往采用高维向量描述，一般情况下可以达到10^2的量级，甚至更高。在表示图像可视化特征的高维向量中各维信息的重要程度是不同的，通过降维技术去除属于次要信息的特征向量以及相关性较强的特征向量，从而降低特征空间的维数，这种方法已经得到了一些实际应用。</p> 
<p>&nbsp; &nbsp;&nbsp;然而这种方法存在不足之处采用降维技术可能会导致有效信息的损失，尤其不适合于处理特征空间中的特征向量相关性很小的情况。另外主流的多维索引结构大都针对欧氏空间，设计需要利用到欧氏空间的几何性质，而图像的相似性计算很可能不限于基于欧氏距离。这种情况下人们越来越关注基于距离的度量空间高维索引结构可以直接应用于高维向量相似性查询问题。</p> 
<p>&nbsp; &nbsp; 度量空间中对象之间的距离度量只能利用三角不等式性质，而不能利用其他几何性质。向量空间可以看作由实数坐标串组成的特殊度量空间，目前针对度量空间的高维索引问题提出的索引结构有很多种大致可以作如下分类，如下图所示：</p> 
<blockquote> 
 <blockquote>
  &nbsp; &nbsp;&nbsp;
  <img alt="" height="168" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI4NTExMl82MjI5LmpwZw?x-oss-process=image/format,png" width="457">
 </blockquote> 
</blockquote> 
<div>
 &nbsp; &nbsp; 其中，VP树和MVP树中特征向量的举例表示为：
</div> 
<blockquote> 
 <blockquote> 
  <blockquote> 
   <p><img alt="" height="59" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMTEvMzAvMTM1NDI4NTQxMV84NzM4LmpwZw?x-oss-process=image/format,png" width="398"></p> 
  </blockquote> 
 </blockquote> 
</blockquote> 
<p>&nbsp; &nbsp; &nbsp;读者点评：</p> 
<ol><li>UESTC_HN_AY_GUOBO：现在主要是在kdtree的基础上有了mtree或者mvptree，其实关键还是pivot的选择，以及度量空间中算法怎么减少距离计算；</li><li>mandycool：mvp-tree，是利用三角形不等式来缩小搜索区域的，不过mvp-tree的目标稍有不同，查询的是到query点的距离小于某个值r的点；另外作者test的数据集只有20维，不知道上百维以后效果如何，而减少距离计算的一个思路是做embedding，通过不等式排除掉一部分点。</li></ol>
<p>&nbsp; &nbsp; 更多内容请参见论文1：DIST ANCE-BASED INDEXING FOR HIGH-DIMENSIONAL METRIC SP ACES，作者：Tolga Bozkaya &amp; Meral Ozsoyoglu，及论文2：基于度量空间高维索引结构VP-tree及MVP-tree的图像检索，王志强，甘国辉，程起敏。</p> 
<p>&nbsp; &nbsp; 当然，如果你觉得上述论文还不够满足你胃口的话，这里有一大堆nearest neighbor algorithms相关的论文可供你看：<a href="http://scholar.google.com.hk/scholar?q=nearest+neighbor+algorithms&amp;btnG=&amp;hl=zh-CN&amp;as_sdt=0&amp;as_vis=1">http://scholar.google.com.hk/scholar?q=nearest+neighbor+algorithms&amp;btnG=&amp;hl=zh-CN&amp;as_sdt=0&amp;as_vis=1</a>（其中，这篇可以看下：<span style="color:#333333;">Spill-Trees，An investigation of practical approximate nearest neighbor algorithms</span>）。</p> 
<p></p> 
<h2><a name="t15"></a>第三部分、KD树的应用：SIFT+KD_BBF搜索算法</h2> 
<h3><a name="t16"></a>3.1、SIFT特征匹配算法 &nbsp; &nbsp;</h3> 
<p>&nbsp; &nbsp; 之前本blog内阐述过图像特征匹配SIFT算法，写过五篇文章，这五篇文章分别为：</p> 
<ul><li><a href="http://blog.csdn.net/v_JULY_v/article/details/6186942">九、图像特征提取与匹配之SIFT算法</a>&nbsp; &nbsp; &nbsp;&nbsp;(sift算法系列五篇文章)</li><li><a href="http://blog.csdn.net/v_JULY_v/archive/2011/03/05/6225117.aspx">九（续）、sift算法的编译与实现</a></li><li><a href="http://blog.csdn.net/v_JULY_v/archive/2011/03/13/6245939.aspx">九（再续）、教你一步一步用c语言实现sift算法、上</a></li><li><a href="http://blog.csdn.net/v_JULY_v/archive/2011/03/13/6246213.aspx">九（再续）、教你一步一步用c语言实现sift算法、下</a></li><li><a href="http://blog.csdn.net/v_JULY_v/archive/2011/06/20/6555899.aspx">九（三续）：SIFT算法的应用--<strong>目标识别</strong>之Bag-of-words模型</a></li></ul>
<p>&nbsp; &nbsp; 不熟悉SIFT算法相关概念的可以看上述几篇文章，这里不再做赘述。与此同时，本文此部分也作为<a href="http://blog.csdn.net/v_JULY_v/article/details/6305212">十五个经典算法研究系列</a>里SIFT算法的九之四续。</p> 
<p>&nbsp; &nbsp; OK，我们知道，在sift算法中，给定两幅图片图片，若要做特征匹配，一般会先提取出图片中的下列相关属性作为特征点：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment"><span class="hljs-comment">/**</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">Structure to represent an affine invariant image feature.  The fields</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">x, y, a, b, c represent the affine region around the feature:</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment"></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">a(x-u)(x-u) + 2b(x-u)(y-v) + c(y-v)(y-v) = 1</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">*/</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> x;                      <span class="hljs-comment">/**&lt; x coord */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> y;                      <span class="hljs-comment">/**&lt; y coord */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> a;                      <span class="hljs-comment">/**&lt; Oxford-type affine region parameter */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> b;                      <span class="hljs-comment">/**&lt; Oxford-type affine region parameter */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> c;                      <span class="hljs-comment">/**&lt; Oxford-type affine region parameter */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> scl;                    <span class="hljs-comment">/**&lt; scale of a Lowe-style feature */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> ori;                    <span class="hljs-comment">/**&lt; orientation of a Lowe-style feature */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> d;                         <span class="hljs-comment">/**&lt; descriptor length */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> descr[FEATURE_MAX_D];   <span class="hljs-comment">/**&lt; descriptor */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> type;                      <span class="hljs-comment">/**&lt; feature type, OXFD or LOWE */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> category;                  <span class="hljs-comment">/**&lt; all-purpose feature category */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span>* fwd_match;     <span class="hljs-comment">/**&lt; matching feature from forward image */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span>* bck_match;     <span class="hljs-comment">/**&lt; matching feature from backmward image */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span>* mdl_match;     <span class="hljs-comment">/**&lt; matching feature from model */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="23"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	CvPoint2D64f img_pt;           <span class="hljs-comment">/**&lt; location in image */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="24"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	CvPoint2D64f mdl_pt;           <span class="hljs-comment">/**&lt; location in model */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="25"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">void</span>* feature_data;            <span class="hljs-comment">/**&lt; user-definable data */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="26"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">char</span> dense;						<span class="hljs-comment">/*表征特征点所处稠密程度*/</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="27"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">};</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 而后在sift.h文件中定义两个关键函数，这里，我们把它们称之为函数一，和函数二，如下所示：</p> 
<p>函数一的声明：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><span class="hljs-function"><span class="hljs-keyword">extern</span> <span class="hljs-type">int</span> <span class="hljs-title">sift_features</span><span class="hljs-params">( IplImage* img, <span class="hljs-keyword">struct</span> feature** feat )</span></span>;</code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>函数一的实现：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">int</span> <span class="hljs-title">sift_features</span><span class="hljs-params">( IplImage* img, <span class="hljs-keyword">struct</span> feature** feat )</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> _sift_features( img, feat, SIFT_INTVLS, SIFT_SIGMA, SIFT_CONTR_THR,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">							SIFT_CURV_THR, SIFT_IMG_DBL, SIFT_DESCR_WIDTH,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">							SIFT_DESCR_HIST_BINS );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>从上述函数一的实现中，我们可以看到，它内部实际上调用的是这个函数：_sift_features(..)，也就是下面马上要分析的函数二。</p> 
<p>函数二的声明：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">extern</span> <span class="hljs-type">int</span> _sift_features( IplImage* img, <span class="hljs-keyword">struct</span> feature** feat, <span class="hljs-type">int</span> intvls,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">						  <span class="hljs-type">double</span> sigma, <span class="hljs-type">double</span> contr_thr, <span class="hljs-type">int</span> curv_thr,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">						  <span class="hljs-type">int</span> img_dbl, <span class="hljs-type">int</span> descr_width, <span class="hljs-type">int</span> descr_hist_bins );</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>函数二的实现：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-type">int</span> _sift_features( IplImage* img, <span class="hljs-keyword">struct</span> feature** feat, <span class="hljs-type">int</span> intvls,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				   <span class="hljs-type">double</span> sigma, <span class="hljs-type">double</span> contr_thr, <span class="hljs-type">int</span> curv_thr,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				   <span class="hljs-type">int</span> img_dbl, <span class="hljs-type">int</span> descr_width, <span class="hljs-type">int</span> descr_hist_bins )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	IplImage* init_img;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	IplImage*** gauss_pyr, *** dog_pyr;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	CvMemStorage* storage;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	CvSeq* features;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> octvs, i, n = <span class="hljs-number">0</span>,n0 = <span class="hljs-number">0</span>,n1 = <span class="hljs-number">0</span>,n2 = <span class="hljs-number">0</span>,n3 = <span class="hljs-number">0</span>,n4 = <span class="hljs-number">0</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> start;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">/* check arguments */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( ! img )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">fatal_error</span>( <span class="hljs-string">"NULL pointer error, %s, line %d"</span>,  __FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( ! feat )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">fatal_error</span>( <span class="hljs-string">"NULL pointer error, %s, line %d"</span>,  __FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">/* build scale space pyramid; smallest dimension of top level is ~4 pixels */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	start=<span class="hljs-built_in">GetTickCount</span>();</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	init_img = <span class="hljs-built_in">create_init_img</span>( img, img_dbl, sigma );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	octvs = <span class="hljs-built_in">log</span>( (<span class="hljs-type">float</span>)(<span class="hljs-built_in">MIN</span>( init_img-&gt;width, init_img-&gt;height )) ) / <span class="hljs-built_in">log</span>((<span class="hljs-type">float</span>)(<span class="hljs-number">2.0</span>)) <span class="hljs-number">-5</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="23"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	gauss_pyr = <span class="hljs-built_in">build_gauss_pyr</span>( init_img, octvs, intvls, sigma );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="24"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	dog_pyr = <span class="hljs-built_in">build_dog_pyr</span>( gauss_pyr, octvs, intvls );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="25"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">" creat the pyramid use %d\n"</span>,<span class="hljs-built_in">GetTickCount</span>()-start);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="26"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="27"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	storage = <span class="hljs-built_in">cvCreateMemStorage</span>( <span class="hljs-number">0</span> );    <span class="hljs-comment">//创建存储内存，0为默认64k</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="28"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	start=<span class="hljs-built_in">GetTickCount</span>();</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="29"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	features = <span class="hljs-built_in">scale_space_extrema</span>( dog_pyr, octvs, intvls, contr_thr,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="30"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		curv_thr, storage );  <span class="hljs-comment">//在DOG空间寻找极值点，确定关键点位置</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="31"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">" find the extrum points in DOG use %d\n"</span>,<span class="hljs-built_in">GetTickCount</span>()-start);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="32"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="33"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">calc_feature_scales</span>( features, sigma, intvls ); <span class="hljs-comment">//计算关键点的尺度</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="34"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="35"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( img_dbl )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="36"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">adjust_for_img_dbl</span>( features );  <span class="hljs-comment">//如果原始空间图扩大，特征点坐标就缩小</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="37"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	start=<span class="hljs-built_in">GetTickCount</span>();</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="38"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">calc_feature_oris</span>( features, gauss_pyr );  <span class="hljs-comment">//在gaussian空间计算关键点的主方向和幅值</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="39"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">" get the main oritation use %d\n"</span>,<span class="hljs-built_in">GetTickCount</span>()-start);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="40"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="41"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	start=<span class="hljs-built_in">GetTickCount</span>();</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="42"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">compute_descriptors</span>( features, gauss_pyr, descr_width, descr_hist_bins ); <span class="hljs-comment">//建立关键点描述器</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="43"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">" compute the descriptors use %d\n"</span>,<span class="hljs-built_in">GetTickCount</span>()-start);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="44"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="45"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">/* sort features by decreasing scale and move from CvSeq to array */</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="46"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//start=GetTickCount();</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="47"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//cvSeqSort( features, (CvCmpFunc)feature_cmp, NULL ); //?????</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="48"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	n = features-&gt;total;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="49"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	*feat = (feature*)(<span class="hljs-built_in">calloc</span>( n, <span class="hljs-built_in">sizeof</span>(<span class="hljs-keyword">struct</span> feature) ));</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="50"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	*feat = (feature*)(<span class="hljs-built_in">cvCvtSeqToArray</span>( features, *feat, CV_WHOLE_SEQ )); <span class="hljs-comment">//整条链表放在feat指向的内存</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="51"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="52"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">for</span>( i = <span class="hljs-number">0</span>; i &lt; n; i++ )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="53"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="54"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">free</span>( (*feat)[i].feature_data );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="55"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		(*feat)[i].feature_data = <span class="hljs-literal">NULL</span>;  <span class="hljs-comment">//释放ddata(r,c,octv,intvl,xi,scl_octv)</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="56"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>((*feat)[i].dense == <span class="hljs-number">4</span>) ++n4;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="57"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>((*feat)[i].dense == <span class="hljs-number">3</span>) ++n3;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="58"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>((*feat)[i].dense == <span class="hljs-number">2</span>) ++n2;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="59"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>((*feat)[i].dense == <span class="hljs-number">1</span>) ++n1;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="60"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">else</span>						 ++n0;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="61"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="62"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="63"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//fprintf( stderr, " move features from sequce to array use %d\n",GetTickCount()-start);</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="64"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//start=GetTickCount();</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="65"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"In the total feature points the extent4 points is %d\n"</span>,n4);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="66"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"In the total feature points the extent3 points is %d\n"</span>,n3);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="67"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"In the total feature points the extent2 points is %d\n"</span>,n2);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="68"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"In the total feature points the extent1 points is %d\n"</span>,n1);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="69"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"In the total feature points the extent0 points is %d\n"</span>,n0);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="70"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">cvReleaseMemStorage</span>( &amp;storage );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="71"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">cvReleaseImage</span>( &amp;init_img );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="72"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">release_pyr</span>( &amp;gauss_pyr, octvs, intvls + <span class="hljs-number">3</span> );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="73"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">release_pyr</span>( &amp;dog_pyr, octvs, intvls + <span class="hljs-number">2</span> );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="74"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//fprintf( stderr, " free the pyramid use %d\n",GetTickCount()-start);</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="75"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> n;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="76"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 说明：上面的函数二，包含了SIFT算法中几乎所有函数，是SIFT算法的核心。本文不打算一一分析上面所有函数，只会抽取其中涉及到BBF查询机制相关的函数。</p> 
<p></p> 
<h3><a name="t17"></a>3.2、K个最小近邻的查找：大顶堆优先级队列</h3> 
<p>&nbsp; &nbsp; 上文中一直在讲最近邻问题，也就是说只找最近的那唯一一个邻居，但如果现实中需要我们找到k个最近的邻居。该如何做呢？对的，之前blog内曾相近阐述过<a href="http://blog.csdn.net/v_JULY_v/article/details/6370650">寻找最小的k个数</a>的问题，显然，寻找k个最近邻与寻找最小的k个数的问题如出一辙。</p> 
<p>&nbsp; &nbsp; 回忆下寻找k个最小的数中关于构造大顶堆的解决方案：</p> 
<p>&nbsp; &nbsp; “<span style="color:#333333;">寻找最小的k个树，更好的办法是维护k个元素的最大堆，即用容量为k的最大堆存储最先遍历到的k个数，并假设它们即是最小的k个数，建堆费时O（k）后，有k1<span style="color:#333333;">O（n*logk）</span><span style="color:#333333;">。此方法得益于在堆中，查找等各项操作时间复杂度均为logk。</span>”</span></p> 
<p>&nbsp; &nbsp; 根据上述方法，咱们来写大顶堆最小优先级队列相关代码实现：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">void</span>* <span class="hljs-title">minpq_extract_min</span><span class="hljs-params">( <span class="hljs-keyword">struct</span> min_pq* min_pq )</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">void</span>* data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( min_pq-&gt;n &lt; <span class="hljs-number">1</span> )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: PQ empty, %s line %d\n"</span>, __FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span> <span class="hljs-literal">NULL</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	data = min_pq-&gt;pq_array[<span class="hljs-number">0</span>].data; <span class="hljs-comment">//root of tree</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	min_pq-&gt;n--;   <span class="hljs-comment">//0</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	min_pq-&gt;pq_array[<span class="hljs-number">0</span>] = min_pq-&gt;pq_array[min_pq-&gt;n];</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">restore_minpq_order</span>( min_pq-&gt;pq_array, <span class="hljs-number">0</span>, min_pq-&gt;n );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">                                              <span class="hljs-comment">//0</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 上述函数中，restore_minpq_order的实现如下：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">static</span> <span class="hljs-type">void</span> <span class="hljs-title">restore_minpq_order</span><span class="hljs-params">( <span class="hljs-keyword">struct</span> pq_node* pq_array, <span class="hljs-type">int</span> i, <span class="hljs-type">int</span> n )</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{                                                          <span class="hljs-comment">//0     //0</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">pq_node</span> tmp;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> l, r, min = i;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	l = <span class="hljs-built_in">left</span>( i ); <span class="hljs-comment">//2*i+1,????????????</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	r = <span class="hljs-built_in">right</span>( i );<span class="hljs-comment">//2*i+2,????????????</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( l &lt; n )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( pq_array[l].key &lt; pq_array[i].key )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			min = l;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( r &lt; n )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( pq_array[r].key &lt; pq_array[min].key )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			min = r;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( min != i )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		tmp = pq_array[min];</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		pq_array[min] = pq_array[i];</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		pq_array[i] = tmp;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">restore_minpq_order</span>( pq_array, min, n );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<h3><a name="t18"></a>3.3、KD树近邻搜索改进之BBF算法</h3> 
<p>&nbsp; &nbsp; 原理在上文第二部分已经阐述明白，结合大顶堆找最近的K个近邻思想，相关主体代码如下：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-comment">//KD树近邻搜索改进之BBF算法</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-function"><span class="hljs-type">int</span> <span class="hljs-title">kdtree_bbf_knn</span><span class="hljs-params">( <span class="hljs-keyword">struct</span> kd_node* kd_root, <span class="hljs-keyword">struct</span> feature* feat, <span class="hljs-type">int</span> k,</span></span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					<span class="hljs-keyword">struct</span> feature*** nbrs, <span class="hljs-type">int</span> max_nn_chks )                  <span class="hljs-comment">//2</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{                                               <span class="hljs-comment">//200</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* expl;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">min_pq</span>* min_pq;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">feature</span>* tree_feat, ** _nbrs;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">bbf_data</span>* bbf_data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> i, t = <span class="hljs-number">0</span>, n = <span class="hljs-number">0</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">if</span>( ! nbrs  ||  ! feat  ||  ! kd_root )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: NULL pointer error, %s, line %d\n"</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				__FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">return</span> <span class="hljs-number">-1</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	_nbrs = (feature**)(<span class="hljs-built_in">calloc</span>( k, <span class="hljs-built_in">sizeof</span>( <span class="hljs-keyword">struct</span> feature* ) )); <span class="hljs-comment">//2</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	min_pq = <span class="hljs-built_in">minpq_init</span>(); </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">minpq_insert</span>( min_pq, kd_root, <span class="hljs-number">0</span> ); <span class="hljs-comment">//把根节点加入搜索序列中</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-comment">//队列有东西就继续搜，同时控制在t&lt;200步内</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="23"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">while</span>( min_pq-&gt;n &gt; <span class="hljs-number">0</span>  &amp;&amp;  t &lt; max_nn_chks )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="24"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{                 </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="25"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//刚进来时,从kd树根节点搜索,exp1是根节点 </span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="26"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//后进来时,exp1是min_pq差值最小的未搜索节点入口</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="27"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//同时按min_pq中父,子顺序依次检验,保证父节点的差值比子节点小.这样减少返回搜索时间</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="28"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		expl = (<span class="hljs-keyword">struct</span> kd_node*)<span class="hljs-built_in">minpq_extract_min</span>( min_pq );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="29"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( ! expl )                                        </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="30"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{                                                   </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="31"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: PQ unexpectedly empty, %s line %d\n"</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="32"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					__FILE__, __LINE__ );                         </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="33"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-keyword">goto</span> fail;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="34"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="35"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="36"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//从根节点(或差值最小节点)搜索,根据目标点与节点模值的差值(小)</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="37"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//确定在kd树的搜索路径,同时存储各个节点另一入口地址\同级搜索路径差值.</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="38"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//存储时比较父节点的差值,如果子节点差值比父节点差值小,交换两者存储位置,</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="39"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//使未搜索节点差值小的存储在min_pq的前面,减小返回搜索的时间.</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="40"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		expl = <span class="hljs-built_in">explore_to_leaf</span>( expl, feat, min_pq ); </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="41"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( ! expl )                                  </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="42"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{                                             </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="43"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: PQ unexpectedly empty, %s line %d\n"</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="44"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					__FILE__, __LINE__ );                   </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="45"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-keyword">goto</span> fail;                                  </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="46"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}                                             </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="47"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="48"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">for</span>( i = <span class="hljs-number">0</span>; i &lt; expl-&gt;n; i++ )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="49"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{ </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="50"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-comment">//使用exp1-&gt;n原因:如果是叶节点,exp1-&gt;n=1,如果是伪叶节点,exp1-&gt;n=0.</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="51"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			tree_feat = &amp;expl-&gt;features[i];</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="52"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			bbf_data = (<span class="hljs-keyword">struct</span> bbf_data*)(<span class="hljs-built_in">malloc</span>( <span class="hljs-built_in">sizeof</span>( <span class="hljs-keyword">struct</span> bbf_data ) ));</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="53"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-keyword">if</span>( ! bbf_data )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="54"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="55"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: unable to allocate memory,"</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="56"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					<span class="hljs-string">" %s line %d\n"</span>, __FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="57"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">				<span class="hljs-keyword">goto</span> fail;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="58"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="59"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			bbf_data-&gt;old_data = tree_feat-&gt;feature_data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="60"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			bbf_data-&gt;d = <span class="hljs-built_in">descr_dist_sq</span>(feat, tree_feat); <span class="hljs-comment">//计算两个关键点描述器差平方和</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="61"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			tree_feat-&gt;feature_data = bbf_data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="62"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="63"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-comment">//取前k个</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="64"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			n += <span class="hljs-built_in">insert_into_nbr_array</span>( tree_feat, _nbrs, n, k );<span class="hljs-comment">//</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="65"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}                                                  <span class="hljs-comment">//2</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="66"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		t++;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="67"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="68"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="69"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">minpq_release</span>( &amp;min_pq );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="70"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">for</span>( i = <span class="hljs-number">0</span>; i &lt; n; i++ ) <span class="hljs-comment">//bbf_data为何搞个old_data?</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="71"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="72"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		bbf_data = (<span class="hljs-keyword">struct</span> bbf_data*)(_nbrs[i]-&gt;feature_data);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="73"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		_nbrs[i]-&gt;feature_data = bbf_data-&gt;old_data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="74"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">free</span>( bbf_data );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="75"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="76"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	*nbrs = _nbrs;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="77"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> n;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="78"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="79"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">fail:</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="80"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">minpq_release</span>( &amp;min_pq );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="81"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">for</span>( i = <span class="hljs-number">0</span>; i &lt; n; i++ )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="82"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="83"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		bbf_data = (<span class="hljs-keyword">struct</span> bbf_data*)(_nbrs[i]-&gt;feature_data);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="84"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		_nbrs[i]-&gt;feature_data = bbf_data-&gt;old_data;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="85"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-built_in">free</span>( bbf_data );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="86"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="87"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-built_in">free</span>( _nbrs );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="88"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	*nbrs = <span class="hljs-literal">NULL</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="89"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> <span class="hljs-number">-1</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="90"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp;依据上述函数kdtree_bbf_knn从上而下看下来，注意几点：</p> 
<p>&nbsp; &nbsp; 1、上述函数kdtree_bbf_knn中，explore_to_leaf的代码如下：</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-type">static</span> <span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* <span class="hljs-built_in">explore_to_leaf</span>( <span class="hljs-keyword">struct</span> kd_node* kd_node, <span class="hljs-keyword">struct</span> feature* feat,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">										<span class="hljs-keyword">struct</span> min_pq* min_pq )       <span class="hljs-comment">//kd tree          //the before </span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">struct</span> <span class="hljs-title class_">kd_node</span>* unexpl, * expl = kd_node;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">double</span> kv;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-type">int</span> ki;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">while</span>( expl  &amp;&amp;  ! expl-&gt;leaf )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	{                    <span class="hljs-comment">//0</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		ki = expl-&gt;ki;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		kv = expl-&gt;kv;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( ki &gt;= feat-&gt;d )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="15"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: comparing imcompatible descriptors, %s"</span> \</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="16"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					<span class="hljs-string">" line %d\n"</span>, __FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="17"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-keyword">return</span> <span class="hljs-literal">NULL</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="18"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="19"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( feat-&gt;descr[ki] &lt;= kv )  <span class="hljs-comment">//由目标点描述器确定搜索向kd tree的左边或右边</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="20"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{                            <span class="hljs-comment">//如果目标点模值比节点小，搜索向tree的左边进行</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="21"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			unexpl = expl-&gt;kd_right;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="22"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			expl = expl-&gt;kd_left;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="23"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="24"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">else</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="25"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="26"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			unexpl = expl-&gt;kd_left;    <span class="hljs-comment">//else </span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="27"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			expl = expl-&gt;kd_right;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="28"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="29"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="30"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-comment">//把未搜索数分支入口,差值存储在min_pq,</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="31"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( <span class="hljs-built_in">minpq_insert</span>( min_pq, unexpl, <span class="hljs-built_in">ABS</span>( kv - feat-&gt;descr[ki] ) ) )</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="32"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		{                                     </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="33"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-built_in">fprintf</span>( stderr, <span class="hljs-string">"Warning: unable to insert into PQ, %s, line %d\n"</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="34"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">					__FILE__, __LINE__ );</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="35"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">			<span class="hljs-keyword">return</span> <span class="hljs-literal">NULL</span>;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="36"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="37"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	}</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="38"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">	<span class="hljs-keyword">return</span> expl;</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="39"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">}</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 2、上述查找函数kdtree_bbf_knn中的参数k可调，代表的是要查找近邻的个数，即number of neighbors to find，在sift特征匹配中，k一般取2</p> 
<pre class="has" name="code"><code class="language-cpp hljs"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">k = <span class="hljs-built_in">kdtree_bbf_knn</span>( kd_root_0, feat, <span class="hljs-number">2</span>, &amp;nbrs, KDTREE_BBF_MAX_NN_CHKS_0 );<span class="hljs-comment">//点匹配函数(核心)</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">		<span class="hljs-keyword">if</span>( k == <span class="hljs-number">2</span> ) <span class="hljs-comment">//只有进行2次以上匹配过程,才算是正常匹配过程</span></div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp; 3、上述函数kdtree_bbf_knn中“bbf_data-&gt;d = descr_dist_sq(feat, tree_feat); //计算两个关键点描述器差平方和”，使用的计算方法是本文第一部分1.2节中所述的欧氏距离。</p> 
<h3><a name="t19"></a>3.3、SIFT+BBF算法匹配效果</h3> 
<p><span style="color:#333333;">&nbsp; &nbsp; 之前试了下<span style="color:#333333;">sift + KD + BBF算法，</span>用两幅不同的图片做了下匹配（当然，运行结果显示是不匹配的），效果还不错：</span><span style="color:#336699;"><a href="http://weibo.com/1580904460/yDmzAEwcV#1348475194313">http://weibo.com/1580904460/yDmzAEwcV#1348475194313</a>。</span></p> 
<blockquote> 
 <p><a href="http://weibo.com/1580904460/yDmzAEwcV#1348475194313"><img alt="" src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWctbXkuY3Nkbi5uZXQvdXBsb2Fkcy8yMDEyMDkvMjQvMTM0ODQ3NTM5NF85NTkwLmpwZw?x-oss-process=image/format,png"></a></p> 
</blockquote> 
<p>&nbsp; &nbsp; “<span style="color:#333333;">编译的过程中，直接用的VS2010 + opencv（并没下gsl）。2012.09.24”。</span>....</p> 
<p><span style="color:#333333;">&nbsp; &nbsp; 本文文末的完整源码，有pudn帐号的朋友可以前去这里下载：<a href="http://www.pudn.com/downloads340/sourcecode/graph/texture_mapping/detail1486667.html">http://www.pudn.com/downloads340/sourcecode/graph/texture_mapping/detail1486667.html</a>；没有pudn账号的同学请加群：252422340（之前的旧群群满了，导致很多同学没法加群，故可加此新群），至群文件下载SIFT_area_match.rar，验证信息：sift。感谢诸位。</span></p> 
<p></p> 
</div>
                </div>
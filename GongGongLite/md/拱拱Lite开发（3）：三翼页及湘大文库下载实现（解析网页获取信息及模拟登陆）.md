#拱拱Lite开发（3）：三翼页及湘大文库下载实现（解析网页获取信息及模拟登陆）
因为没有三翼新闻及湘大文库的API,简单的方法行不通就只能绕远啦，我们这次来解析网页，嗯，是个体力活其实。因为网页HTML是有一定语法规则的，所以只要网页结构不进行大的改动，我们就可以一直这样解析网页获得数据。其实如果有条件的话，也可以放在服务器上解析存储，这样不仅可以减轻对于原网站的访问压力，而且在原网页出现较大改动时，我们可以直接同步更新服务器而不必推送客户端新版本，从而保证用户体验。好了，让我们开始进入正题吧。
##（1）寻找信息源
我这次想的是要有滚动标题栏，就像三翼官网的那样，这样我们要搞到图片URL；另外还有三翼的公告和新闻；其实我对学校的讲座也蛮感兴趣的，嗯，加上来；还有湘大文库，我们要实现模拟登陆并实现文档搜寻及下载，好了，差不多就这些。先来看看网页（浏览器F12获得网页代码）：1. [三翼校园官网（手机版） ](https://www.sky31.com/html/m/)
```HTML

<html>

<head>……</head>

<body>

    <div style="display: none">……</div>

    <div id="muenDetail" style="padding: 5px; height: 100%;">……</div>

    </div>

 

    <div id="main" onmousedown="muenClose();lookupClose()">

        <div class="obj" id="objIndex">

            <div class="selectSub" id="selectSub">……</div>

            <div class="imageFlow" style="height: 370.74px;" imageflowid="0">

                <div class="imageFlowAll" id="imageFlowAll0" style="left: -1670px; width: 4175px;">

                    <div class="imageFlowC" id="imageFlowC0_0" style="width: 835px;" picid="0">

                        <div class="imageFlowPic">

                            <a class="imageFlow_pic" style="background-image: url('https://www.sky31.com/uploadfile/2017/1124/20171124011623451.jpg')"

                                href="https://www.wjx.cn/m/18288944.aspx"></a>

                        </div>

                        <div class="imageFlowText">拱拱2.0调查问卷</div>

                    </div>

                    <div class="imageFlowC" id="imageFlowC0_1" style="width: 835px;" picid="1">

                        <div class="imageFlowPic">

                            <a class="imageFlow_pic" style="background-image: url('https://www.sky31.com/uploadfile/2017/1111/20171111034437731.jpg')"

                                href="https://search.bilibili.com/all?keyword=eatest&from_source=banner_search"></a>

                        </div>

                        <div class="imageFlowText">eatest-美食测评</div>

                    </div>

                    <div class="imageFlowC" id="imageFlowC0_2" style="width: 835px;" picid="2">

                        <div class="imageFlowPic">

                            <a class="imageFlow_pic" style="background-image: url('https://www.sky31.com/uploadfile/2017/1109/20171109104711498.jpg')"

                                href="https://buy.sky31.com/game/index.html"></a>

                        </div>

                        <div class="imageFlowText">测一测双十一你和“谁”更配</div>

                    </div>

                    <div class="imageFlowC" id="imageFlowC0_3" style="width: 835px;" picid="3">

                        <div class="imageFlowPic">

                            <a class="imageFlow_pic" style="background-image: url('https://www.sky31.com/uploadfile/2017/1111/20171111114515206.jpg')"

                                href="https://www.sky31.com/2017/study_1017/7604.html"></a>

                        </div>

                        <div class="imageFlowText">通识课作业！</div>

                    </div>

                    <div class="imageFlowC" id="imageFlowC0_4" style="width: 835px;" picid="4">

                        <div class="imageFlowPic">

                            <a class="imageFlow_pic" style="background-image: url('https://www.sky31.com/uploadfile/2017/0423/20170423093914201.jpg')"

                                href="HTTPS://buy.sky31.com"></a>

                        </div>

                        <div class="imageFlowText">湘大二手街</div>

                    </div>

                </div>

                <div class="imageFlowCount">3/5</div>

            </div>

            <div class="articleList">……</div>

        <div class="obj" id="objNews">……</div>
        <div class="obj" id="objInfo">……</div>
        <div class="obj" id="objMore">……</div>

    </div>

    <div id="footer">……</div>

    <script src="//www.google-analytics.com/analytics.js" async=""></script>

    <script src="//hm.baidu.com/hm.js?ba96561e15987574ca2264145c06f8a3"></script>

    <script src="//j.sky31.com/jQuery/1.11.3/TweenMax/Holder/jQuery.LazyLoad/" type="text/javascript"></script>

    <script src="https://www.sky31.com/statics/js/sky31/touch_indexFunc.js" type="text/javascript"></script>

    <script type="text/javascript">……</script>

    <div style="display:none;">……</div>

</body>

</html>
```
三翼官网有700多行代码，而手机版只有400多行代码，因此当然选择简单的啦，经历了一番辛苦地搜寻，我终于在'class=imageFlowAll'的节点下面找到了要找的信息（其他无关信息已折叠），就是滚动图片的URL，然后暂时记住节点位置。

2.[湘大官网-学术活动](http://www.xtu.edu.cn/xysh/xywh/xsjz/)
```HTML

<html lang="zh-CN">

 

<head>……</head>

 

<body>

    <div class="Pnavbox hidden-sm hidden-md hidden-lg visible-xs ">……</div>

    <div class="navbar-toggle navbar-toggle-box hidden-sm hidden-md hidden-lg  visible-xs">……</div>

    <div class="allnav collapse hidden-sm hidden-md hidden-lg " id="allnav">……</div>

    <div class="pr con-header hidden-xs">……</div>

    <div class="navbar-toggle menubar-toggle-box">…… </div>

    <div class="menunav collapse hidden-sm hidden-md hidden-lg" id="menunav">……</div>

    <div class="mainbox">

        <div class="container">

            <div class="row">

                <div class="dropmenu fl col-lg-2 col-md-2 col-sm-2 hidden-xs">……</div>

                <div class="content container-fluid fr col-lg-10 col-md-10 col-sm-10 col-xs-12">

                    <div class="breadnav" id="breadnav-red">……</div>

                    <div class="contentbox col-lg-10 col-md-10 col-sm-10 col-xs-12">

                        <div class="list">

                            <a title="【韶风名家论坛】Molecular transfer in dynamic polymer materials" href="/xysh/xywh/xsjz/hxxy/2018-01-10/4557.html">【韶风名家论坛】Molecular transfer in dynamic polymer materials

                                <span>2018-01-10</span>

                            </a>

                            <a title="【韶风名家论坛】基于嵌入式和分布式可再生能源发电系统" href="/xysh/xywh/xsjz/gly/2018-01-02/4531.html">【韶风名家论坛】基于嵌入式和分布式可再生能源发电系统

                                <span>2018-01-02</span>

                            </a>

                            <a title="【韶风名家论坛】Control of Smart Grid Considering the Communication Networks in-the-loop" href="/xysh/xywh/xsjz/gly/2018-01-02/4530.html">【韶风名家论坛】Control of Smart Grid Considering the Communication Networks in-the-loop

                                <span>2018-01-02</span>

                            </a>

                            <a title="【韶风名家论坛】Generating Polynomials and Symmetric Tensor Decompositions" href="/xysh/xywh/xsjz/sxy/2017-12-25/4500.html">【韶风名家论坛】Generating Polynomials and Symmetric Tensor Decompositions

                                <span>2017-12-25</span>

                            </a>

                            <a title="【韶风名家论坛】地质构造区煤与瓦斯突出防治技术" href="/xysh/xywh/xsjz/hjyzy/2017-12-22/4483.html">【韶风名家论坛】地质构造区煤与瓦斯突出防治技术

                                <span>2017-12-22</span>

                            </a>

                            <a title="【韶风名家论坛】聚合物表征方法及应用" href="/xysh/xywh/xsjz/hxxy/2017-12-18/4469.html">【韶风名家论坛】聚合物表征方法及应用

                                <span>2017-12-18</span>

                            </a>

                            <a title="【韶风名家论坛】铱配合物的生物应用研究" href="/xysh/xywh/xsjz/hxxy/2017-12-18/4468.html">【韶风名家论坛】铱配合物的生物应用研究

                                <span>2017-12-18</span>

                            </a>

                            <a title="【韶风名家论坛】基于碳纳米材料的分离膜的制备及水处理特性" href="/xysh/xywh/xsjz/hjyzy/2017-12-04/4378.html">【韶风名家论坛】基于碳纳米材料的分离膜的制备及水处理特性

                                <span>2017-12-04</span>

                            </a>

                            <a title="【韶风名家论坛】AI2AGI ：从经典人工智能到强人工智能" href="/xysh/xywh/xsjz/gly/2017-11-28/4354.html">【韶风名家论坛】AI2AGI ：从经典人工智能到强人工智能

                                <span>2017-11-28</span>

                            </a>

                            <a title="【韶风名家论坛】海洋视频大数据智能分析、挖掘与数据技术" href="/xysh/xywh/xsjz/gly/2017-11-28/4353.html">【韶风名家论坛】海洋视频大数据智能分析、挖掘与数据技术

                                <span>2017-11-28</span>

                            </a>

                            <a title="【韶风名家论坛】新时代行政体制改革的基本思路" href="/xysh/xywh/xsjz/ggglxy/2017-11-15/4289.html">【韶风名家论坛】新时代行政体制改革的基本思路

                                <span>2017-11-15</span>

                            </a>

                            <a title="【韶风名家论坛】饮料中应用的乳化液" href="/xysh/xywh/xsjz/gly/2017-11-13/4262.html">【韶风名家论坛】饮料中应用的乳化液

                                <span>2017-11-13</span>

                            </a>

                            <a title="【韶风名家论坛】多铁性材料中的交叉耦合和性质调控" href="/xysh/xywh/xsjz/wlxy/2017-11-08/4244.html">【韶风名家论坛】多铁性材料中的交叉耦合和性质调控

                                <span>2017-11-08</span>

                            </a>

                            <a title="【韶风名家论坛】绿色生物制造" href="/xysh/xywh/xsjz/gly/2017-11-01/4209.html">【韶风名家论坛】绿色生物制造

                                <span>2017-11-01</span>

                            </a>

                            <a title="【韶风名家论坛】新时代的中国特色大国外交" href="/xysh/xywh/xsjz/gly/2017-10-31/4207.html">【韶风名家论坛】新时代的中国特色大国外交

                                <span>2017-10-31</span>

                            </a>

                            <a title="【韶风名家论坛】视频行为的深度识别方法" href="/xysh/xywh/xsjz/xgy/2017-10-27/4184.html">【韶风名家论坛】视频行为的深度识别方法

                                <span>2017-10-27</span>

                            </a>

                            <a title="【韶风名家论坛】小样本目标识别：结合深度网络和属性学习的方法" href="/xysh/xywh/xsjz/xgy/2017-10-27/4183.html">【韶风名家论坛】小样本目标识别：结合深度网络和属性学习的方法

                                <span>2017-10-27</span>

                            </a>

                            <a title="【韶风名家论坛】模式识别研究现状与趋势" href="/xysh/xywh/xsjz/xgy/2017-10-27/4182.html">【韶风名家论坛】模式识别研究现状与趋势

                                <span>2017-10-27</span>

                            </a>

                            <a title="【韶风名家论坛】结构冲击响应的应力波效应" href="/xysh/xywh/xsjz/tmxy/2017-10-26/4176.html">【韶风名家论坛】结构冲击响应的应力波效应

                                <span>2017-10-26</span>

                            </a>

                            <a title="【韶风名家论坛】逻辑、语言和语境" href="/xysh/xywh/xsjz/zxx/2017-10-26/4174.html">【韶风名家论坛】逻辑、语言和语境

                                <span>2017-10-26</span>

                            </a>

                            <a title="【韶风名家论坛】上转换荧光分析" href="/xysh/xywh/xsjz/gly/2017-10-26/4173.html">【韶风名家论坛】上转换荧光分析

                                <span>2017-10-26</span>

                            </a>

                            <a title="【韶风名家论坛】微芯片电泳-质谱检测单细胞分析和生物质量子点荧光探针的研究" href="/xysh/xywh/xsjz/gly/2017-10-26/4171.html">【韶风名家论坛】微芯片电泳-质谱检测单细胞分析和生物质量子点荧光探针的研究

                                <span>2017-10-26</span>

                            </a>

                            <a title="【韶风名家论坛】收入差距为什么越来越大？——财富差距、收入差距与消费差距" href="/xysh/xywh/xsjz/gsxy/2017-10-24/4160.html">【韶风名家论坛】收入差距为什么越来越大？——财富差距、收入差距与消费差距

                                <span>2017-10-24</span>

                            </a>

                            <a title="【学士讲座】中日养老产业政策比较研究——以福冈北京为例" href="/xysh/xywh/xsjz/gly/2017-10-24/4159.html">【学士讲座】中日养老产业政策比较研究——以福冈北京为例

                                <span>2017-10-24</span>

                            </a>

                            <a title="【韶风名家论坛】日本的NPO现状" href="/xysh/xywh/xsjz/gly/2017-10-24/4158.html">【韶风名家论坛】日本的NPO现状

                                <span>2017-10-24</span>

                            </a>

                        </div>

                        <div class="listpage">……</div>

                    </div>

                    <div class="sharebox col-lg-2 col-md-2 col-sm-2 col-xs-12">……</div>

                </div>

            </div>

        </div>

    </div>

    <style>……</style>

    <div class="footer1">……</div>

    <script src="/skin/default/js/jquery.min.js"></script>

    <script src="/skin/default/js/bootstrap.min.js"></script>

    <script src="/skin/default/js/public.js" type="text/javascript"></script>

    <script src="/skin/default/js/respond.js"></script>

    <script>……</script>

 

</body>

</html>
```
这个就比较直观了，class='list'节点。

3.[三翼-热点](https://www.sky31.com/news/hot/)

这个就不贴源码了，大家自己找吧。

4.[三翼-湘大文库](https://doc.sky31.com/)

湘大文库的话，我们需要的信息源是动态的，就是说不仅仅是一个网页，我们要做到用户查找一个文档，我们能对应搜到网页并解析，这样就不能向上面那样简单分析一个网页。我们需要先从网址开始。先随便查找一个文档，就会发现它的网址格式规律：

示例：https://doc.sky31.com/search/all/离散数学/2

如果是搜doc文档，那就是：https://doc.sky31.com/search/doc/离散数学/2

其实就是：https://doc.sky31.com/search/"文档类型"/"查找文档名"/"页数"

```HTML

<!DOCTYPE html>

<html>

	<head>……</head>

	<body>

		<div class="navbar navbar-inverse navbar-fixed-top">……</div><!-- navbar -->

		<div class="container m-container">

<script>

var DOC_IS_LOGIN = true;

</script>

<div class="row" id="bannerSearch">……</div>

<div class="row">

	<div class="col-lg-9">

		<div class="row">

			<div class="col-lg-12 m-s-nums">

				找到相关文档约 24 篇

			</div>

		</div>

		<div class="row">

			<div class="col-lg-12">

				<div class="list-group">

				  					<div class="list-group-item">

    					<h4 class="list-group-item-heading">

    						<img src="/static/image/doc.png" height="22">

    						 <a href="/view/53701e36ab7fb2c83e8b4567">同济大学第六版高等<em>数学</em>上册课后答案全集</a>

    					</h4>

    					<p class="list-group-item-text">同济大学第六版高等<em>数学</em>上册课后答案全集</p>

    					<p class="list-group-info">2014-05-12 | 共176页 | 4次下载 | 0积分 | <a href="/lists/math">数学</a> | 贡献者：Nolouch</p>

  					</div>

  				  					<div class="list-group-item">

    					<h4 class="list-group-item-heading">

    						<img src="/static/image/doc.png" height="22">

    						 <a href="/view/54117f42ab7fb2ca78e5a97e">工程<em>数学</em>(线性代数与概率统计_周勇_完整版</a>

    					</h4>

    					<p class="list-group-item-text">湘大  周勇版 线性代数与工程<em>数学</em>资料（理科）</p>

    					<p class="list-group-info">2014-09-11 | 共30页 | 7次下载 | 0积分 | <a href="/lists/other">其他</a> | 贡献者：Neo</p>

  					</div>

  				  					<div class="list-group-item">

    					<h4 class="list-group-item-heading">

    						<img src="/static/image/doc.png" height="22">

    						 <a href="/view/55a33b38cb1bedf9488b4568">大一下学期高等<em>数学</em>期末试题及答案__数套</a>

    					</h4>

    					<p class="list-group-item-text">为湘大文库贡献一份力量</p>

    					<p class="list-group-info">2015-07-13 | 共22页 | 324次下载 | 0积分 | <a href="/lists/math">数学</a> | 贡献者：黑牛</p>

  					</div>

  				  					<div class="list-group-item">

    					<h4 class="list-group-item-heading">

    						<img src="/static/image/doc.png" height="22">

    						 <a href="/view/548653e3ab7fb2bc0ef7fccd">工程<em>数学</em>_线性代数_周勇_朱砾_答案(3章)</a>

    					</h4>

    					<p class="list-group-item-text">线代第三章答案</p>

    					<p class="list-group-info">2014-12-09 | 共6页 | 2次下载 | 1积分 | <a href="/lists/math">数学</a> | 贡献者：kylin</p>

  					</div>

  								</div>

				

			</div>

			<div class="col-lg-12">

				<ul class="pagination pagination-sm"><li><a href="/search/doc/%E7%A6%BB%E6%95%A3%E6%95%B0%E5%AD%A6/1">上一页</a></li><li><a href="/search/doc/%E7%A6%BB%E6%95%A3%E6%95%B0%E5%AD%A6/">1</a></li><li class="active"><a href="#">2</a></li></ul>			</div>

		</div>

 

	</div>

</div>

			<br>

			<footer>……</footer>

		</div>

 

<div class="modal fade" id="linkInModal" tabindex="-1" role="dialog" aria-hidden="true">……</div>

		<script src="/static/js/jquery-1.10.2.min.js"></script>

		<script src="/static/js/bootstrap.min.js"></script>

		<script src="/static/js/jquery.uploadify.min.js"></script>

		<script src="/static/js/doc.js?v=6"></script>

		<div style="display:none"><script src="https://s95.cnzz.com/z_stat.php?id=1261392149&web_id=1261392149" language="JavaScript"></script></div>

	</body>

</html>
```
这样，知道了网址格式我们就能主动去获取所需信息，在上面的网页我们能很快在class="list-group"节点下找到搜寻结果。

##（2）C#发送GET请求获取HTML代码并解析
刚才是在浏览器获得网页HTML代码，现在我们需要用C#去实现同样的功能，这次我用的是WebRequest和WebResponse类发送请求。 
```C#

 internal async Task<ObservableCollection<Sky31DataModel>> GetDocSearchResult(string searchname, int pagenum)//湘大文库文档查询

        {

            ObservableCollection<Sky31DataModel> dataList = new ObservableCollection<Sky31DataModel>();

          

      		WebRequest request = HttpWebRequest.Create(URL_Doc + "/search/all/"+searchname+ "/"+ pagenum);

                WebResponse response = await request.GetResponseAsync();//发送请求并获得响应

                Stream stream = response.GetResponseStream();//将数据传送到流

                var result = "";//result放置获得的HTML代码

                using (StreamReader sr = new StreamReader(stream))

                {

                    result = sr.ReadToEnd();

                }

                HtmlDocument doc = new HtmlDocument();

                doc.LoadHtml(result);//存储方便利用HtmlNode类的SelectSingleNode方法查找节点

                HtmlAgilityPack.HtmlNodeCollection htmlnodes = doc.DocumentNode.SelectNodes("//div[@class='list-group-item']");

 

                for (int i = 0; i < htmlnodes.Count; i++)

                {

                    Sky31DataModel data = new Sky31DataModel();

                    HtmlNode imgnode = htmlnodes[i].SelectSingleNode(htmlnodes[i].XPath + "/h4/img");

                    HtmlNode titlenode = htmlnodes[i].SelectSingleNode(htmlnodes[i].XPath + "/h4/a");

                    HtmlNode contentnode = htmlnodes[i].SelectSingleNode(htmlnodes[i].XPath + "/p[@class='list-group-item-text']");

                    HtmlNode sublinenode = htmlnodes[i].SelectSingleNode(htmlnodes[i].XPath + "/p[@class='list-group-info']");

                       data.ImageUrl = StringToImg(imgnode.Attributes["src"].Value.ToString());//改为指向本地资源地址

                    data.ContentUrl = URL_Doc + titlenode.Attributes["href"].Value.ToString();//指向地址 格式:URL_Doc/view/(文件编号)

                    if(!string.Equals(StringToType(imgnode.Attributes["src"].Value.ToString()),""))

                        data.Type = StringToType(imgnode.Attributes["src"].Value.ToString());

                    if (titlenode != null)

                        data.Title = titlenode.InnerText;

                    if (contentnode != null)

                        data.Content = contentnode.InnerText;

                    if (sublinenode != null)

                        data.SublineInf = sublinenode.InnerText;

                      data.IsRingActive = false;

                    data.IsDownloaded = Windows.UI.Xaml.Visibility.Collapsed;

                    

                    dataList.Add(data);

                }

                return dataList;

            }
```
于是HTML，如同上次的JSON，我们依然有特定的方法处理它，不过我没有用正则表达式，这次是HtmlNode类的SelectSingleNode，用XPath可以快速简单地定位（XPath相关知识请看这位大神的总结），这样，利用我们刚才找到的信息的节点，我们能快速定位并用HtmlNode类的InnerText或InnerHtml读取里面的信息。这样就终于得到了我们期盼的数据。
##（3）C#发送POST请求获取cookie及其他登陆信息
刚才我们可以获取到网页的信息，但是有些信息比如昵称、积分、下载URL、上传URL等是需要我们登陆才能获取到的，那么用C#能够得到登录后的信息吗？答案是可以，不过这个就不是GET请求了，而是POST请求。我们需要像浏览器一样先向服务器发送带有用户登陆信息及验证码（如果有）的数据，服务器处理通过后才会再向我们传回登陆后的信息及Cookie,这个Cookie可以看作是一个凭据，服务器可以根据Cookie来辨识我们，这样我们就不必每次都提交账号密码，只要传入Cookie就行了，这里我推荐一个工具可以监视浏览器发送、收到的数据，可以方便我们模拟POST操作：Fiddler。然后我们继续： 
```C#

internal async Task<CookieContainer> GetPageCookie()//获取cookie用于网页验证

        {

            ApplicationDataContainer localSettings = ApplicationData.Current.LocalSettings;//本地存储的信息

            string url = "https://doc.sky31.com/login/post";//向三翼湘大文库发送登陆POST请求

            string indata = string.Format("num={0}&pass={1}", localSettings.Values["ID_JIAO"].ToString(), localSettings.Values["Password_JIAO"].ToString());//发送的数据：教务系统账号密码

            CookieContainer myCookieContainer = new CookieContainer();//用于装传回的Cookie信息

            byte[] requestBuffer = Encoding.GetEncoding("UTF-8").GetBytes(indata);//将传入的信息转为二进制数据

            HttpWebRequest myHttpWebRequest = (HttpWebRequest)WebRequest.Create(url);

            myHttpWebRequest.ContentType = "application/x-www-form-urlencoded;charset=UTF-8";//浏览器的一些默认表单信息

            myHttpWebRequest.Accept = "zh-CN";//浏览器的一些默认表单信息

            myHttpWebRequest.Method = "POST";//请求方式

            myHttpWebRequest.CookieContainer = myCookieContainer;

 

            using (Stream myRequestStream = await myHttpWebRequest.GetRequestStreamAsync())//采用流的方式发送数据

            {

                myRequestStream.Write(requestBuffer, 0, requestBuffer.Length);

            }

 

            HttpWebResponse myHttpWebResponse = (HttpWebResponse)await myHttpWebRequest.GetResponseAsync();发送请求并获得传回的信息

 

            return myCookieContainer;//返回服务器传回的Cookie

        }
```
这样子就得到了Cookie啦，有了Cookie我们就能愉快方便地模拟各种登陆后才能进行的操作啦~
比如，下载一份文档、获取积分信息：
```C#

private async void DownloadFile(Sky31DataModel data)//下载需要的文档，data存储着所需文档的信息如名称、Id，这个可以在分析搜寻结果的网页解析获得

        {

            DataManager manager = new DataManager();

            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(data.ContentUrl.Replace("view", "down_file"));//替换查看文档的URL为下载的URL

            request.CookieContainer = await manager.GetPageCookie();//获得Cookie

            request.ContentType = "application/x-www-form-urlencoded;charset=UTF-8";//默认表单信息

            request.Accept = "zh-CN";//默认表单信息

            request.Method = "GET";//GET方式请求

            try//用于监视是否有异常

            {

                WebResponse response = await request.GetResponseAsync();//到这一步发送GET请求

                Stream stream = response.GetResponseStream();//用于存储文档数据的流

                var file = await ApplicationData.Current.LocalCacheFolder.CreateFileAsync(string.Format(data.Title + "." + data.Type), CreationCollisionOption.ReplaceExisting);//用于存储到本地文件，设置文件名及后缀，比如:离散数学课后习题解答.doc

 

                using (var stream2 = await file.OpenStreamForWriteAsync())

                {

                    await stream.CopyToAsync(stream2);//将获取的HttpClient的Stream流复制到文件的stream流   

                    await stream2.FlushAsync();//异步刷新

 

                }

            }

            catch (Exception e)//收集异常

            {

                DebugManager debug = new DebugManager();

                debug.BugToText(e, "下载文档");//记录异常

                NotifyControl notify = new NotifyControl("下载出现问题(╬▔皿▔)凸");

                notify.Show();//显示异常

            }

 

        }

        
 private async void GetDocCoin()//获得湘大文库积分

        {

            DataManager dataManager = new DataManager();

            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create("https://doc.sky31.com/home/");//获取积分的目标网址

            try

            {

                request.CookieContainer = await dataManager.GetPageCookie();

                request.ContentType = "application/x-www-form-urlencoded;charset=UTF-8";

                request.Accept = "zh-CN";

                request.Method = "GET";

 

                WebResponse response = await request.GetResponseAsync();

                Stream stream = response.GetResponseStream();

                var result = "";

                using (StreamReader sr = new StreamReader(stream))

                {

                    result = sr.ReadToEnd();

                }

                HtmlDocument doc = new HtmlDocument();

                doc.LoadHtml(result);

                HtmlAgilityPack.HtmlNode htmlnode = doc.DocumentNode.SelectSingleNode("//div[@class='media-body']");//所需要的积分、昵称、文档数信息所在节点

                string resultstr = htmlnode.InnerText.Replace("\t", "");//整理得到信息的格式

                resultstr = resultstr.Replace("\n", "");//整理得到信息的格式

                resultstr = resultstr.Replace("\r", "");//整理得到信息的格式

                resultstr = resultstr.Replace("积分", "    积分");//整理得到信息的格式

                resultstr = resultstr.Replace("文档数", "  文档数");//整理得到信息的格式

                DocCoinTextBlock.Text = resultstr;//得到经整理格式的积分、昵称、文档数信息并传到控件显示

            }

            catch (Exception e)

            {

                DebugManager debugManager = new DebugManager();

                debugManager.BugToText(e, "获得湘大文库积分");

            }

        }

```
除了这些，利用C#发送数据模拟操作还可以做很多事情，大家可以根据需要来获得信息。当然，对于带验证码的登陆，也同样可以用C#发送数据进行登陆，这里就不说明了，大家可以自己百度了解。
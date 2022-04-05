<!DOCTYPE html>
<html>
    <head>
	<meta charset="utf-8">
	<meta http-equiv="Content-Language" content="zh-cn">
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
	<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
	<meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
	<title></title>
	<style>
		body {
		    padding: 0;
		    margin: 10px;
		    font-size: 12px;
		    /*line-height: 1.125rem;*/
		    font-family: 'Helvetica Neue',Helvetica,'Lucida Grande','Luxi Sans',Arial,'PingFang SC','Hiragino Sans GB',STHeiti,'Microsoft YaHei','Wenquanyi Micro Hei','WenQuanYi Micro Hei Mono','WenQuanYi Zen Hei','WenQuanYi Zen Hei Mono',LiGothicMed;
		}

		a:link,
		a:active,
		a:visited
		{
			text-decoration: none;
			color: black;
		}
		a:hover   {
			background-color: black;
			color: white;
		}

		ul,
		li{
			padding-left: 15px;
    		margin: 0px;
    		list-style: none;
		}

		.classify {
			padding: 3px;
			display: grid;
		    /*grid-template-columns: repeat(4, 1fr);*/
		    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
		    border-width: 1px;
		    border-style: dashed;
		    border-color: #525252;
		    margin-bottom: 20px;
			/*padding: 3px;
		    position: relative;
		    display: flex;
		    justify-content: flex-start;
		    flex-flow: row wrap;
		    align-items: stretch;
		    flex-wrap: wrap;

		    border-width: 1px;
		    border-style: dashed;
		    border-color: #525252;
		    margin-bottom: 20px;*/
		}

		.classify > ul {
			padding-left: 0px;
		}

		/*.panel {
			min-width: 300px;
		}*/

		.books {
			display: grid;
		    grid-template-columns: repeat(3, 1fr);
		    margin-bottom: 20px;
		}
		.books ul,
		.books li {
			padding: 0;
		}

		.footer {
			text-align: center;
		}

		.title {
			margin: 0px 0px 10px 0px;display: grid;grid-auto-flow: column;width: 100%;
		}
		.title .title_text {
			font-size: 25px;
		}
		.title .search_content {
			justify-self: end;display: inline-flex;align-items: flex-end;
		}
		.search_content .input {
			font-size: 12px;
			height: 21px;border: 1px solid black;padding: 0 2px;margin-right: 2px;width: 300px;
		}

		.search_content .button {
			cursor: pointer;
			height: 23px;border: 1px solid black;background-color: black;color: white;font-size: 12px;justify-self: juu;
		}
		.search_content .button:hover {
			background-color: white;
			color: black;
		}
		sup {
			padding: 0 4px;
		}
	</style>
	</head>
	<body>
<!-- 		{% capture namespace %}
		{% if site.github.pages_hostname == 'github.io'%}/{{site.github.repository_name}}{% endif %}
		{% endcapture %}
		<input style="display: none;" value="{{namespace}}"> -->
		<div class="title">
			<div class="title_text">个人图书馆</div>
			<div class="search_content">
				<form onsubmit="return do_search()" submit="" method="post">
					<input id="search_text" class="input" onkeyup="do_search()" name="input" type="text" placeholder="ISBN / 书名 / 作者 / 出版社 / 分类 / 分类代码 / 译者">
					<input type="submit" class="button" value="查找" />
				</form>
			</div>
		</div>

		<div id="classify_content" class="classify"></div>
		<div id="books" class="books"></div>
		<div class="footer">
			<div>© 2022 xiashuangxi@hotmail.com</div>
			<div>本站所有数据均收集于网络</div>
			<div>本站的分类采用的是<a href="http://clc.nlc.cn/ztfdsb.jsp">中国图书馆分类法第五版</a></div>
		</div>
	</body>

</html>
<script>
	// 实现 树 ：https://code.tutsplus.com/articles/data-structures-with-javascript-tree--cms-23393
	
	var classify = '{{ site.data.classification | jsonify}}';
	var html = ""
	var init_classify = function(obj,i_t) {	
		var obj = obj.reverse();
		var t = "ul"
		for (var i = obj.length - 1; i >= 0; i--) {
			
			var i_s = obj[i].item? obj[i].item.length:0;
			if (i_t == "child") {
				t = i_s == 0 ? "li" : "ul";
			}
			html = html + "<"+t+" class='panel'><a title='"+obj[i].code+" "+obj[i].name +"' href='#"+obj[i].code+"'>"+obj[i].code + ' '+obj[i].name+"</a>";
			if (i_s > 0) {
				init_classify(obj[i].item,"child")
			} 
			html = html + "</"+t+">";
		}
	}

	var do_search = function() {
		var in_t = document.getElementById("search_text").value;
		if (in_t.length > 0){
			document.location.href="#search/"+document.getElementById("search_text").value;
		} else {
			document.getElementById("search_text").value = "";
			document.location.href="#";
		}
		return false
	}

	init_classify(JSON.parse(classify),"root");
	document.getElementById("classify_content").innerHTML = html;
	document.getElementById("books").innerHTML = html;

</script>
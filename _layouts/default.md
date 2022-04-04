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
		    margin: 10px 20px;
		    font-size: 12px;
		    line-height: 1.125rem;
		    font-family: 'Helvetica Neue',Helvetica,'Lucida Grande','Luxi Sans',Arial,'PingFang SC','Hiragino Sans GB',STHeiti,'Microsoft YaHei','Wenquanyi Micro Hei','WenQuanYi Micro Hei Mono','WenQuanYi Zen Hei','WenQuanYi Zen Hei Mono',LiGothicMed;
		    background-color: #f6f7f8;
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
			display: grid;
		    grid-template-columns: repeat(4, 1fr);
		    /*gap: 7px;*/
		    border-width: 1px;
		    border-style: dashed;
		    border-color: #525252;
		    margin-bottom: 20px;
		}

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
	</style>
	</head>
	<body>
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
	init_classify(JSON.parse(classify),"root");
	document.getElementById("classify_content").innerHTML = html;
	document.getElementById("books").innerHTML = html;

</script>
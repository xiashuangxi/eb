{% capture namespace %}
{% if site.github.pages_hostname == 'github.io'%}/{{site.github.repository_name}}{% endif %}
{% endcapture %}
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
		  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
		  /*border-width: 1px;
		  border-style: dashed;
		  border-color: #525252;
		  */margin-bottom: 20px;
		}

		.classify > ul {
			padding-left: 0px;
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
			margin-top: 20px;
		}

		.title {
			margin: 0px 0px 10px 0px;display: grid;grid-auto-flow: column;width: 100%;
			margin-bottom: 20px;
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

		.download_button{
			border: 1px solid black;
	    padding: 0px 4px;
	    font-size: 12px;
	    cursor: pointer;
		}
	</style>
	</head>
	<body>
		<div class="title" aria-region="true" aria-regiontype="2" tabindex="0">
			<div class="title_text"><a href="{{namespace}}/index.html">个人图书馆</a></div>
			<!-- <div class="search_content">
				<form onsubmit="return do_search()" submit="" method="post">
					<input id="search_text" class="input" onkeyup="do_search()" name="input" type="text" placeholder="ISBN / 书名 / 作者 / 出版社 / 分类 / 分类代码 / 译者">
					<input type="submit" class="button" value="查找" />
				</form>
			</div> -->
		</div>

		<!-- <div id="classify_content" class="classify"></div>
		<div id="books" class="books">
		</div> -->

		<div id="container"></div>

		<div class="footer">
			<div>© 2022 xiashuangxi@hotmail.com</div>
			<div>本站所有数据均收集于网络</div>
			<div>本站的分类采用的是<a href="http://clc.nlc.cn/ztfdsb.jsp">中国图书馆分类法第五版</a></div>
		</div>
</html>

<!-- tree -->
<script type="text/javascript">
	class TreeNode {
		constructor(key, value, parent = null) {
			this.key = key;
			this.value = value;
			this.parent = parent;
			this.children = [];
		}

		get is_leaf() {
			return this.children.length === 0;
		}

		get hasChildren() {
			return !this.is_leaf();
		}
	}

	class Tree {
		constructor (key, value = value) {
			this.root = new TreeNode(key,value);
		}

		*preOrderTraversal(node = this.root) {
			yield node;
			if (node.children.length){
				for (let child of node.children) {
					yield* this.preOrderTraversal(child);
				}
			}
		}

		*postOrderTraversal(node = this.root) {
			if (node.children.length) {
				for (let child of node.children) {
					yield* this.postOrderTraversal(child);
				}
			}
			yield node;
		}

		insert(parentNodeKey, key, value) {
			for (let node of this.preOrderTraversal()){
				if (node.key === parentNodeKey){
					node.children.push(new TreeNode(key, value,node));
					return true;
				}
			}
			return false;
		}

		remove(key) {
			for (let node of this.preOrderTraversal()) {
				const filtered = node.children.filter(c => c.key !== key);
				if (filtered.length !== node.children.length) {
					node.children = filtered;
					return true;
				}
			}
			return false;
		}

		find(key){
			for (let node of this.preOrderTraversal()) {
				if(node.key === key) return node;
			}
			return undefined;
		}
	}
</script>
<!-- tree end -->

<script>
	// data
	var cf = [];
	var cf_key = "classification";
	var books = [];

	// show_books
	var show_books = function() {
		var ul = document.createElement('ul');
		//padding: 0px; display: grid;justify-content: start;align-items: start;justify-items: start;grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
		// ul.style.padding='0';
		// ul.style.display='grid';
		// ul.style.grid
		ul.setAttribute("style", "padding: 0px; display: grid;justify-content: start;align-items: start;justify-items: start;grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));");

		for (var i = books.length - 1; i >= 0; i--) {
			var li = document.createElement('li');
			li.style.padding='0';
			ul.appendChild(li);

			var book = books[i][0];
			var book_title = document.createElement('div');
			book_title.innerHTML ="<a href='/eb/info.html/#isbn/"+book.isbn+"' title='"+book.name+"'>"+ book.name + "</a>";
			li.appendChild(book_title);
		}
		
		document.getElementById('container').appendChild(ul);
		// document.getElementById('books').appendChild(ul);
	}

	var get_book_obj = function(isbn) {
		for (var i = books.length - 1; i >= 0; i--) {
			var book = books[i][0];
			if ( isbn && isbn == book.isbn ) {
				var isbn_el = document.createElement('div');
				isbn_el.style.display = 'flex';
				isbn_el.style.lineHeight = '1rem';
				isbn_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">书名:</div><div>'+book.name+'</div>'

				var name_el = document.createElement('div');
				name_el.style.display = 'flex';
				name_el.style.lineHeight = '1rem';
				name_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">ISBN:</div><div>'+book.isbn+'</div>'

				var author_el = document.createElement('div');
				author_el.style.display = 'flex';
				author_el.style.lineHeight = '1rem';
				author_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">作者/译者:</div><div>'+(book.author ? book.author : "" ).replaceAll('\\','')+'，&nbsp;[译]'+book.translator+'</div>'

				var ph_el = document.createElement('div');
				ph_el.style.display = 'flex';
				ph_el.style.lineHeight = '1rem';
				ph_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">出版社:</div><div>'+book.publishinghouse+'</div>'

				var standard_el = document.createElement('div');
				standard_el.style.display = 'flex';
				standard_el.style.lineHeight = '1rem';
				standard_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">字数:</div><div>'+(book.standard ? book.standard : "")+'</div>'

				var edition_el = document.createElement('div');
				edition_el.style.display = 'flex';
				edition_el.style.lineHeight = '1rem';
				edition_el.innerHTML = '<div style="width:60px; font-weight:bold; text-align: right;margin-right:5px">版次:</div><div>'+(book.edition ? book.edition : "")+'</div>'

				var btn_el = document.createElement('div');
				btn_el.innerHTML = '<a class="download_button" href="/eb/">返回</a><a target="_block" class="download_button" href="'+book.url+'">下载</a>';

				var container = document.getElementById('container');
				container.appendChild(name_el);
				container.appendChild(isbn_el);
				container.appendChild(author_el);
				container.appendChild(ph_el);
				container.appendChild(edition_el);
				container.appendChild(standard_el);

				container.appendChild(btn_el);
			}
		}
	}

	var get_action_info = function() {
		var action_info = window.location.href.match(/\#(\S*)/);
		if (action_info){
			var t1 = action_info[0].match(/\/(\S*)/);//[1];
			if(t1) {
				return action_info[0].match(/\/(\S*)/)[1];
			}
		} 
		return null;
	}

	var get_action = function() {
		var action_info = window.location.href.match(/\#(\S*)/);
		if (action_info){
			//return action_info[0].match(/(\S*)\//)[1].toUpperCase();
			var t1 = action_info[0].match(/(\S*)\//);//[1];
			if (t1) { return t1[1].toUpperCase() } else { return action_info[0].toUpperCase() }
		} 
		return null;
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

	window.onload = function() {
		var data_obj = JSON.parse('{{site.data | jsonify}}');
		cf = data_obj[cf_key];
		var book_keys = Object.keys(data_obj);
		for (var i = book_keys.length - 1; i >= 0; i--) {
			var b_key = book_keys[i];
			if (b_key !== cf_key) {
				books.push(data_obj[b_key]);
			}
		}

		var current_action = get_action();
		if (current_action === "#ISBN") {
			get_book_obj(get_action_info());
		}

		if (!current_action || current_action === "#SEARCG") {
			show_books();
		}

	}
</script>
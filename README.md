#基于 Emmet 的 HTML 模板引擎

将

>  	div#user.module.module-user[data-id=%id%]>h2.module_title{%username%}+span.module_status{%status%}

转化为

	<div id="user" class="module module-user" data-id="%id%">
		<h2 class="module_title">%username%</h2>
		<span class="module_status">%status%</span>
	</div>

当然实际结果并没有缩进格式，这里只是作为示例

该模板唯一的目的是为了减少 HTML 的代码量，不过目前仅实现 Emmet 的 #id、.class、[attr=value] 的基础选择器功能
目前来说作为模板引擎已经足够了

该模板引擎可以前后端通用

在前端中并不依赖 jQuery，但全局中若有 jQuery，模板引擎会挂到 jQuery 的命名空间下

##前端的使用

###AMD 规范下
	require(['yourPath/emmetTpl.js'], function(tpl){
		var tplFunc = tpl({
			template: 'div#user.module.module-user[data-id=%id%]>h2.module_title{%username%}+span.module_status{%status%}'
			, filter: {
				status: function(data, i){
					return data.status;
				}
			}
		});

		// 获取数据 data，调用 模板引擎
		var html = tplFunc( data ).join('');
	});

当然也可以基于 jQuery 来使用

	require(['yourPath/jquery'， 'yourPath/emmetTpl'], function($){
        var tplFunc = $.template({
            template: 'div#user.module.module-user[data-id=%id%]>h2.module_title{%username%}+span.module_status{%status%}'
            , filter: {
                status: function(data, i){
                    return data.status;
                }
            }
        });

        // 获取数据 data，调用 模板引擎
        var html = tplFunc( data ).join('');
    });

###未使用 AMD 规范

	<script src="yourPath/jquery.js"></script>
	<script src="yourPath/emmetTpl.js"></script>
	<script>
	$(function(){
		var tplFunc = $.template({
			template: 'div#user.module.module-user[data-id=%id%]>h2.module_title{%username%}+span.module_status{%status%}'
            , filter: {
                status: function(data, i){
                    return data.status;
                }
            }
		});

		// 获取数据 data，调用 模板引擎
        var html = tplFunc( data ).join('');
	});
	</script>

##服务器端 Node.js 下使用

如 express 下:

	var tpl = require('yourPath/emmetTpl.js').template
		, userTpl = tpl({
			template: 'div#user.module.module-user[data-id=%id%]>h2.module_title{%username%}+span.module_status{%status%}'
            , filter: {
                status: function(data, i){
                    return data.status;
                }
            }
		})
		;

	app.get('/user', function(req, res){
        // 获取数据 data，调用 模板引擎
        res.send( userTpl( data ).join('') );
	});

也可以使用 express 设置为静态文件供前端调用

	app.use('/yourPath/jquery.emmetTpl.js', express.static(__dirname + 'yourPath/emmetTpl.js'))
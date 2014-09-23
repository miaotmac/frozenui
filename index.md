# css组件库

- order: 2
- category: alice

---

Frozen提供的UI组件是目前QQ会员前端开发组所用的通用样式库。遵循[手Q样式规范](http://isux.oa.com/guide/mqq/vd#545)，基本样式使用离线包的方式减少请求，并提供快速接入的方案。

<script type="text/template" id="alice-module">
    <div class="alice-module">
        <div class="alice-module-head">
            <h2 class="alice-module-title">
                <a href="#"></a>
            </h2>
            <span class="alice-module-version"></span>
            <p class="alice-module-description"></p>
        </div>
    </div>
</script>

<script type="text/template" id="alice-module-demo">
    <div class="alice-module-demo">
        <h3 class="alice-module-subtitle"></h3>
        <div class="alice-module-dom"></div>
        <pre class="alice-module-code"></pre>
    </div>
</script>

<style>
.content{padding: 15px;}
.ui-avatar-tiled{display:inline-block;}
.alice-module {
    border-bottom: 1px solid #eee;    
    padding: 10;
    margin-top: 20px;
    margin-bottom: 50px;
}
.alice-module-head {
    overflow: hidden;
}
.alice-module-title {
    margin: 0;
    font-size: 28px;
    font-family: Trebuchet MS;
    display: inline;
}
.alice-module-title a {
    /*color: #7CAE23;*/
    color: #00a5e0;
    cursor: pointer;
}
.alice-module-link {
    font-size: 14px;
}
.alice-module-version {
    font-size: 12px;
    font-weight: normal;
    margin-left: 0.5em;
    color: #888;
    font-family: Menlo,Monaco,"Courier New",monospace;
}
p.alice-module-description {
    font-size: 14px;
    color: #888;
    margin: 10px 0 20px;
}
.alice-module-demo {
    position: relative;
}
.alice-module-demo:hover {
    background: #fdfdfd;
}
.alice-module-dom {
    margin-bottom: 8px;
    font-size:14px;
}
.alice-module-code {
    margin: 0!important;
}
h3.alice-module-subtitle {
    margin: 0;
    color: #333;
    display: block;
    padding: 20px 0;
}
.alice-module-sourcecode {
    position: absolute;
    right: 5px;
    top: 0;
    font-size: 12px;
    padding: 5px 10px;
    background: #EFFFE4;
    border-radius: 3px;
    display: none;
    z-index: 99;
    opacity: 0.8;
}

.alice-loading {
    margin-bottom: 20px;
}
.black {
    font-size: 12px;
    padding: 2px;
    border-radius: 2px;
    background: rgba(0, 0, 0, 0.55);
    color: #fff;
}
</style>

<div class="alice-modules"></div>

<script type="text/javascript">
seajs.use(['$', 'gallery/underscore/1.6.0/underscore', 'arale/popup/1.1.6/popup'], function($, _, Popup) {


    $.getJSON('../package.json', function(data) {
        var alias;
            alias = data.widgets;
        var deps = _.pairs(alias);
        _.each(deps, function(dep) {
            var moduleNode = $($('#alice-module').html());
            moduleNode.find('.alice-module-title a')
                .attr('href', 'docs/' + dep[1])
                .attr('id', 'modules-' + dep[1])
                .html(dep[1]);
            moduleNode.appendTo('.alice-modules');
            var list = substractTitle(moduleNode.find('h2'));

            $.ajax({
                url: 'docs/' + dep[1],
                dataType: 'html',
                success: function(data) {
                    data = $(data);
                    moduleNode.find('.alice-module-description')
                        .html(data.find('.entry-content > p:first-child').html());

                    data.find('.nico-insert-code').each(function(index, item) {
                        var demoNode = $($('#alice-module-demo').html());
                        item = $(item);
                        var subtitle = item.prev().html();
                        if (item.prev()[0].tagName !== 'H3' || !subtitle) {
                            subtitle = '默认';
                        }
                        
                        demoNode.find('.alice-module-subtitle').html(subtitle);
                        demoNode.find('.alice-module-dom').html(item.html());
                        
                        // 直接使用目标页面生成的高亮代码，不再动态渲染
                        var codeHtml = item.next('.highlight').find('pre').html();
                        demoNode.find('.alice-module-code').html(codeHtml);

                        moduleNode.find('.alice-loading').remove();
                        demoNode.appendTo(moduleNode);
                    });

                    // 中文关键词，一般放在 keywords 数组的第一个
                    // 在这里写到左边索引栏中
                    moduleNode.find('.alice-module-version')
                    var keywords = data.find('#sidebar-wrapper .keywords').html();
                    if (keywords) {
                        list.find('i').html(keywords);
                    }
                }
            });
        });
        seajs.use('/static/side', function(side) {
            side.init();        
        });        
    });
    
    function substractTitle(item) {
        $('.side-loading').remove();
        item = item.find('a');
        var list = $($('#list-template').html());
        list.find('a').html(item.html() + list.find('a').html());
        list.find('a').attr('href', '#' + item.attr('id'));
        list.appendTo('.side-area ul');
        return list;
    }

});
</script>

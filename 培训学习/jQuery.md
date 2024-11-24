###### http://192.168.229.113:8888/dqc/assessment/detailGfxBmfhl?date=2023-03-19&pc=day&jgdm=469251923&pageSize=50&0=13&ra	0获取所选元素的内容

1. $('div').text()：	获取所选元素的内容，包括子标签内容
   		有参数时设置所选元素的文本

2. $('div').html()：	获取所选元素的内容，包括子标签（标签加标签内容），只会返回第一个标签的HTML
   		有参数时设置所选元素的文本（可以添加子标签）



###### 获取元素

1. $('input')【得到一个集合】
2. $('input :  first')【得到第一个节点】
3. $('input : last')【得到最后一个节点】
4. $('input').get(0)【获取集合里面的第一个节点】
5. $('input').eq(0)【获取集合里面的第一个元素】
6. $(this).parent()【获取父标签】
7. .$(this).children()【获取所有子标签】
8. $('.work tr:last').clone()【复制一个节点】
9. $("#input").prev(); // 上一个兄弟节点
10. $("#input").prevAll(); // 之前所有兄弟节点
11. $("#input").next(); // 下一个兄弟节点
12. $("#input").nextAll(); // 之后所有兄弟节点
13. $("#input").siblings(); // 所有兄弟节点 



###### 获取元素属性值

1. $('input').eq(0).attr('value')：	
   	只会读取标签中设置的属性值，没有设置则为undefined（不会随着输入改变）

   $('input').eq(0).attr('value','888')：
   	设置value属性的值为888

   

2. $('input').eq(0).val()：	
   	仅适用于表单，获取当前表单中填入的内容（会随着输入改变）

   $('input').eq(0).val('888')：
   	设置表单填入内容为888

   

3. $('[name="hobby"]').eq(0).prop('checked')：	
   	获取单选/复选框是否被选中，选中为true,反之false



 4. $('select option:selected').val()

    获取下拉列表选中的值

###### CSS属性

1. 查询CSS属性：查看div标签的颜色属性
   	 $('div').eq(0).css('color');
   
2. 设置CSS属性：设置div标签的颜色属性为red
   	 $('div').eq(0).css('color', 'red');
3. 设置一堆属性： 同时设置div标签多个属性
   	$('div').eq(0).css({
      		fontSize: '2em', 
      		color: 'red' 
      	})



###### 元素类名

1. 添加类名：$('div').eq(0).addClass('div');
2. 移除类名：$('div').eq(0).removeClass('div');
3. 如果存在则删除，不存在则添加：$('div').eq(0).toggleClass('div')



###### 创建元素

1. 创建元素：$('<span>span标签</span>')
2. 在元素的结尾插入内容（**内部**）：$('div').eq(0).append($('<span>span标签</span>'))
3. 在元素的开头插入内容（**内部**）：$('div').eq(0).prepend($('<span>span标签</span>'))
4. 在元素之后插入内容（**外部**）：$('div').eq(0).after($('<span>span标签</span>'))
5. 在被选元素之前插入内容（**外部**）：$('div').eq(0).before($('<span>span标签</span>'))
6. 删除选中元素（**自己以及所有元素，内容**）：$('div').eq(0).remove()
7. 删除元素（**除自己标签外所有子元素，内容**）：$('div').eq(0).empty()


















































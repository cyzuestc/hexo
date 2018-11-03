---
title: ajax学习
date: 2018-04-25 09:42:31
tags:
---
# 提交ajax请求
参考使用ajax通过无刷新验证账号是否存在, 服务端使用jsp进行验证.当用户输入abc的时候提示已经存在
完整的$.ajax参数 比较复杂, 这里使用常见的调用方式
```
$.ajax(
url: page,
data: {"name":value},
success: function(result){
	$("#checkResult").html(result);
}
);
```
1. url: page表示访问的是page页面
2. data: 表示提交的参数
3. success: function表示服务器成功返回后对应的响应参数

```
<script>
$(function(){
   $("#name").keyup(function(){
     var page = "/study/checkName.jsp";
     var value = $(this).val();
        $.ajax({
            url: page,
            data:{"name":value},
            success: function(result){
              $("#checkResult").html(result);
            }
        });
   });
});	
<script>
```
以上看起来比较复杂, 其逻辑结构为:
```
$( function(){ $("#name").keyup(function(){});  }  );

```
# 使用get方式提交ajax
$.get 是$.ajax的简化版, 专门用于发送get请求
```
$.get(
	page,
	{"name":value},
	function(result){
		$("#checkResult").html(result);
	}
);
```
完整代码
```
<script>
 $(function(){
  $("#name").keyup(function(){
   var page="/study/checkName.jsp";
   var value= $(this).val;
	$.get(
		page,
		{"name":value},
		function(result){
			$("#checkResult").html(result);
		}
	);   
  });
 });
</script>
```
# 使用post方式提交ajax
```
$.post(
	page,
	{"name":value},
	function(result){
		$("#checkResult").html(result);
	}
);
```
# get和post的区别
1. get是form默认提交方式, 通过超链访问某个地址,是get.不可以提交二进制数据.如上传文件.
2. post必须通过method="post"显示指定, 提交数据不会再浏览器显示, 可以提交二进制数据.

# 最简单的调用ajax的方式
load比起$.get $.post就简单太多了.
$("#id").load(page,[data])
<script>
	$(function(){ 
		$("#name").keyup(
			function(){
				var value = $(this).val();
				var page="/study/checkName.jsp?name="+value;
				$("#checkResult").load(page);
			});
	}  );

</script>

# 格式化form下的输入数据
form下输入内容比较多的时候, 一个一个取比较麻烦, 就可以用serialize()把输入数据格式化成字符串;

```
<script src="http://how2j.cn/study/jquery.min.js"></script>
    
<div id="checkResult"></div>
<div id="data"></div>
<a href="http://how2j.cn/study/checkName.jsp">http://how2j.cn/study/checkName.jsp</a>
 
<form id="form">   
输入账号 :<input id="name" type="text" name="name"> <br>
输入年龄 :<input id="age" type="text" name="age"> <br>
输入手机号码 :<input id="mobile" type="text" name="mobile"> <br>
     
</form>
 
<script>
$(function(){
   $("input").keyup(function(){
      var data = $("#form").serialize();
      var url = "http://how2j.cn/study/checkName.jsp";
      var link = url+"?"+ data;
      $("a").html(link);
      $("a").attr("href",link);
   });
});
    
</script>
```
# 获取一个对象
1. 创建一个Hero对象
2. 创建一个JSONObject 对象
3. 把Hero对象转换为JSONObject 对象，并放在上一个JSONObject对象上，key是"hero"
4. 设置编码方式为UTF-8
5. 通过response返回 
```
        Hero hero = new Hero();
        hero.setName("盖伦");
        hero.setHp(353);
         
        JSONObject json= new JSONObject();
   
        json.put("hero", JSONObject.fromObject(hero));
        response.setContentType("text/html;charset=utf-8"); 
        response.getWriter().print(json);
```
1. 点击按钮之后，通过ajax访问getOneServlet
2. 获取都数据后，通过JSON.parse 转换为json对象
3. 获取name和hp
4. 显示在div里 
```
    <script> 
    $('#sender').click(function(){ 
        var url="getOneServlet"; 
        $.post(
                url,
                function(data) {
                     var json=JSON.parse(data); 
                     var name =json.hero.name; 
                     var hp = json.hero.hp;
                     $("#messageDiv").html("英雄名称："+name + "<br>英雄血量:" +hp );
                      
         });  
    }); 
    </script>  
```
# 获取多个对象
1. 准备一个集合
2. 向集合中放入 10个Hero对象
3. 通过JSONSerializer.toJSON(heros)把集合转换为JSON字符串
4. 返回给浏览器 
```
List<Hero> heros = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            Hero hero = new Hero();
            hero.setName("name"+i);
            hero.setHp(500+i);
            heros.add(hero);
        }
         
        String result =JSONSerializer.toJSON(heros).toString();
 
        response.setContentType("text/html;charset=utf-8"); 
        response.getWriter().print(result);
    }  
```
1. 通过ajax访问getManyServlet
2. 把返回的数据，通过 $.parseJSON 转换为json数组
3. 遍历数组，显示在div中 
```
    <script> 
    $('#sender').click(function(){ 
        var url="getManyServlet"; 
        $.post(
                url,
                function(data) {
                    var heros = $.parseJSON(data);
                     for(i in heros){
                         var old = $("#messageDiv").html();
                         var hero = heros[i];
                         $("#messageDiv").html(old + "<br>"+hero.name+"   -----   "+hero.hp); 
                     }
         });  
    }); 
    </script>  
```




















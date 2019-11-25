# slider

> [unslider](http://www.jq22.com/jquery-info1940)

````
<!-- 首先引入jQuery和unslider -->
<script src="jquery-1.11.1.min.js"></script>
<script src="unslider.min.js"></script>
````

````
<!-- 写点样式，让轮播好看点 -->
ul, ol { padding: 0;}
.banner { position: relative; overflow: auto; text-align: center;}
.banner li { list-style: none; }
.banner ul li { float: left; }
#b04 { width: 640px;}
#b04 .dots { position: absolute; left: 0; right: 0; bottom: 20px;}
#b04 .dots li 
{ 
    display: inline-block; 
    width: 10px; 
    height: 10px; 
    margin: 0 4px; 
    text-indent: -999em; 
    border: 2px solid #fff; 
    border-radius: 6px; 
    cursor: pointer; 
    opacity: .4; 
    -webkit-transition: background .5s, opacity .5s; 
    -moz-transition: background .5s, opacity .5s; 
    transition: background .5s, opacity .5s;
}
#b04 .dots li.active 
{
    background: #fff;
    opacity: 1;
}
#b04 .arrow { position: absolute; top: 200px;}
#b04 #al { left: 15px;}
#b04 #ar { right: 15px;}
````

````
<!-- 把要轮播的地方写上来 -->
<div class="banner" id="b04">
    <ul>
        <li><img src="01.jpg" alt="" width="640" height="480" ></li>
        <li><img src="02.jpg" alt="" width="640" height="480" ></li>
        <li><img src="03.jpg" alt="" width="640" height="480" ></li>
        <li><img src="04.jpg" alt="" width="640" height="480" ></li>
        <li><img src="05.jpg" alt="" width="640" height="480" ></li>
    </ul>
    <a href="javascript:void(0);" class="unslider-arrow04 prev"><img class="arrow" id="al" src="arrowl.png" alt="prev" width="20" height="35"></a>
    <a href="javascript:void(0);" class="unslider-arrow04 next"><img class="arrow" id="ar" src="arrowr.png" alt="next" width="20" height="37"></a>
</div>
````

````
//最后用js控制
$(document).ready(function(e) {
    var unslider04 = $('#b04').unslider({
        dots: true
    }),
    data04 = unslider04.data('unslider');
     
    $('.unslider-arrow04').click(function() {
        var fn = this.className.split(' ')[1];
        data04[fn]();
    });
});
````
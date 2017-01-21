title : Smartstudy 网站js分析
date: 2015-1-15

---
## javascript

为了应聘贵公司职位 , 更好的展现自己 . 我把 [http://www.smartstudy.com/](http://www.smartstudy.com/)网站中涉及到的js前端代码认真的阅读了一遍 . 给每个js加上了我理解的注释.(有些相似的脚本以及插件未加).

###  1 index.js 大图轮播

大图轮播

> 给每个js加上了我理解的注释

该轮播插件实现的效果

- 每6秒图片滚动一次
- 点击图片编号,切换到指定图片,并且期间定时器不停止工作
- 图片获得鼠标焦点时,停止定时器.失去时 重新启用定时器.
- 被点击的图片的编号 大于/小于 当前图片的编号时 图片从向 左/右 滑动 ,
- 当浏览器窗口大小变化时,每次滑动都要判断 以便让图片居中

```
$(function() {
  $("#focusIndex1").show();
  默认展示第一张图片
  $("#focusBar li").css("width", $(window).width());
  $("#focusBar li").mouseover(function() {
    stopFocusAm(); // 当鼠标放到大图上 停止定时器
  }).mouseout(function() {
    starFocustAm(); // 鼠标移开 启用定时器
  });
  starFocustAm(); // 默认启用定时器
});
var timerFID;

function nextPage() {
  changeFocus(true);
}

function prePage() {
  changeFocus(false);
}
var currentFocusI = 1;
var changeingFocus = false;
```

```
function changeFocus(dir, nextI) { // 初始值为 dir = true ; nextI = 0

  if (currentFocusI == nextI) { // 当前图片编号 === 被点击图片编号
    return; // 不做任何操作
  }
  if (currentFocusI > nextI) { // 当前图片编号 > 被点击图片编号
    dir = false; // 改变图片滚动默认方向
  } else {
    dir = true; // 否则不改变方向
  }
  if (nextI == 0) { // 当点击回到起点
    dir = true; // 回复默认方向
    var nextI = dir ? currentFocusI + 1 : currentFocusI - 1; // ?
    nextI = nextI > $("#focusBar li").length ? 1 : (nextI < 1 ? $("#focusBar li").length : nextI);
  }
  $('.ban_item_box a').each(function() {
    $(this).removeClass("a_active"); //先让所有编号变灰
  });
  $('#nav_' + nextI).addClass("a_active"); // 然后把要展示的编号点亮
  if ($("#focusBar li").length <= 1) return; // 如果只有一张图 或者没有图片 那么 返回
  if (changeingFocus) return; // 如果突然客户端点击了别的编号 那么 返回
  changeingFocus = true;
  $("#focusIndex" + nextI).stop(false, true); // 停止 当前动画
  $("#focusIndex" + nextI + " .focusL").stop(false, true); // 停止 当前动画
  $("#focusIndex" + nextI + " .focusR").stop(false, true); // 停止 当前动画
  /*var nextI = dir?currentFocusI+1:currentFocusI-1;
  nextI = nextI>$("#focusBar li").length?1:(nextI<1?$("#focusBar li").length:nextI);*/
  var focusWidth = $(window).width()>1000?1000:$(window).width();

  // 设置当前图片所在li宽度为浏览器窗口宽度 以便是图片可以居中
  $("#focusIndex" + currentFocusI).css("width", $(window).width());
  $("#focusIndex" + nextI).css("width", $(window).width()); //
  if (dir) { // 如果是默认方向没变

    // 让下一张图 右边的屏幕外准备好
    $("#focusIndex" + nextI).css("left", $(window).width());
    $("#focusIndex" + nextI + " .focusL").css("left", $(window).width() / 2);
    $("#focusIndex" + nextI + " .focusR").css("left", $(window).width() / 2);
    $("#focusIndex" + currentFocusI).show(); // 显示当前图
    $("#focusIndex" + nextI).show(); // 显示下一张要显示的图
    $("#focusIndex" + currentFocusI + " .focusL").animate({ // 开始滑动...
      left: -($(window).width() / 2 + 1000)
    }, 600, 'easeInExpo');
    $("#focusIndex" + currentFocusI + " .focusR").animate({
      left: -($(window).width() / 2 + 1000)
    }, 600, 'easeInExpo', function() {
      $("#focusIndex" + nextI + " .focusL").animate({
        left: -500
      }, 600, 'easeInOutCirc');
      $("#focusIndex" + nextI + " .focusR").animate({
        left: -500
      }, 600, 'easeInOutCirc');
      $("#focusIndex" + currentFocusI).animate({
        left: -$(window).width()
      }, 600, 'easeOutExpo');
      $("#focusIndex" + nextI).animate({
        left: 0
      }, 600, 'easeOutExpo', function() {
        $("#focusIndex" + currentFocusI).hide();
        currentFocusI = nextI;
        changeingFocus = false;
      });
    });
  } else { // 方向与默认相反
    $("#focusIndex" + nextI).css("left", -$(window).width()); // 下一张要现实的图片 在左边藏好了
    $("#focusIndex" + nextI + " .focusL").css("left", -($(window).width() / 2 + 1000));
    $("#focusIndex" + nextI + " .focusR").css("left", -($(window).width() / 2 + 1000));
    $("#focusIndex" + currentFocusI).show();
    $("#focusIndex" + nextI).show();
    $("#focusIndex" + currentFocusI + " .focusR").animate({
      left: $(window).width() / 2
    }, 600, 'easeInExpo');
    $("#focusIndex" + currentFocusI + " .focusL").animate({
      left: $(window).width() / 2
    }, 600, 'easeInExpo', function() {
      $("#focusIndex" + nextI + " .focusL").animate({
        left: -500
      }, 600, 'easeInOutCirc');
      $("#focusIndex" + nextI + " .focusR").animate({
        left: -500
      }, 600, 'easeInOutCirc');
      $("#focusIndex" + currentFocusI).animate({
        left: $(window).width()
      }, 600, 'easeOutExpo');
      $("#focusIndex" + nextI).animate({
        left: 0
      }, 600, 'easeOutExpo', function() {
        $("#focusIndex" + currentFocusI).hide();
        currentFocusI = nextI;
        changeingFocus = false;
      });
    });
  }
}
```
```
function starFocustAm() {
  timerFID = setInterval("timer_tickF()", 6000); //每6秒执行 timer_tickF()函数
}

function stopFocusAm() {
  clearInterval(timerFID);
}

function timer_tickF() {
  changeFocus(true, 0); //执行changeFocus
}
```

### 2 set_Tab()

控制footer下的选项卡

> 我给每个js加上了我理解的注释
```
  function set_Tab(name, cursel, n) { // 每个菜单被点击都要触发这个函数,

    for (i = 1; i <= 3; i++) { // 对三个选项卡进行遍历

      var menu = document.getElementById(name + i); // 菜单对象
      var con = document.getElementById("con_" + name + "_" + i); // 选项卡对象
      menu.className = i == cursel ? "hover1" : ""; // 如果 i== cursel 那么className = hover1 否则 为 ""
      con.style.display = i == cursel ? "block" : "none"; // 同上

    }
  }
```
#### 问题

 1: 如果增添新的菜单,那么html 与 javascript代码都要改动.这样不利于升级 .有优化空间...

### 3 dropMenu()

该函数控制最上方帮助中心的下拉菜单的出现与隐藏渐变


> 我给每个js加上了我理解的注释
```
function dropMenu(obj) {
  $(obj).each(function() {
    var theSpan = $(this); // 菜单
    var theMenu = theSpan.find(".submenu"); // 子选项卡
    var tarHeight = theMenu.height(); // 子选项卡高度
    theMenu.css({
      height: 0, // 把高度和透明度都设置为0 , 用于初始化.
      opacity: 0
    });
    theSpan.hover( // 添加 鼠标hover事件
      function() {
        $(this).addClass("selected"); // 设置类名 表示被选中

        // 注意 这里必须要加stop()函数来清空theMenu上的事件,否则会出现控制延迟的情况
        theMenu.stop().show().animate({
          height: tarHeight,
          opacity: 0.9
        }, 400); // 利用jquery animate 函数时间高度和通明度 400ms 的渐变效果
      },
      function() {  // 与上相反
        $(this).removeClass("selected");
        theMenu.stop().animate({
          height: 0,
          opacity: 0
        }, 400, function() {
          $(this).css({
            display: "none"
          });
        });
      }
    );
  });
}


```
#### 问题

- 纯代码实现的透明度难免带来一些兼容性问题
- animate 函数实现渐变要比纯生javascript更吃浏览器性能 (还好用的地方比较少)

### 4 Zoom()

控制图片放大比例 , 并且保证了不会让图片周围留下空白

> 我给每个js加上了我理解的注释
```
function Zoom(obj, width, height) {
  var img = new Image();
  img.src = obj.src;

  // 取放大比例为需求与实际的比值更大的一部分,这样虽然会有一部分会被遮挡,但是不会出现空白
  var scale = Math.max(width / img.width, height / img.height);
  var newWidth = img.width * scale; // 按比例缩放后的尺寸
  var newHeight = img.height * scale; //
  var div = obj.parentNode; // 在夫级创建dom元素div
  obj.width = newWidth; // 在保证了原图片比例的情况下 为图片重新赋值
  obj.height = newHeight;
  div.style.width = width + "px";
  div.style.height = height + "px";
  div.style.overflow = "hidden"; // 设置div的overflow隐藏
  obj.style.marginLeft = (width - newWidth) / 2 + "px"; // 让图片居中
  obj.style.marginTop = (height - newHeight) / 2 + "px";
}
```



## plugins

- 1. lxb.js // 百度的离线包 不解释
- 2. 百度认证
- 3. pikaday.js // 插件 用于快速选择日期 不解释
- 4. manhuatoTop.1.0.js // 浏览器返回顶部特效

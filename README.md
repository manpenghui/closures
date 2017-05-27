# closures
scope  &amp;&amp; closures function

一个函数只要内部函数未销毁（内部函数存在被调用的可能），这个函数就不会销毁，将一直存在于内存中，只有所有内部函数都销毁了，并所有的业务代码都已执行完，这个函数才会被释放。我们看看最常见的经典闭包问题：
```
    function show() {
        var li = document.getElementsByTagName('li');
        var length = li.length;
        for (var i = 0; i < length; i++) {
            li[i].onclick = function() {
                alert(i);
            }
        }
    }

    show();
```
    此时，我们点击所有的li,alert的结果都会是li的长度，而不是当前元素的下标。为什么会是这样呢？
    由于show的内部函数（click事件处理程序时刻有调用可能），所以show的作用域链不能被销毁（只能页面卸载是销毁），i的值一直保持for循环执行完后的length值，此时的click的函数只是进行了声明而未运行，当click触发的时候，函数才开始执行，这个时候i的值已经是length了。所以每次触发onclick的时候才会alert length。我们进行改一下：
```
    function show(){
	var li = document.getElementsByTagName('li');
	var length = li.length;
	for(var i=0;i<length;i++){
		(function(n){
			li[n].onclick = function(){
				alert(n);
			}
		})(i)
	}
}

show();
```
为什么这样就行了呢，这时候onclick引用的变量变成了n，而由于立即执行函数的原因，每个onclick函数在作用域链中分别保持着对应的n（0~length-1），这时候就可以了。

闭包会使子函数保持其作用域链的所有变量及函数与内存中，内存消耗很大，在使用的时候尽量销毁父函数不再使用的变量。你经常访问一些范围之外的标识符，每次访问都将导致一些性能损失。

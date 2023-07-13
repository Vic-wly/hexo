title: '手机号js验证 & 后端正则验证 '
author: Laiyong Wang
date: 2023-02-22 09:09:09
tags:
---

```
//标签失焦时，验证输入的手机号是否为 1 开头的十一位数字，错误则新增标签提示
$("#phone")[0].onblur=function(){
    var reg=/^[1][0-9]{10}$/;
    if (!reg.test(this.value)) {
        if($("#phone-check").length==0){
            var node = this.parentNode.childNodes;
            var elem = document.createElement("p");
            elem.id = "phone-check";
            elem.class = "help-block";
            elem.setAttribute("style", "color:#d9534f;margin-top:2px;margin-bottom:0px;font-size: 12px;");
            elem.innerHTML = "手机号必须为 1 开头的十一位数字";
            this.parentNode.appendChild(elem);
        }
    } else {
        $("#phone-check").remove();
    }
}
//将非数字替换成空并限制最多输入十一位
$("#phone")[0].oninput=function(){
    this.value = this.value.slice(0,11);
    this.value = this.value.replace(/[^\d]/g,"");
}


```
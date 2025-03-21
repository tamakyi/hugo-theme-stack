---
title: "handsome美化记录"
categories: [ "记录" ]
tags: [ "handsome","美化","教程" ]
draft: false
slug: "handsome-modify"
date: "2022-07-10 20:20:00"
---

`[scode type="yellow"]此处备份handsome主题一些自定义的教程：[/scode]`

* 彩色标签云：在`开发者设置-自定义 JavaScript`填写：

```
let tags = document.querySelectorAll("#tag_cloud-2 a");
let colorArr = ["#428BCA", "#AEDCAE", "#ECA9A7", "#DA99FF", "#FFB380", "#D9B999"];
tags.forEach(tag => {
    tagsColor = colorArr[Math.floor(Math.random() * colorArr.length)];
    tag.style.backgroundColor = tagsColor;
});
```

如果使用PJAX需要同步填写到PJAX回调函数。

* 头像相关美化：在`开发者设置-自定义CSS`：

```
/*头像呼吸光环和鼠标悬停旋转放大*/
.img-full {
    width: 100px;
    border-radius: 50%;
    animation: light 4s ease-in-out infinite;
    transition: 0.5s;
}
.img-full:hover {
    transform: scale(1.15) rotate(720deg);
}

@keyframes light {
    0% {
        box-shadow: 0 0 4px #f00;
    }

    25% {
        box-shadow: 0 0 16px #0f0;
    }

    50% {
        box-shadow: 0 0 4px #00f;
    }

    75% {
        box-shadow: 0 0 16px #0f0;
    }

    100% {
        box-shadow: 0 0 4px #f00;
    }
}
/*评论头像旋转*/
.img-circle {
    transition: all 0.3s;
}
.img-circle:hover {
    transform: rotate(360deg);
}
```

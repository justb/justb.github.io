---
layout: post
title:  "CSS居中"
date:   2017-10-26 23:45:13 -0400
background: '/img/posts/01.jpg'
---

不用float的多个div的水平居中
```
    <div class="parent">
        <div class="child">
            1
        </div>
        <div class="child">
            2
        </div>
        <div class="child">
            3
        </div>
    </div>
```
```
.parent {
            text-align: center;
            font-size: 0;//去除display: inline-flex;产生的间隙
        }

.child {
            font-size: 1rem;//去除display: inline-flex;产生的间隙
            width: 10rem;
            height: 10rem;
            background-color: pink;
            display: inline-flex;
            -webkit-text-size-adjust:none;
        }
```


![image.png](http://upload-images.jianshu.io/upload_images/6459152-6d2df8d527a85fc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
        .parent {
            display: flex;
            align-items:center;
        }
        
        .child {
            margin: 0 auto;
            width: 10rem;
            height: 10rem;
            background-color: pink;
            
        }
```

![image.png](http://upload-images.jianshu.io/upload_images/6459152-9ef21d471244ab86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

div的垂直居中
1.display:flex;
```
<div class="parent">
        <div class="myself">
            <div class="child">
                1
            </div>
            <div class="child">
                2
            </div>
            <div class="child">
                3
            </div>
        </div>
    </div>
```
```
.parent {
            height: 50rem;
            background-color: yellow;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .child {
            /*margin: 0 auto;*/
            width: 10rem;
            height: 10rem;
            background-color: pink;
        }
```

![image.png](http://upload-images.jianshu.io/upload_images/6459152-fd7650b24a2d658e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. position:absolute;+transform
```
.parent {
            height: 50rem;
            background-color: yellow;
            position: relative;
        }
        
        .child {
            width: 10rem;
            height: 10rem;
            background-color: pink;
        }
        .myself{
            position: absolute;
            top: 50%;
            transform: translateY(-50%) translateX(-50%);
            left: 50%;
        }
```
![image.png](http://upload-images.jianshu.io/upload_images/6459152-fd7650b24a2d658e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

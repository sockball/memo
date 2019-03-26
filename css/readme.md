* 伪元素修改默认file样式实例 [参考](http://www.zhangxinxu.com/study/201306/pseudo-elements-to-style-type-file.html)
```
::-ms-browse, [type='file'] {
    padding: .4em;
    line-height: 24px;
    border: 1px solid #a0b3d6;
    background: #f0f3f9;
    color: #34538b;
}
::-webkit-file-upload-button {
    padding: .4em;
    line-height: 30px;
    border: 1px solid #a0b3d6;
    background: #f0f3f9;
    color: #34538b;
}
```

* `border`渐变，分别对应4个方向还有如`border-top-image`
```
.div {
    border: 1px solid;
    border-image: linear-gradient(90deg, #000, #fff) 5 10
}
```
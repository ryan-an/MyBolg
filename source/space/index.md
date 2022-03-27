---
title: space
date: 2021-09-15 16:26:34
comment: true
---
<style>
.list{
    width:100%;    
    display:flex;
    flex-wrap:wrap;
    height:auto;
    padding-bottom:50px;
}
/* .list-box{
    box-shadow: 0 2px 5px 0 rgba(61,111,155,.1);
    width:210px;
    height:60px;
    background:coral ;
    background:#e0ffff ;
    margin-left:10px;
    margin-top:10px;
    border-radius:6px;
    color:#fff;
    color:#777777;
    cursor:pointer;
    border:0 solid #00bfff;
    transition: all 0.4s ease-in-out;
    padding: 10px 10px;
    font-size:15px;
} */
.list-box{
    width:210px;
    height:180px;
    /* background:#eeeeee ; */
    margin-left:10px;
    border-radius:6px;
    transition: all 0.4s ease-in-out;
    box-shadow: 0 5px 8px 0 rgba(61,111,155,.1);
}
.list-box img{
    border-radius:6px;
}
.list-box .title{
    margin-top:0.1rem;
    padding-left:0.2rem;
}
.list-box:hover{
    box-shadow: 0 2px 5px 0 rgba(61,111,155,.1);
}
.list-box:hover .title{
    color:#fe6a00;
}
.list-box .subtitle{
    margin-top:0.2rem;
    color:#666;
    font-size:12px;
    color:#999999;
    white-space:nowrap;
    overflow:hidden;
    text-overflow:ellipsis;
    padding-left:0.2rem;
}
.list-box:hover{
    -webkit-transform:translateY(-6px);
    transform: translateY(-6px);
    transition: all 0.4s ease-in-out;
}
.list-box:nth-child(1){
    /* margin-left:0px; */
}
/* .list-box div{
    font-size:13px;
    width:170px;
    color:#999999;
    white-space:nowrap;
    overflow:hidden;
    text-overflow:ellipsis;
} */
.line{
    width:100%;
    margin-top:1rem;
    margin-bottom:1rem;
    border:0;
    border-top: 1px solid rgba(0,0,0,.1); 
}
</style>
<div>
    <h5>闲来逛逛</h5>
    <div class="line"></div>
    <div class="list">
        <a class="list-box" href="http://549.tv/">
            <img src="https://img0.baidu.com/it/u=2503213554,2188006727&fm=253&fmt=auto&app=120&f=JPEG?w=610&h=300" width="100%" height="70%">
            <div class="title">影视森林</div>
            <div class="subtitle">全网各大影视站点，包括App、在线、海外....</div>
        </a>
        <a class="list-box" href="https://ss.netnr.com/wallpaper">
            <img src="https://browser9.qhimg.com/bdr/__85/t016ad88ddaf2ae2d92.jpg" width="100%" height="70%">
            <div class="title">壁纸</div>
            <div class="subtitle">超清壁纸loveing....</div>
        </a>
        <a class="list-box">
            <img src="/static/img/favicon.png" width="100%" height="70%">
            <div class="title">嚯嚯嚯嚯</div>
            <div class="subtitle">嚯嚯嚯嚯....</div>
        </a>
        <a class="list-box">
            <img src="/static/img/favicon.png" width="100%" height="70%">
            <div class="title">哈哈哈哈</div>
            <div class="subtitle">哈哈哈哈....</div>
        </a>
    <div>
</div>
<div style="margin-top:2rem">
    <h5>学会聆听</h5>
    <div class="line"></div>
    <div class="list"> 
        <a class="list-box" href="#1111">
            <img src="https://browser9.qhimg.com/bdm/1000_618_80/t0124fe3eebb423762c.jpg" width="100%" height="70%">
            <div class="title">快乐歌单</div>
            <div class="subtitle">全世界都在发光ing...</div>
        </a>
        <a class="list-box" href="#1111">
            <img src="https://browser9.qhimg.com/bdr/__85/t0196345b5171788e63.jpg" width="100%" height="70%">
            <div class="title">网抑云歌单</div>
            <div class="subtitle">宁静的感受，空洞的稳重...</div>
        </a>
    </div>
</div>


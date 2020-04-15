---
title: "给康工的网页"
subtitle: " 给康康做的视频网页 "
layout: post
author: "Huanqing"
header-style: text
hidden: true
tags:
  - 笔记
---



下面是[DPlayer](https://github.com/MoePlayer/DPlayer)（一个bilibili风格的播放器）做的在线播放，主要我是为了添加字幕试试水。

**菜逼前端，电子攻城狮欢庆祝康工拿大奖**


<link href="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.css" rel="stylesheet">
<div id="dplayer"></div>
<script src="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.js"></script>
<script src="https://cdn.bootcss.com/blueimp-md5/2.12.0/js/md5.min.js"></script>
<script>
var url="https://eduinhk-my.sharepoint.com/personal/huanqing_eduinhk_onmicrosoft_com/Documents/urls/VIDEO/Nokia_Experts_talk_end-to-end_5G_technology_and_the_way_forward.mp4";    //这里填写视频地址
var suburl="https://eduinhk-my.sharepoint.com/personal/huanqing_eduinhk_onmicrosoft_com/Documents/urls/VIDEO/Nokia_Experts_talk_end-to-end_5G_technology_and_the_way_forward.vtt";    //这里填写视频地址
var id=md5(url);
const dp = new DPlayer({
    container: document.getElementById('dplayer'),
    video: {
        quality: [
            {
                name: '高清',
                url: url,
                //type: 'normal',
            },
        ],
        defaultQuality: 0,
  }

//  danmaku: {
//        id: id,
//        api: 'https://api.prprpr.me/dplayer/'    //这里填写弹幕地址
//    }
});
</script>

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
var url="https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9lZHVpbmhrLW15LnNoYXJlcG9pbnQuY29tLzp2Oi9nL3BlcnNvbmFsL2h1YW5xaW5nX2VkdWluaGtfb25taWNyb3NvZnRfY29tL0VVS0JZLUl4VGl0RGxnMzhlOFVoaWtvQkJyN0lDNi00YnVwRUxRTXI1d1dSdlE/ZT1EZGowSUM=.mp4";    //这里填写视频地址
var suburl="https://eduinhk-my.sharepoint.com/personal/huanqing_eduinhk_onmicrosoft_com/Documents/urls/VIDEO/Nokia.vtt";    //这里填写视频地址

var id=md5(url);
const dp = new DPlayer({
    container: document.getElementById('dplayer'),
    autoplay: false,
    theme: '#FADFA3',
    loop: true,
    lang: 'zh-cn',
    screenshot: true,
    hotkey: true,
    preload: 'auto',
    logo: 'logo.png',
    volume: 0.7,
    mutex: true,
    video: {
        url: url,
        pic: 'dplayer.png',
        thumbnails: 'thumbnails.jpg',
        type: 'auto',
    },
//    subtitle: {
//        url: suburl,
//        type: 'webvtt',
//        fontSize: '25px',
//        bottom: '10%',
//        color: '#b7daff',
//    },
    contextmenu: [
        {
            text: 'custom1',
            link: 'https://github.com/DIYgod/DPlayer',
        },
        {
            text: 'custom2',
            click: (player) => {
                console.log(player);
            },
        },
    ],
    highlight: [
        {
            time: 20,
            text: '这是第 20 秒',
        },
        {
            time: 120,
            text: '这是 2 分钟',
        },
    ],
});
</script>

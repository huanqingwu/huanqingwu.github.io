---
title: "「视频」欢庆的视频"
subtitle: "欢庆的视频"
layout: post
author: "Huanqing"
header-style: text
hidden: true
tags:
  - 视频
---


内容
------------------

### ~啥都没有~

<link href="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.css" rel="stylesheet">
<div id="dplayer"></div>
<script src="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.js"></script>
<script src="https://cdn.bootcss.com/blueimp-md5/2.12.0/js/md5.min.js"></script>
<script>
var url="https://pmjs-my.sharepoint.com/personal/huanqingsmovie_my365_tw/Documents/WestWorldS03/Westworld.S03E01.Parce.Domine.1080p.AMZN.WEB-DL.DDP5.1.H.264-NTb/Westworld.S03E01.Parce.Domine.1080p.AMZN.WEB-DL.DDP5.1.H.264-NTb.mp4";    //这里填写视频地址
var suburl="\_posts\video_sub\WestworldS03E01ParceDomine.vtt";
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
    subtitle: {
        url: suburl,
        type: 'webvtt',
        fontSize: '25px',
        bottom: '10%',
        color: '#b7daff',
    },
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

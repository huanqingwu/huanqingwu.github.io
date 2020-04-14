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


Nokia Experts talk end-to-end 5G technology and the way forward

<link href="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.css" rel="stylesheet">
<div id="dplayer"></div>
<script src="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.js"></script>
<script>
const dp = new DPlayer({
    container: document.getElementById('dplayer'),
    autoplay: false,
    video: {
        url: 'https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9lZHVpbmhrLW15LnNoYXJlcG9pbnQuY29tLzp2Oi9nL3BlcnNvbmFsL2h1YW5xaW5nX2VkdWluaGtfb25taWNyb3NvZnRfY29tL0ViVW1lLXdYYVgxSXFuQjI4QTg1WjIwQjRWa245U1RsVnJqd3RrOVpET0FVeWc/ZT04b2F3aWE=.mp4',
    },
    subtitle: {
        url: 'https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9lZHVpbmhrLW15LnNoYXJlcG9pbnQuY29tLzp1Oi9nL3BlcnNvbmFsL2h1YW5xaW5nX2VkdWluaGtfb25taWNyb3NvZnRfY29tL0VVN3NUbE5La05kTWlvSF8ybWVuRzV3QjM4cXZHQzhVbUIyc1lBOEFSNE9YV2c/ZT1pQkVLczM=.VTT',
        type: 'webvtt',
        fontSize: '25px',
        bottom: '10%',
        color: '#b7daff',
    },

});
</script>

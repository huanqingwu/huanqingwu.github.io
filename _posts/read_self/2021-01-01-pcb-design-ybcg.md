---
title: "PCB设计一板即成功专栏 持续更新中"
subtitle: "PCB设计一板即成功专栏"
layout: post
date:   2021-01-01
author: "Huanqing"
header-style: text
hidden: true
catalog: true
tags:
  - 笔记
---

<div class="tutor-course-content-content">
			<p>电子产业在摩尔定律的驱动下，产品的功能越来越强，集成度越来越高，信号的速率越来越快，电子产品不断微小化、精密化、高速化，PCB设计不仅仅要完成各元器件的线路连接，更要考虑高速、高密带来的各种挑战，更为重要的是，PCB设计成功与否直接影响着产品的上市时间。在这个瞬息万变的时代，能够领先竞争对手一步推出产品，市场反响或将出现天壤之别。因此，为保证产品上市时间这一首要任务，很多公司评估项目的时候会有一个PCB设计一板成功率的指标。</p>
<p>如何提高PCB设计中的一板成功率，就要通盘考虑SI、PI、EMI、结构、散热等因素，并需要了解当前主流PCB工厂、SMT工厂的加工能力、工艺来完成PCB设计，也就是DFX设计。</p>
<p>为了应对以上的挑战，老wu的博客计划开展一项“PCB设计一板成功”的付费阅读专栏，这不是一份教程，不是《21天搞定Cadence PCB设计》也不是《15天精通Altium Design》而是一份长期有效的阅读专栏，你可以理解为类似知识星球或者头条专栏的形式，采用付费VIP专属阅读的形式，一次加入长期有效，内容会围绕SI、PI、EMI、热设计以及DFX设计等主题展开，目的是建立正确的PCB设计意识，提高PCB设计一板成功率，缩短产品研发周期和研发成本，实现工程师的自我增值。</p>
<p><strong>内容目录</strong></p>
<p>严格意义上来说，《PCB设计一板即成功阅读专栏》没有目录，因为这是一项长期的写作计划，内容是不断扩充的，比如我们现在讨论DDR4或者PCIE-4.0、接着DDR5、PCIE 5.0、5G毫米波等等技术的应用，相应的PCB板材、工艺、PCB布线规则、高速总线的接口规范博客里也会跟进扩充。</p>
<p><strong>2021年整年的写作计划有：</strong></p>
<ul>
<li>PCB相关的发展历史</li>
<li>PCB设计流程</li>
<li>PCB板厂以及生产制造流程视频</li>
<li>原理图设计规范</li>
<li>PCB板厂工艺详解以及对应到PCB设计规则的设置</li>
<li>PCB板材与表面处理工艺的选择</li>
<li>PCB封装设计与元件库管理</li>
<li>PCB通用布局布线设计规范</li>
<li>DFM设计</li>
<li>HDI设计</li>
<li>RF电路设计</li>
<li>PCB叠层设计</li>
<li>PCB阻抗设计详解</li>
<li>高速串行接口</li>
<li>DDRx设计</li>
<li>信号完整性</li>
<li>电源完整性</li>
<li>PCB热仿真与设计</li>
<li>电磁兼容</li>
<li>CAM软件的基础操作</li>
<li>SIWave</li>
<li>HFSS</li>
<li>Q3D</li>
<li>HFSS 3D Layout</li>
<li>IcePak</li>
</ul>
<p>以上内容大的方向不变，具体细则会根据大家的反馈进行相应的微调</p>
<p>内容采用每周一更新的形式，如果视频制作比较复杂，可能稍微延期，最长不超过10天（春节、五一、国庆等长假顺延）。</p>
<p><strong>内容形式：&nbsp;&nbsp;</strong></p>
<p>内容以图文为主，涉及到软件操作以及需要精讲的部分会以视频进行讲解，视频以录播的形式不是直播授课的形式。</p>
<p>为方便大家对于信号在PCB上传输行为的理解，老wu会大量借助于电磁场3D全波仿真软件，以可视化的场视图以及电流分布视图进行讲解。</p>
</div>

<link href="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.css" rel="stylesheet">
<div id="dplayer"></div>
<script src="https://cdn.bootcss.com/dplayer/1.25.0/DPlayer.min.js"></script>
<script src="https://cdn.bootcss.com/blueimp-md5/2.12.0/js/md5.min.js"></script>
<script>
var url1="https://files.catbox.moe/su1djc.mp4";    //这里填写视频地址
var id=md5(url1);
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
        url: url1,
        pic: 'dplayer.png',
        thumbnails: 'thumbnails.jpg',
        type: 'auto',
    },
    subtitle: {
        url: 'dplayer.vtt',
        type: 'webvtt',
        fontSize: '25px',
        bottom: '10%',
        color: '#b7daff',
    },
    danmaku: {
        id: '9E2E3368B56CDBB4',
        api: 'https://api.prprpr.me/dplayer/',
        token: 'tokendemo',
        maximum: 1000,
        addition: ['https://api.prprpr.me/dplayer/v3/bilibili?aid=4157142'],
        user: 'DIYgod',
        bottom: '15%',
        unlimited: true,
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

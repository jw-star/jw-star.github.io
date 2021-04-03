# 苹果CMS设置aliplayer播放器

## 苹果cms设置阿里云本地播放器
### 编辑 aliplayer.html 
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="x-ua-compatible" content="IE=edge" >
  <meta name="viewport" content="width=device-width, height=device-height, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"/>
  <title>Aliplay记忆播放器</title>
  <link rel="stylesheet" href="https://g.alicdn.com/de/prismplayer/2.8.7/skins/default/aliplayer-min.css" />
  <script type="text/javascript" charset="utf-8" src="https://g.alicdn.com/de/prismplayer/2.8.7/aliplayer-min.js"></script>
  <script type="text/javascript" charset="utf-8" src="https://player.alicdn.com/aliplayer/presentation/js/aliplayercomponents.min.js"></script>
  <style type="text/css">
    body,html{background-color:#000;padding: 0;margin: 0;width:100%;height:100%;}
    #liryCN{width:100%;height:100%;padding:0;margin:0;display:block;}
  </style>
</head>
<body>
<div id="liryCN" class="prism-player"></div>
<script type="text/javascript">
var player = new Aliplayer({
         id: "liryCN",
     "source": parent.MacPlayer.PlayUrl,
      width: "100%",
     height: "100%",
   autoplay: true,
    preload: true,
     rePlay: false,
playsinline: false,
 useH5Prism: false,
     isLive: false,
 autoPlayDelay: 0,
autoPlayDelayDisplayText: 'YY影院,请稍待正在加载...',
 components: [{
      name: 'MemoryPlayComponent',
        type: AliPlayerComponent.MemoryPlayComponent,
      args: [true]
    }], 
}, 
function (player) {
      
           player.play();
  }
 );
 player.on('ended',function(){
 if(parent.MacPlayer.PlayLinkNext!=''){
		top.location.href = parent.MacPlayer.PlayLinkNext;
	}
 });
 var _video = document.querySelector('video');
player.on('play', function(e) {
  _video.removeEventListener('click', play);
  _video.addEventListener('click', pause);
});
player.on('pause', function(e) {
  _video.removeEventListener('click', pause);
  _video.addEventListener('click', play)
});

function play() {
  if (player) player.play();
}

function pause() {
  if (player) player.pause();
}
try{ 
//document.getElementById('liryCN').style.height = parent.MacPlayer.Height + 'px';  
}
catch(e){}
</script>
</body>
</html>
```
编辑好后上传到 网站目录/static/player/ 下
### 播放器代码填写
```html
MacPlayer.Html = '<iframe src="'+maccms.path+'/static/player/aliplayer.html" width="100%" height="'+MacPlayer.Height+'" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>'; 
MacPlayer.Show(); 
```
## aliplayer播放器效果

[![https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200412012802.png](https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200412012802.png "https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200412012802.png")](https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200412012802.png "https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200412012802.png")


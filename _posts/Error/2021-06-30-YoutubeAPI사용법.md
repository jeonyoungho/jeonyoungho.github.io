---
title: "Youtube API 사용법" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [GCP] # categories는 최대 2개까지 가능
tags: [GCP, YoutubeAPI] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 1) Google API 콘솔에 액세스
- https://console.developers.google.com/apis/dashboard<br>
<img width="1440" alt="1" src="https://user-images.githubusercontent.com/44339530/74651939-4e77bb00-51c8-11ea-9725-7607af35b016.png">

# 2) 새 프로젝트 등록
<img width="1440" alt="2" src="https://user-images.githubusercontent.com/44339530/74652059-91d22980-51c8-11ea-845d-25b8d26026f4.png">

# 3) 사용할 API 검색 및 서비스 사용 설정
<img width="1015" alt="3-1" src="https://user-images.githubusercontent.com/44339530/74652108-a8788080-51c8-11ea-8126-01e0551e70e1.png"><br>
<img width="1440" alt="3-2" src="https://user-images.githubusercontent.com/44339530/74652114-ac0c0780-51c8-11ea-9b74-6a68f9b59cfb.png"><br>
<img width="696" alt="3-3" src="https://user-images.githubusercontent.com/44339530/74652115-ad3d3480-51c8-11ea-9191-fb677db849e8.png">

# 4) 사용자 인증정보(KEY)를 발급
<img width="1117" alt="4" src="https://user-images.githubusercontent.com/44339530/74652169-cb0a9980-51c8-11ea-9d75-c6754643c6f1.png"><br>
<img width="1058" alt="4-1" src="https://user-images.githubusercontent.com/44339530/74652173-ccd45d00-51c8-11ea-84da-ae5ea64d5efd.png">

# 5) 발급된 인증키로 API 테스트
- [https://developers.google.com/apis-explorer/?hl=ko#p/youtube/v3/](https://developers.google.com/apis-explorer/?hl=ko#p/youtube/v3/)<br>
<img width="920" alt="5" src="https://user-images.githubusercontent.com/44339530/74652177-ce058a00-51c8-11ea-9d59-4cddba9879e9.png">

#### HTML 코드

~~~
<html>
<head>
  <script>
 // youtube API 불러옴
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/player_api";
var firstScriptTag = document.getElementsByTagName('script')[0];
firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

// 플레이어변수 설정
var youTubePlayer1;

function onYouTubeIframeAPIReady() {
    youTubePlayer1 = new YT.Player('youTubePlayer1', {
        width: '1000',
        height: '563',
        videoId: 'FF6CF8TZIhE', //원하는 url의 비디오 아이디 설정
        playerVars: {rel: 0},//추천영상 안보여주게 설정
        events: {
          'onReady': onPlayerReady, //로딩할때 이벤트 실행
          'onStateChange': onPlayerStateChange //플레이어 상태 변화시 이벤트실행
        }
    });//youTubePlayer1셋팅
}

function onPlayerReady(event) {
    event.target.playVideo();//자동재생
    //로딩할때 실행될 동작을 작성한다.
}

function onPlayerStateChange(event) {
    if (event.data == YT.PlayerState.PLAYING) {
        //플레이어가 재생중일때 작성한 동작이 실행된다.
    }
  }
 
$(document).ready(function () {
    $(".btn_play").on("click", function () {
        youTubePlayer1.playVideo();//재생
    });
    $(".btn_stop").on("click", function () {
        youTubePlayer1.stopVideo();//정지
    });
    $(".btn_pause").on("click", function () {
        youTubePlayer1.pauseVideo();//일시정지
    });
});
  </script>
<style>
     <!-- youtube플레이어 화면에 꽉차게 하는 css  -->
#youTubePlayer1 {position:relative;width:100%;padding-bottom:56.25%;}
#youTubePlayer1 iframe {position:absolute;width:100%;height:100%;}
</style>
<title>hello</title>
</head>
<body>
    <div id="youTubePlayer1"></div><!-- 플레이어를 불러올 영역-->
 
    <div class="btn_play">시작</div><!-- 시작버튼-->
    <div class="btn_stop">정지</div><!-- 정지버튼-->
    <div class="btn_pause">일시정지</div> <!-- 일시정지버튼-->

</body>
</html>
~~~

# 6) 실행결과
<img width="1066" alt="스크린샷 2021-06-16 오후 8 46 43" src="https://user-images.githubusercontent.com/44339530/122213487-fc41c980-cee3-11eb-8200-e4d1c9be9f8d.png">

#### 출처
- [https://bonniness.tistory.com/entry/%EA%B5%AC%EA%B8%80-Youtube-API-%EC%82%AC%EC%9A%A9-%EC%82%AC%EC%9A%A9%EC%84%A4%EC%A0%95-KEY-%EB%B0%9C%EA%B8%89-%ED%85%8C%EC%8A%A4%ED%8A%B8](https://bonniness.tistory.com/entry/%EA%B5%AC%EA%B8%80-Youtube-API-%EC%82%AC%EC%9A%A9-%EC%82%AC%EC%9A%A9%EC%84%A4%EC%A0%95-KEY-%EB%B0%9C%EA%B8%89-%ED%85%8C%EC%8A%A4%ED%8A%B8)
- [https://mcatcher.github.io/2018/01/26/youtube.html](https://mcatcher.github.io/2018/01/26/youtube.html)
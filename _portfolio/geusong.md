---
layout: work
title: 그송 온라인
img: "assets/img/portfolio/geusong.png"
images: ["assets/img/portfolio/geusong.png", "assets/img/portfolio/geusong2.png", "assets/img/portfolio/geusong3.png", "assets/img/portfolio/geusong4.png", "assets/img/portfolio/geusong5.png", "assets/img/portfolio/geusong6.png"]
date: July 2017
bootstrap: true
tags: [React, Node.js, Firebase, AWS]
---

### About
* 노래 반주 듣고 제목 맞추는 게임을 구현한 웹 어플리케이션
* React, Redux, Firebase로 실시간 멀티 플레이가 가능한 UI 구현
* 게임 진행 도중 멈추는 문제를 해결하기 위해서 밤새 디버깅을 했고 원인을 찾아서 코드를 수정 및 개선
* iTunes 검색 API를 통한 가수검색 기능구현 중에 CORS 문제가 발생하여 AWS Lambda 및 API Gateway를 통해 우회하여 문제 해결
* [멜론 차트 크롤링 오픈소스](https://github.com/iicdii/melon-chart-parser){: target="_blank" } 개발 및 배포
* Heroku Scheduler를 이용하여 정기적으로 멜론 차트를 크롤링하고 크롤링한 데이터와 iTunes API에서
검색한 데이터를 기반으로 Firebase에 데이터를 일괄 저장하는 스크립트 작성
* 아바타 상점을 위한 관리자 페이지 개발 

<br>
### Skills
<span class="badge badge-dark">HTML/CSS</span>
<span class="badge badge-dark">Node.js</span>
<span class="badge badge-dark">React</span>
<span class="badge badge-dark">Redux</span>
<span class="badge badge-dark">AWS Lambda</span>
<span class="badge badge-dark">AWS API Gateway</span>
<span class="badge badge-dark">Firebase</span>

<br>
### Resources
- [Site](http://geusong.herokuapp.com){: target="_blank" } - Heroku 무료 서버라서 최초 로딩 시간이 길어요 😭
- [melon-chart-parser](https://github.com/iicdii/melon-chart-parser){: target="_blank" }

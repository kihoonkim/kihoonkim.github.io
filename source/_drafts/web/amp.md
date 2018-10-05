why amp
monetization vs user experience
developer vs user

parallax scrolling

amp stories
page
layers
elements

https://ampbyexample.com/

1sec 7% drop shopping

amp-list
amp-mustache
amp-date-picker
amp-bind 
amp-state
amp-selector
amp-form
amp-bind-macro
amp-access
amp-pixel
amp-animation
amp-position-observer

bit.ly/amp-apple

persnalization

then component i need doesn't exist
- CSS + 


Progressive Web amps
- 80% of time spent is in user's top 3apps
- 0 number of apps the average user installs per month

| mobile web      pwa
|  
|                 apps
--------------------->

service worker : client side javascript proxy. cache, web server
manifest : 뭘 display 할지
push notification

AMP or PWA ??  https://pic3.zhimg.com/v2-9bcb3d0e6c12cd51d6ee2341feda99f7_b.gif
amp = web page = pwa
AMP Cache problem

from AMP to PWA
fast rendering and then fast communication
start fast, stay fast

amp-install-serviceworker

copy url in pwa -> share -> opens pwa without warm cache

an investment in amp today is an investment in pwa tommorow

# App shell pattern
- makes caching and goin offline easy
- allows content and app to be managed independently

Shadow DOM
- 1 window, 1 amp library instance,


# Production ready AMP pages
> generate 
write code or CMS 

> optimize
local css 50kb inline css
image optimization

> validate
ci, monitoring 
amphtml-validator
Google Search Console
lighthouse
webpagetest

> measure 
amp-analytics : measure + collect + batch
a/b testing : amp cache 때문에 server 에서 컨트롤 안됨. amp-experience

> distribute
AMP page + metadata(schema.org)
structured data testing tool
amp cache -> cors problem


# Next AMP
@mrjoro Rozier Joey

Vision : strong user-first open web forever.
Mission
Provide a user first format for web content

is the whole web user-friendly today?

> next : 
https://www.ampproject.org/roadmap
https://github.com/ampproject

Infinite scroll
Tilt-based Animation
Pan & Zoom
image Compare
PDF/Doc Viewer
Input Masking
> Media++
Video docking
sticky mute button for amp-audio
customizable video controls
> storytelling
AMP stories
Experimentally available
Overflow menu
link out : tap targets in top 80%
> Monetization
Paywalls 

> Developer tooling
automatic conversion
wordpress : amp in gutenberg
Automatic PWA + AMP

> Better URLs
web pacakagin in amp cache

> AMPHTML ads everywhere
> AMP for email
privacy control 
already supported Gmail

allow developers to grogram AMP pages with JavaScript

# Advertising & AMP
how to best monetize amp page
> Three core issues
  disruptive ads
  slow loading ads
  unsafe ads

> Ads on AMP pages
 set dimensions
 separate the ads : iframe sandbox. 
 prioritized content

> monetizion AMP pages
 

 ads on amp pages
 amp for display ads
 amp for ad landing pages 
 paywalls & payments

# etc
carousel slider
light box
parallax
facebook instant article
card news -> amp story
ken burns effect
readability : dime out or text shadow or gradation

page , background, text, logo

https://pre.veast.io

google web designer
ad create tool
celtra amp ad

why? google search
user friendly : pwa amp

now.
2 version support. 최신 + 이전

webview perfomance : 80$ phone에서도 잘 돌아감. pre loading, pre rendering 은 잘 안됨

amp story ie에서 잘 동작 안됨
css grid polifill 이슈인듯..
ie 11 support 는 당연히 되야됨

google domain rendering : security, privacy 문제 때문에 사용.. 개선 중..
 web packaging, 
 cache, pre render, privacy 때문에..

google analytics: clientId 를 통해 cross domain tracking.. 
clientid amp help page


amp 가 google web 의 미리래고 생각하는가? 비지니스 모델로서가 아닌..

광고: 대안적인 수익화 방법론으로 생각.. paywall monetazation... 
대체 될 것인가? amp가 가지고 있는 components를 브라우저로 옮겨갈 것이다. 
amp 는 component framework 의 여러 옵션중 하나로 생각 되게..
high level component가 built in,...

amp story를 왜 개발하게 되었는가?
미국, 다른 시장에서 snap chat, instagram stroy 가 인기 있다. 
풍푸한 미디어를 포함한 포멧이 인기가 많아 지고 있다.
카드 뉴스..

AMP는 빠르게 만들었다. 만들고 나서 보니..
모바일 사용자들이 컨텐츠를 소비하는 방식이 다르다. 짧게 스토리 위주로 소비.. 멀티미디어 위주..
이를 쉽게 만들 수 있게 만들어 주고 싶었다.

아직 자바스크립트가 지원 안되는데.. react, vue, angular.. 와 시너지는?
amp script 오픈 소스가 있다.
web worker에서 돌아가는 스크립트...

content missmatching error message 가 불친절 하다...
페이지 정보에 대한 제한이 있다. 

google tag manager or data components 추천??
amp를 지원하긴 함.. amp-analysis 가 유연하다.. 어떤 엔드포인트로든 보낼 수 있고, 수집하고 싶은 데이터를 수집할 수 있다.

어떤 컴포넌트든 모든 비지니스에 제공되는게 중요하다. 철학.

기존의 non amp page를 어떻게 효과적으로 컨버팅 할 수 있는가? 
구글 컨버터가 없다. 서드 파티는 있다.
content missmatch.. amp origin page를 같게 만들어라. 처음부터 amp로 만들길..

hls 지원이 아직 어렵다...





---
layout: post
title:  지킬에 댓글 기능 플러그인 추가하기
author: Aria
category: Jekyll
comments: true
---


# Jekyll에 댓글 기능 추가하기

[Disqus](https://disqus.com/)라는 플러그인을 사용하여 블로그에 댓글을 달아보자!

1. Disqus에서 회원 가입을 한다.

2. 플랜을 선택한다.

   개인 운영을 위한 사이트일 경우 광고 없는 무료 플랜을 선택할 수 있다. *2018-11-15 기준*

3. YAML Front Formatter에 아래와 같이 추가한다. 나는 `_layouts/post.html`에 추가했다.

   ```yaml
   ---
   layout: default
   comments: true
   # other options
   ---
   ```

4. `_includes/disqus.html` 파일 생성

   공식 문서에 나와있는 대로 코드를 copy & paste 하면 아래와 같다.

   ```html
   <!-- _includes/disqus.html -->
   <div id="disqus_thread"></div>
   <script>
   var disqus_config = function () {
       // Replace PAGE_URL with your page's canonical URL variable
       this.page.url = PAGE_URL;
       // Replace PAGE_IDENTIFIER with your page's unique identifier variable
       this.page.identifier = PAGE_IDENTIFIER;
   };
   
   (function() {
       // REQUIRED CONFIGURATION VARIABLE: EDIT THE SHORTNAME BELOW
       var d = document, s = d.createElement('script');
       // IMPORTANT: Replace EXAMPLE with your forum shortname!
       s.src = '//EXAMPLE.disqus.com/embed.js';
       s.setAttribute('data-timestamp', +new Date());
       (d.head || d.body).appendChild(s);
   })();
   </script>
   <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
   ```

   파일 내에 변경되어야 할 설정들은 아래와 같다.

   - this.page.url = "\{\{ site.url \}\}\{\{ page.url \}\}"로 수정
   - this.page.identifier = "\{\{ page.title \}\}"로 수정
   - this.src = { 본인의 disqus 도메인 }로 수정

5. 댓글을 노출하고 싶은 페이지(혹은 레이아웃)에 `disqus.html`을 include 한다.

   ```
   \{\% if page.comments == true \%\}
     \{\% include disqus.html \%\}
   \{\% endif \%\}
   ```

6. 페이지를 리로드 하면 끝!

7. disqus.com/admin에서 댓글에 대한 대시보드도 제공한다!

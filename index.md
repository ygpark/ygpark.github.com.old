---
layout: page
title:
---

{% include JB/setup %}
<p style="text-align:center">
<img src="/images/header-sky.png"/>
<br/>
<a href="/pages/dreams-come-true.html" class="btn">Secret</a>
</p>

<hr/>

###블로그 포스트
<ul class="posts">
	{% for post in site.posts %}
	<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>


<a href="/archive.html" class="btn">더 보기</a>

---
layout: page
title: Secret
---
{% include JB/setup %}

##최근 포스트

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

##나의 생생한 미래

###돈
돈은 100억

![돈](http://farm3.staticflickr.com/2415/2466566500_797ffb7f60_n.jpg)

###집
집은 소박하게 2채

![집](http://farm7.staticflickr.com/6069/6065244606_8bc6aca142_n.jpg)
![집](http://farm3.staticflickr.com/2581/4123527670_3381ea3ce2_n.jpg)

###여행
세계일주. 이제 에펠탑만 가보면 끝

![에펠탑](http://farm4.staticflickr.com/3395/3190129351_bd4e8a8ae9_m.jpg)

###자동차
우리집에 뒹굴거리는 잉여차

![BMW](http://farm6.staticflickr.com/5100/5540460073_524a338997_n.jpg)








---
layout: page
---
{% include JB/setup %}
<ul>
 {% for post in site.posts %}
<li>
{{ post.date | date: "%B %e, %Y" }}
<div class="cbp_tmicon cbp_tmicon-phone"></div>
<div class="cbp_tmlabel">
<h2><a href="{{ BASE_PATH }}{{ post.url }}"> {{ post.title }}  </a> </h2>
<p>{{ post.excerpt }} </p>
 <p> <a class="link" href="{{ BASE_PATH }}{{ post.url }}"> More  </a></p>
 </div>
</li>        
 {% endfor %}
</ul>
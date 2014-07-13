---
layout: page
---
{% include JB/setup %}

{% for post in site.posts %}
  <article class="hentry">
  <header>
    <div class="entry-meta">
      <span class="entry-date date published updated">
	  <time datetime="{{ post.date | date: "%B %e, %Y" }}"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.date | date: "%B %e, %Y" }}</a></time>
	  </span>
	  <span class="author vcard"><span class="fn"><a href="{{ BASE_PATH }}{{ post.url }}" title="liuyiyou">liuyiyou</a></span></span>
	  &nbsp; • &nbsp;<span class="entry-comments"><a href="{{ BASE_PATH }}{{ post.url }}#ds-thread">Comment</a></span>
      
      <span class="entry-reading-time pull-right">
        <i class="fa fa-clock-o"></i>
        
        Reading time ~1 minute
      </span><!-- /.entry-reading-time -->
      
    </div><!-- /.entry-meta -->
    
      <h1 class="entry-title"><a href="{{ BASE_PATH }}{{ post.url }}" rel="bookmark" title="{{ post.title }} " itemprop="url">{{ post.title }} </a></h1>
    
  </header>
  <div class="entry-content">
   
{{ post.excerpt }}

  </div>
   <div class="read-more2">
      <a href="{{ BASE_PATH }}{{ post.url }}"  title="{{ post.title }} " >阅读全文 </a>
   </div>
  <!-- /.entry-content -->
</article>


      
{% endfor %}

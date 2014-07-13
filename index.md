---
layout: page
---
{% include JB/setup %}

{% for post in site.posts %}
  <article class="hentry">
  <header>
    <div class="entry-meta">
      <span class="entry-date date published updated"><time datetime="2013-10-26T00:00:00-04:00"><a href="http://mmistakes.github.io/hpstr-jekyll-theme/background-image/">{{ post.date | date: "%B %e, %Y" }}</a></time></span><span class="author vcard"><span class="fn"><a href="http://mmistakes.github.io/hpstr-jekyll-theme/about/" title="About Michael Rose">Michael Rose</a></span></span>&nbsp; • &nbsp;<span class="entry-comments"><a href="http://mmistakes.github.io/hpstr-jekyll-theme/background-image/#disqus_thread">Comment</a></span>
      
      <span class="entry-reading-time pull-right">
        <i class="fa fa-clock-o"></i>
        
        Reading time ~1 minute
      </span><!-- /.entry-reading-time -->
      
    </div><!-- /.entry-meta -->
    
      <h1 class="entry-title"><a href="{{ BASE_PATH }}{{ post.url }}" rel="bookmark" title="{{ post.title }} " itemprop="url">{{ post.title }} </a></h1>
    
  </header>
  <div class="entry-content">
    



{{ post.excerpt }}





  </div><!-- /.entry-content -->
</article>


      
{% endfor %}

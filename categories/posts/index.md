---
layout: default
---

<div class="row-fluid">
    <div class="span12">
        {% for post in site.posts %}
            <div class="row-fluid">
                <div class="span12 ">
                    <div class="row-fluid">
                        <div class="span12">
                            <div class="post">
                                <div class="post-hd">
                                    <div class="post-info">
                                        <h4 class="post-title"><a href="{{site.baseurl}}{{post.url}}"> {{ post.title }}  </a> <br></h4>
                                    
                                    </div>
                                </div>
                               
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        {% endfor %}
    </div>
</div>


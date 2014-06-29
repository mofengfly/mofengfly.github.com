---
layout: home
---

<div class="index-content blog">
    <div class="section">
        <ul class="artical-cate">
            <li class="on" ><a href="/"><span>Javascript</span></a></li>
            <li ><a href="/css_post"><span>Css</span></a></li>
            <li ><a href="/nodejs"><span>NodeJs</span></a></li>
            <li ><a href="/linux"><span>Linux</span></a></li>
        </ul>

        <div class="cate-bar"><span id="cateBar"></span></div>

        <ul class="artical-list">
        {% for post in site.categories.javascript %}
            <li>
                <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
                <div class="title-desc">{{ post.description }}</div>
            </li>
        {% endfor %}
        </ul>
    </div>
    <div class="aside">
    </div>
</div>

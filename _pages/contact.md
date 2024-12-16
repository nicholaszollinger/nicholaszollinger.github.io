---
layout: contact
title: Contact
permalink: /contact/
image: '/assets/Art/Forest Shishkin Story.png'
---

**Email**: nicholas.zollinger+work@protonmail.com

<div class="social-footer">
    <ul class="social__list list-reset">
        {% for social in site.data.settings.social %}
        <li class="social__item">
            <a class="social__link" href="{{social.link}}" target="_blank" rel="noopener" aria-label="Social link"><i class="{{social.icon}}"></i></a>
        </li>
        {% endfor %}
    </ul>
</div>
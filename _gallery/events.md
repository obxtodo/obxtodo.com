---
layout: default
title: Upcoming Events
permalink: /events/
hide: true
nav_show: false
nav_order: 3
---

### Outer Banks Event Calendar
Stay up to date with festivals, live music, and local happenings.
{% comment %} 
  Capture today's date and subtract 1 day (86400 seconds) 
  to account for time zone differences during the build.
{% endcomment %}
{% assign yesterday_seconds = "now" | date: "%s" | minus: 86400 %}
{% assign visible_threshold = yesterday_seconds | date: "%Y%m%d" | plus: 0 %}

{% assign sorted_events = site.data.events | sort: "date" %}

<div class="event-list">
  {% for event in sorted_events %}
    
    {% assign event_start = event.date | date: "%Y%m%d" | plus: 0 %}
    
    {% if event.end_date %}
      {% assign event_expiry = event.end_date | date: "%Y%m%d" | plus: 0 %}
    {% else %}
      {% assign event_expiry = event_start %}
    {% endif %}

    {% comment %} Now we compare against the threshold {% endcomment %}
    {% if event_expiry >= visible_threshold %}
      <div class="event-card">
        
</div>
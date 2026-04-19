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
  1. Filter logic: Keep multi-day events visible until they are over.
{% endcomment %}
{% assign today = "now" | date: "%s" | plus: 0 %}
{% assign sorted_events = site.data.events | sort: "date" %}

<div class="event-list">
  {% for event in sorted_events %}
    
    {% assign event_start_seconds = event.date | date: "%s" | plus: 0 %}
    {% if event.end_date %}
      {% assign event_show_until = event.end_date | date: "%s" | plus: 0 %}
    {% else %}
      {% assign event_show_until = event_start_seconds %}
    {% endif %}

    {% if event_show_until >= today %}
      <div class="event-card">
        
        <div class="date-badge">
          <span class="month">{{ event.date | date: "%b" }}</span>
          <span class="day">{{ event.date | date: "%-d" }}</span>
        </div>

        <div class="event-info">
          <div class="title-row">
            <h4>{{ event.name }}</h4>
            
            {% if event.end_date %}
              <span class="multi-day-tag">Multi-Day Event</span>
            {% endif %}
          </div>
          
          <p class="location-time">
            <i class="fa-solid fa-location-dot"></i> {{ event.location }} • {{ event.time }}
          </p>
        </div>

        {% if event.link %}
          <a href="{{ event.link }}" class="btn-sm" target="_blank">Details</a>
        {% endif %}
      </div>
    {% endif %}
  {% endfor %}
</div>
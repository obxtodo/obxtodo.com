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
  We convert "now" to a simple YYYY-MM-DD string.
  Crucial: We subtract 12 hours (43200 seconds) from 'now' to create a safety 
  buffer so events don't disappear at night due to UTC/Server time offsets.
{% endcomment %}
{% assign day_buffer = "now" | date: "%s" | minus: 43200 %}
{% assign today_str = day_buffer | date: "%Y-%m-%d" %}

{% assign sorted_events = site.data.events | sort: "date" %}

<div class="event-list">
  {% for event in sorted_events %}
    
    {% comment %} Convert event dates to the same string format for comparison {% endcomment %}
    {% assign event_start = event.date | date: "%Y-%m-%d" %}
    
    {% if event.end_date %}
      {% assign event_expiry = event.end_date | date: "%Y-%m-%d" %}
    {% else %}
      {% assign event_expiry = event_start %}
    {% endif %}

    {% comment %} 
      String comparison: "2026-04-19" >= "2026-04-18" will be true.
    {% endcomment %}
    {% if event_expiry >= today_str %}
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
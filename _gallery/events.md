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

<style>
  /* 1. Reset/Consistency for the Badge */
  .date-badge {
    min-width: 65px !important;
    height: 65px !important;
    display: flex !important;
    flex-direction: column !important;
    align-items: center;
    justify-content: center;
    background: #f0fdf4;
    border-radius: 12px;
    margin-right: 1.5rem;
  }

  /* 2. Title and Tag Layout */
  .title-row {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 4px;
    flex-wrap: wrap;
  }

  .title-row h4 {
    margin: 0 !important;
    font-size: 1.15rem;
    color: #2c3e50;
  }

  /* 3. The Minimal Multi-Day Tag */
  .multi-day-tag {
    background: #e2e8f0; /* Soft grey instead of dark navy */
    color: #475569;
    font-size: 0.6rem;
    font-weight: 700;
    text-transform: uppercase;
    padding: 2px 8px;
    border-radius: 4px;
    letter-spacing: 0.5px;
    border: 1px solid #cbd5e1;
  }

  .location-time {
    margin: 0;
    font-size: 0.85rem;
    color: #7f8c8d;
  }

  @media (max-width: 600px) {
    .title-row {
      flex-direction: column;
      align-items: flex-start;
      gap: 6px;
    }
  }
</style>
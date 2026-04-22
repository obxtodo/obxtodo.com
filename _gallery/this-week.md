---
layout: default
title: This Week in the OBX
permalink: /this-week/
hide: true
nav_show: false
---

{% comment %} 
  1. Set our range boundaries.
  We reset 'now' to a clean integer for comparison.
{% endcomment %}
{% assign date_start = "now" | date: "%Y%m%d" | plus: 0 %}
{% assign date_end = "now" | date: "%s" | plus: 604800 | date: "%Y%m%d" | plus: 0 %}

<div class="week-header" style="margin-bottom: 30px;">
  <h3 style="margin-bottom: 5px;">This Week in the Outer Banks</h3>
  <p style="font-size: 0.9rem; color: #7f8c8d;">
    Showing events for: 
    <strong>{{ "now" | date: "%b %d" }}</strong> – 
    <strong>{{ "now" | date: "%s" | plus: 604800 | date: "%b %d" }}</strong>
  </p>
</div>

<div class="event-list">
  {% assign sorted_events = site.data.events | sort: "sort_id" %}
  {% assign count = 0 %}

  {% for event in sorted_events %}
    {% comment %} Convert start and end dates to integers for comparison {% endcomment %}
    {% assign event_start = event.date | date: "%Y%m%d" | plus: 0 %}
    
    {% if event.end_date %}
      {% assign event_finish = event.end_date | date: "%Y%m%d" | plus: 0 %}
    {% else %}
      {% assign event_finish = event_start %}
    {% endif %}

    {% comment %} 
      Logic: Show if the event is active at ANY POINT during this 7-day window.
    {% endcomment %}
    {% if event_start <= date_end and event_finish >= date_start %}
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
            {% if event.other %}
              <span class="multi-day-tag">{{ event.other }}</span>
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
      {% assign count = count | plus: 1 %}
    {% endif %}
  {% endfor %}

  {% if count == 0 %}
    <p>No major events scheduled for this window. Perfect time for a beach day!</p>
  {% endif %}
</div>

<style>
  /* Use the same styling as events.md for brand consistency */
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

  .multi-day-tag {
    background: #e2e8f0;
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
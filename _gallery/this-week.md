---
layout: default
title: This Week in the OBX
permalink: /this-week/
hide: true
nav_show: false
---


<section class="weekly-snapshot">
  <div class="snapshot-header">
    <h3>The Weekly Outlook</h3>
    <p>Hand-picked events for <span id="week-start-display">...</span> — <span id="week-end-display">...</span></p>
  </div>

  <div id="weekly-event-list" class="event-list" style="margin-top: 20px;">
    <p style="text-align: center; color: #64748b;">Gathering this week's schedule...</p>
  </div>
</section>

<script id="events-data" type="application/json">
[
  {% for event in site.data.events %}
    {
      "name": {{ event.name | jsonify }},
      "sort_id": {{ event.sort_id | default: 999 }},
      "start": "{{ event.date }}",
      "end": "{{ event.end_date | default: event.date }}",
      "time": {{ event.time | jsonify }},
      "location": {{ event.location | jsonify }},
      "link": {{ event.link | jsonify }}
    }{% unless forloop.last %},{% endunless %}
  {% endfor %}
]
</script>

<script>
document.addEventListener('DOMContentLoaded', function() {
    renderWeeklyEvents();
});

function renderWeeklyEvents() {
    const container = document.getElementById('weekly-event-list');
    const dataElement = document.getElementById('events-data');
    if (!container || !dataElement) return;

    let events = JSON.parse(dataElement.textContent);
    
    // 1. Calculate the Monday-Sunday window
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dayOfWeek = today.getDay(); // 0 (Sun) to 6 (Sat)

    let mon = new Date(today);
    
    // Logic: If today is Sunday(0), go back 6 days to Monday.
    // Otherwise, go back (dayOfWeek - 1) days to Monday.
    const diffToMon = dayOfWeek === 0 ? 6 : dayOfWeek - 1;
    mon.setDate(today.getDate() - diffToMon);

    let sun = new Date(mon);
    sun.setDate(mon.getDate() + 6);

    const startWindow = mon.toISOString().slice(0, 10);
    const endWindow = sun.toISOString().slice(0, 10);

    // Update Header Text
    const options = { month: 'short', day: 'numeric' };
    document.getElementById('week-start-display').textContent = mon.toLocaleDateString('en-US', options);
    document.getElementById('week-end-display').textContent = sun.toLocaleDateString('en-US', options);

    // 2. Filter: Event overlaps the Mon-Sun window
    let filtered = events.filter(e => {
        const eventStart = e.start;
        const eventEnd = e.end || e.start;
        return eventEnd >= startWindow && eventStart <= endWindow;
    });

    // 3. Sort by sort_id
    filtered.sort((a, b) => (a.sort_id || 999) - (b.sort_id || 999));

    if (filtered.length > 0) {
        container.innerHTML = ''; 
        filtered.forEach(event => {
            const d = new Date(event.start + 'T00:00:00');
            const isMultiDay = event.end && event.end !== event.start;

            container.innerHTML += `
              <div class="event-card" style="display: flex; align-items: center; justify-content: space-between; gap: 15px; margin-bottom: 15px;">
                <a href="${event.link}" class="btn-sm" target="_blank" style="flex-shrink: 0;">Details</a>
                <div class="event-info" style="flex-grow: 1; min-width: 0;">
                  <div class="title-row" style="display: flex; align-items: center; gap: 8px; flex-wrap: wrap;">
                    <h4 style="margin: 0;">${event.name}</h4>
                    ${isMultiDay ? '<span class="multi-day-tag">Multi-Day</span>' : ''}
                  </div>
                  <p class="location-time" style="margin: 4px 0 0 0;">
                    <i class="fa-solid fa-location-dot"></i> ${event.location} • ${event.time}
                  </p>
                </div>
                <div class="date-container" style="display: flex; align-items: center; justify-content: flex-end; flex-shrink: 0; min-width: 60px;">
                  <div class="date-badge">
                    <span class="month">${d.toLocaleString('default', { month: 'short' })}</span>
                    <span class="day">${d.getDate()}</span>
                  </div>
                </div>
              </div>`;
        });
    } else {
        container.innerHTML = `<p style="text-align: center; color: #64748b; padding: 20px;">No events scheduled for this week.</p>`;
    }
}
</script>

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
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

<div id="dynamic-event-list" class="event-list" style="margin-top: 20px;">
  <p style="text-align: center; color: #64748b;">Loading local events...</p>
</div>

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
  function renderTodayEvents() {
    const container = document.getElementById('dynamic-event-list');
    const dataElement = document.getElementById('events-data');
    if (!container || !dataElement) return;

    let events = JSON.parse(dataElement.textContent);
    
    // Get local date (YYYY-MM-DD)
    const now = new Date();
    const offset = now.getTimezoneOffset() * 60000;
    const todayStr = (new Date(now - offset)).toISOString().slice(0, 10);

    // 1. Filter: Today's date must be >= start AND <= end
let filtered = events.filter(e => {
    const expiryDate = e.end || e.start;
    return todayStr <= expiryDate;
});
    // 2. Sort by sort_id
    filtered.sort((a, b) => a.sort_id - b.sort_id);

    if (filtered.length > 0) {
        container.innerHTML = ''; 
        filtered.forEach(event => {
            // JS Date helper for the badges
            const d = new Date(event.start + 'T00:00:00');
            const month = d.toLocaleString('default', { month: 'short' });
            const day = d.getDate();

            const dEnd = new Date(event.end + 'T00:00:00');
            const monthEnd = dEnd.toLocaleString('default', { month: 'short' });
            const dayEnd = dEnd.getDate();
            const isMultiDay = event.start !== event.end;

            container.innerHTML += `
              <div class="event-card" style="display: flex; align-items: center; justify-content: space-between; gap: 15px;">
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
                  <div class="date-badge" style="margin-right: 0 !important;">
                    <span class="month">${month}</span>
                    <span class="day">${day}</span>
                  </div>
                  ${isMultiDay ? `
                    <span style="font-weight: 800; color: #58C896; margin: 0 6px;">-</span>
                    <div class="date-badge" style="margin-right: 0 !important;">
                      <span class="month">${monthEnd}</span>
                      <span class="day">${dayEnd}</span>
                    </div>
                  ` : ''}
                </div>
              </div>`;
        });
    } else {
        container.innerHTML = `
            <div class="event-card" style="justify-content: center; padding: 25px; text-align: center; border: 1px dashed #cbd5e1; background: transparent;">
                <div class="event-info">
                    <h4 style="color: #64748b !important; margin-bottom: 8px !important;">No events scheduled for today</h4>
                </div>
            </div>`;
    }
}

// Ensure you call the function!
renderTodayEvents();
  </script>
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
      "start": "{{ event.date | date: '%Y-%m-%d' }}",
      "end": "{{ event.end_date | default: event.date | date: '%Y-%m-%d' }}",
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
    const dayOfWeek = today.getDay(); // 0 is Sunday, 1 is Monday, etc.

    // Calculate how many days to subtract to get back to Monday
    // If today is Sunday (0), we go back 6 days. Otherwise, go back (dayOfWeek - 1) days.
    const distanceToMonday = dayOfWeek === 0 ? 6 : dayOfWeek - 1;
    
    let monday = new Date(today);
    monday.setDate(today.getDate() - distanceToMonday);

    let sunday = new Date(monday);
    sunday.setDate(monday.getDate() + 6);

    // HELPER: Safely format local dates to YYYY-MM-DD
    const formatLocalISO = (date) => {
        const y = date.getFullYear();
        const m = String(date.getMonth() + 1).padStart(2, '0');
        const d = String(date.getDate()).padStart(2, '0');
        return `${y}-${m}-${d}`;
    };

    const startWindow = formatLocalISO(monday);
    const endWindow = formatLocalISO(sunday);

    // --- DYNAMIC HEADER LOGIC ---
    const options = { month: 'short', day: 'numeric' };
    document.getElementById('week-start-display').textContent = monday.toLocaleDateString('en-US', options);
    document.getElementById('week-end-display').textContent = sunday.toLocaleDateString('en-US', options);

    // 2. Filter: Event must overlap the Mon-Sun window
    let filtered = events.filter(e => {
        const eventStart = e.start;
        const eventEnd = e.end || e.start;
        return eventEnd >= startWindow && eventStart <= endWindow;
    });

    // 3. Sort by sort_id
    filtered.sort((a, b) => (a.sort_id || 999) - (b.sort_id || 999));

    // HELPER: Format YYYY-MM-DD strings for the visual Date Badges
    const formatDate = (dateStr) => {
        const [year, month, day] = dateStr.split('-');
        const d = new Date(year, month - 1, day);
        return {
            month: d.toLocaleString('default', { month: 'short' }),
            day: d.getDate()
        };
    };

    // 4. Render
    if (filtered.length > 0) {
        container.innerHTML = ''; 
        filtered.forEach(event => {
            const isMultiDay = event.end && event.end !== event.start;
            const startParts = formatDate(event.start); 

            container.innerHTML += `
              <div class="event-card" style="display: flex; align-items: center; justify-content: space-between; gap: 15px; margin-bottom: 20px; padding: 15px;">
                <a href="${event.link}" class="btn-sm" target="_blank" style="flex-shrink: 0;">Details</a>

                <div class="event-info" style="flex-grow: 1; min-width: 0;">
                  <h4 style="margin: 0; font-size: 1.1rem; text-align: left;">${event.name}</h4>
                  <p class="location-time" style="margin: 4px 0 0 0; font-size: 0.85rem; color: #64748b; text-align: left;">
                    <i class="fa-solid fa-location-dot"></i> ${event.location} • ${event.time}
                  </p>
                </div>
                
                <div class="date-container" style="display: flex; align-items: center; justify-content: center; flex-shrink: 0;">
                  <div class="date-badge" style="margin-right: 0 !important;">
                    <span class="month">${startParts.month}</span>
                    <span class="day">${startParts.day}</span>
                  </div>

                  ${isMultiDay ? `
                    <div class="event-dash-wrapper" style="padding: 0 8px; color: #64748b;">
                      <span class="event-dash">–</span>
                    </div>
                    
                    <div class="date-badge" style="margin-right: 0 !important;">
                      <span class="month">${formatDate(event.end).month}</span>
                      <span class="day">${formatDate(event.end).day}</span>
                    </div>
                  ` : ''}
                </div>
              </div>`;
        });
    } else {
        container.innerHTML = `<p style="text-align: center; color: #64748b; padding: 20px;">No events scheduled for this week yet.</p>`;
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
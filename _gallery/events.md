---
layout: default
title: Upcoming Events
permalink: /events/
hide: true
nav_show: false
nav_order: 3
---

<div class="snapshot-header">
  <h3>Outer Banks Event Calendar</h3>
  <p id="calendar-subtitle">Stay up to date with festivals, live music, and local happenings.</p>
</div>

<div class="filters-outer-wrapper" style="margin-bottom: 30px; text-align: left;">
  <button id="filter-toggle-btn" onclick="toggleFilters()" class="filter-main-toggle">
    <i class="fa-solid fa-sliders"></i> Filters 
    <i id="toggle-icon" class="fa-solid fa-chevron-down" style="transition: transform 0.3s ease;"></i>
  </button>

  <div id="filter-wrapper-inner" class="collapsible-content">
    <div style="padding: 20px 0;">
      
      <p class="filter-label">Viewing Dates</p>
      <div class="date-filter-row">
        <input type="date" id="start-date" onchange="updateDateFilter()" class="date-picker-clean">
        <span style="color: #cbd5e1;">to</span>
        <input type="date" id="end-date" onchange="updateDateFilter()" class="date-picker-clean">
        <button onclick="resetDates()" class="reset-link-btn">
          <i class="fa-solid fa-rotate-left"></i> Reset
        </button>
      </div>

      <p class="filter-label">Category</p>
      <div id="category-filter-container" class="filter-row"></div>
      
      <p class="filter-label" style="margin-top: 20px;">Town</p>
      <div id="location-filter-container" class="filter-row"></div>
    </div>
  </div>
</div>

<div id="upcoming-event-list" class="event-list" style="margin-top: 20px;">
  <p style="text-align: center; color: #64748b;">Loading local events...</p>
</div>

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
      "link": {{ event.link | jsonify }},
      {% comment %} Force category to always be an array for JS processing {% endcomment %}
      "category": {% if event.category.first %}{{ event.category | jsonify }}{% else %}[{{ event.category | default: "general" | jsonify }}]{% endif %}
    }{% unless forloop.last %},{% endunless %}
  {% endfor %}
]
</script>

<script>
let allEvents = []; 
let filteredByDateWindow = []; 
let activeCategory = 'all';
let activeLocation = 'all';

document.addEventListener('DOMContentLoaded', function() {
    const dataElement = document.getElementById('events-data');
    if (dataElement) {
        allEvents = JSON.parse(dataElement.textContent);
        const today = new Date().toISOString().split('T')[0];
        document.getElementById('start-date').value = today;
        initializePage();
    }
});

function toProperCase(str) {
    if (!str) return "";
    return str.replace(/-/g, ' ')
              .toLowerCase()
              .split(' ')
              .map(word => word.charAt(0).toUpperCase() + word.slice(1))
              .join(' ');
}

function toggleFilters() {
    const content = document.getElementById('filter-wrapper-inner');
    const icon = document.getElementById('toggle-icon');
    const btn = document.getElementById('filter-toggle-btn');
    content.classList.toggle('is-open');
    icon.classList.toggle('rotate-icon');
    
    if (content.classList.contains('is-open')) {
        btn.style.background = "#1e293b";
        btn.style.color = "white";
    } else {
        btn.style.background = "#f1f5f9";
        btn.style.color = "#1e293b";
    }
}

function initializePage() {
    const isWeeklyView = document.getElementById('weekly-event-list');
    if (isWeeklyView) {
        // Logic for Monday-Sunday window (same as before)
        const today = new Date();
        const dayOfWeek = today.getDay();
        const distanceToMonday = dayOfWeek === 0 ? 6 : dayOfWeek - 1;
        let monday = new Date(today); monday.setDate(today.getDate() - distanceToMonday);
        let sunday = new Date(monday); sunday.setDate(monday.getDate() + 6);
        
        document.getElementById('start-date').value = monday.toISOString().split('T')[0];
        document.getElementById('end-date').value = sunday.toISOString().split('T')[0];
        
        const subtitle = document.getElementById('calendar-subtitle');
        if (subtitle) {
            subtitle.innerText = `Hand-picked events for ${monday.toLocaleDateString('en-US', {month:'short', day:'numeric'})} — ${sunday.toLocaleDateString('en-US', {month:'short', day:'numeric'})}`;
        }
    }
    applyCombinedFilters();
}

function updateDateFilter() {
    applyCombinedFilters();
}

function resetDates() {
    const today = new Date().toISOString().split('T')[0];
    document.getElementById('start-date').value = today;
    document.getElementById('end-date').value = '';
    applyCombinedFilters();
}

function generateFilters(eventsInView) {
    const catContainer = document.getElementById('category-filter-container');
    const locContainer = document.getElementById('location-filter-container');
    if (!catContainer || !locContainer) return;

    let rawCats = [];
    eventsInView.forEach(e => {
        const cats = Array.isArray(e.category) ? e.category : [e.category || 'general'];
        rawCats = rawCats.concat(cats);
    });
    const categories = [...new Set(rawCats)].sort();

    const locations = [...new Set(eventsInView.map(e => {
        const parts = e.location.split(',');
        return parts.length > 1 ? parts[parts.length - 1].trim() : e.location.trim();
    }))].sort();

    catContainer.innerHTML = `<button class="filter-btn ${activeCategory === 'all' ? 'active' : ''}" data-type="category" data-val="all" onclick="updateFilter('category', 'all')">All Types</button>` + 
        categories.map(cat => `<button class="filter-btn ${activeCategory === cat ? 'active' : ''}" data-type="category" data-val="${cat}" onclick="updateFilter('category', '${cat}')">${toProperCase(cat)}</button>`).join('');

    locContainer.innerHTML = `<button class="filter-btn ${activeLocation === 'all' ? 'active' : ''}" data-type="location" data-val="all" onclick="updateFilter('location', 'all')">All Towns</button>` + 
        locations.map(loc => `<button class="filter-btn ${activeLocation === loc ? 'active' : ''}" data-type="location" data-val="${loc}" onclick="updateFilter('location', '${loc}')">${toProperCase(loc)}</button>`).join('');
}

function updateFilter(type, value) {
    if (type === 'category') activeCategory = value;
    if (type === 'location') activeLocation = value;
    document.querySelectorAll(`.filter-btn[data-type="${type}"]`).forEach(btn => {
        btn.classList.toggle('active', btn.getAttribute('data-val') === value);
    });
    applyCombinedFilters();
}
function applyCombinedFilters() {
    const startDate = document.getElementById('start-date').value;
    const endDate = document.getElementById('end-date').value;

    const eventsInDateRange = allEvents.filter(e => {
        const eventEnd = e.end || e.start;
        const startsAfterOrOn = !startDate || eventEnd >= startDate;
        const endsBeforeOrOn = !endDate || e.start <= endDate;
        return startsAfterOrOn && endsBeforeOrOn;
    });

    generateFilters(eventsInDateRange);

    const finalResults = eventsInDateRange.filter(event => {
        // Since we forced category to be an array in the Liquid block:
        const matchesCategory = (activeCategory === 'all' || event.category.includes(activeCategory));
        
        const parts = event.location.split(',');
        const town = parts.length > 1 ? parts[parts.length - 1].trim() : event.location.trim();
        const matchesLocation = (activeLocation === 'all' || town === activeLocation);
        
        return matchesCategory && matchesLocation;
    });

    renderEvents(finalResults);
}

function renderEvents(eventsToRender) {
    const container = document.getElementById('upcoming-event-list') || document.getElementById('weekly-event-list');
    eventsToRender.sort((a, b) => a.start.localeCompare(b.start) || (a.sort_id - b.sort_id));

    const formatDate = (dateStr) => {
        const [year, month, day] = dateStr.split('-');
        const d = new Date(year, month - 1, day);
        return { month: d.toLocaleString('default', { month: 'short' }), day: d.getDate() };
    };

    if (eventsToRender.length > 0) {
        container.innerHTML = ''; 
        eventsToRender.forEach(event => {
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
        container.innerHTML = `<p style="text-align: left; color: #64748b; padding: 40px;">No events found for this combination.</p>`;
    }
}
</script>

<style>
/* 1. Toggle Button Styling */
.filter-main-toggle {
  background: #f1f5f9;
  border: 1px solid #cbd5e1;
  padding: 10px 16px;
  border-radius: 8px;
  cursor: pointer;
  font-family: 'Montserrat', sans-serif;
  font-size: 0.85rem;
  font-weight: 600;
  color: #1e293b;
  display: flex;
  align-items: center;
  gap: 10px;
  transition: all 0.3s ease;
}

/* 2. Date Picker Row Styling */
.date-filter-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 25px;
  flex-wrap: wrap;
}

.date-picker-clean {
  border: none;
  border-bottom: 2px solid #e2e8f0;
  font-family: 'Montserrat', sans-serif;
  font-size: 0.9rem;
  padding: 5px 0;
  color: #475569;
  background: transparent;
  outline: none;
}

.date-picker-clean:focus {
  border-color: #1e293b;
}

.reset-link-btn {
  background: none;
  border: none;
  color: #94a3b8;
  font-size: 0.75rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 5px;
  margin-left: 10px;
  transition: color 0.2s;
}

.reset-link-btn:hover {
  color: #ef4444;
}

/* 3. Animation Wrapper */
.collapsible-content {
  max-height: 0;
  overflow: hidden;
  opacity: 0;
  transition: max-height 0.4s ease-out, opacity 0.3s ease;
}

.collapsible-content.is-open {
  max-height: 800px;
  opacity: 1;
}

.rotate-icon { transform: rotate(180deg); }

/* 4. Labels and Pills */
.filter-label {
  font-size: 0.65rem;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  color: #94a3b8;
  margin-bottom: 10px;
  font-weight: 600;
}

.filter-row {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.filter-btn {
  background: white;
  border: 1px solid #e2e8f0;
  padding: 6px 14px;
  border-radius: 20px;
  font-family: 'Montserrat', sans-serif;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 0.2s ease;
  color: #64748b;
}

.filter-btn.active {
  background: #1e293b;
  color: white;
  border-color: #1e293b;
}

.date-badge {
    min-width: 60px;
    height: 60px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: #f0fdf4;
    border-radius: 10px;
}
</style>
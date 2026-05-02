---
layout: default
title: Local Resources
permalink: /resources/
hide: true
nav_show: false
nav_order: 7
---

<div class="snapshot-header">
  <h3>Local Resources</h3>
</div>

<div>
  <div class="resources-layout">
    {% for group in site.data.resources %}
      <section class="resource-section">
        <div class="category-divider">
          <span class="category-label">{{ group.category }}</span>
          <div class="divider-line"></div>
        </div>
        
        <div class="link-grid">
          {% for link in group.links %}
            <a href="{{ link.url }}" target="_blank" class="modern-resource-item">
              <i class="fa-solid fa-arrow-up-right-from-square card-link-icon"></i>
              
              <div class="item-content">
                <div class="text-box">
                  <h4>{{ link.title }}</h4>
                  <p>{{ link.desc }}</p>
                </div>
              </div>
            </a>
          {% endfor %}
        </div>
      </section>
    {% endfor %}
  </div>
</div>

<style>
  /* Base Container */
  .resources-container {
    padding-bottom: 100px;
  }

  /* Section Dividers */
  .category-divider {
    display: flex;
    align-items: center;
    gap: 20px;
    margin: 40px 0 25px 0;
  }

  .category-label {
    font-family: 'Montserrat', sans-serif;
    font-weight: 700;
    font-size: 0.75rem;
    text-transform: uppercase;
    letter-spacing: 2px;
    color: #58C896; /* Brand Green */
    white-space: nowrap;
  }

  .divider-line {
    height: 1px;
    background: #e2e8f0;
    width: 100%;
  }

  /* Link Grid */
  .link-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }

  .modern-resource-item {
    position: relative; /* Context for the absolute icon */
    text-decoration: none;
    background: #f8fafc;
    padding: 24px 40px 24px 20px; /* Extra right padding so text doesn't hit icon */
    border-radius: 12px;
    border: 1px solid transparent;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    display: block;
  }

  .modern-resource-item:hover {
    background: #ffffff;
    border-color: #58C896;
    box-shadow: 0 10px 20px rgba(88, 200, 150, 0.1);
    transform: translateY(-2px);
  }

  /* The External Link Icon */
  .card-link-icon {
    position: absolute;
    top: 18px;
    right: 18px;
    font-size: 0.7rem;
    color: #cbd5e1;
    transition: color 0.3s ease;
  }

  .modern-resource-item:hover .card-link-icon {
    color: #58C896;
  }

  .item-content {
    display: flex;
    align-items: flex-start;
    gap: 15px;
  }

  .text-box h4 {
    font-family: 'Montserrat', sans-serif !important;
    font-size: 1rem !important;
    margin: 0 0 4px 0 !important;
    color: #1e293b !important;
    font-weight: 600 !important;
  }

  .text-box p {
    font-size: 0.85rem !important;
    color: #64748b !important;
    margin: 0 !important;
    line-height: 1.5 !important;
  }

  /* Mobile Optimization */
  @media (max-width: 768px) {
    .link-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
---
title: Publications
---

# Publications

<link rel="stylesheet" href="/assets/style.css">

<div class="filters">
  <input id="q" class="search" type="search" placeholder="Search title, author, venue, keyword…" />
  <select id="topic">
    <option value="">All topics</option>
    {% assign all_topics = "" | split: "" %}
    {% for p in site.data.papers %}
      {% if p.topics %}
        {% for t in p.topics %}{% unless all_topics contains t %}{% assign all_topics = all_topics | push: t %}{% endunless %}{% endfor %}
      {% endif %}
    {% endfor %}
    {% assign all_topics = all_topics | sort %}
    {% for t in all_topics %}
      <option value="{{ t | escape }}">{{ t }}</option>
    {% endfor %}
  </select>
</div>

<div id="pubs">
  {% assign topics_seen = "" | split: "" %}
  {% for p in site.data.papers %}
    {% assign p_topics = p.topics | default: "Uncategorized" %}
    {% if p_topics == "Uncategorized" %}
      {% assign topics = "Uncategorized" | split: "|" %}
    {% else %}
      {% assign topics = p.topics %}
    {% endif %}
    {% for t in topics %}
      {% unless topics_seen contains t %}
        {% assign topics_seen = topics_seen | push: t %}
      {% endunless %}
    {% endfor %}
  {% endfor %}

  {% assign topics_seen = topics_seen | sort %}
  {% for t in topics_seen %}
    <h2 class="group-title" data-topic="{{ t | escape }}">{{ t }}</h2>
    <div class="cards">
      {% for p in site.data.papers %}
        {% assign has_t = p.topics and (p.topics contains t) %}
        {% if (t == "Uncategorized" and (p.topics == nil or p.topics == empty)) or has_t %}
          {% include paper_card.html
             title=p.title authors=p.authors venue=p.venue year=p.year
             url=p.url pdf=p.pdf doi=p.doi arxiv=p.arxiv code=p.code
             topics=p.topics thumb=p.thumb thumb_alt=p.thumb_alt %}
        {% endif %}
      {% endfor %}
    </div>
  {% endfor %}
</div>

<script>
(function(){
  const q = document.getElementById('q');
  const topic = document.getElementById('topic');
  function apply(){
    const term = q.value.trim().toLowerCase();
    const t = topic.value;
    const groups = Array.from(document.querySelectorAll('#pubs .group-title'));
    groups.forEach(h2=>{
      const grid = h2.nextElementSibling;
      let visibleCount = 0;
      const inTopic = !t || (h2.dataset.topic === t);
      Array.from(grid.querySelectorAll('.paper-card')).forEach(card=>{
        const ok = inTopic && (!term || card.textContent.toLowerCase().includes(term));
        card.style.display = ok ? '' : 'none';
        if (ok) visibleCount++;
      });
      const showGroup = inTopic && visibleCount > 0;
      h2.style.display = showGroup ? '' : 'none';
      grid.style.display = showGroup ? 'grid' : 'none';
    });
  }
  q.addEventListener('input', apply);
  topic.addEventListener('change', apply);
  apply();
})();
</script>

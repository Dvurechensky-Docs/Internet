---
layout: default
title: 'Поиск'
---

<h1>Поиск по сайту</h1>
<input type="text" id="search" placeholder="Введите текст для поиска...">
<ul id="results"></ul>

<script src="https://unpkg.com/lunr/lunr.js"></script>
<script>
fetch('{{ "/search.json" | relative_url }}')
  .then(res => res.json())
  .then(data => {
    const idx = lunr(function () {
      this.ref('url')
      this.field('title')
      this.field('content')
      data.forEach(doc => this.add(doc))
    });

    const input = document.querySelector('#search');
    const results = document.querySelector('#results');

    input.addEventListener('input', function() {
      const query = this.value.trim();
      results.innerHTML = '';
      if (query.length < 2) return; // поиск только с 2+ символов
      const searchResults = idx.search(query);
      results.innerHTML = searchResults.map(r => {
        const doc = data.find(d => d.url === r.ref);
        return `<li><a href="${doc.url}">${doc.title}</a></li>`;
      }).join('');
    });
  });
</script>

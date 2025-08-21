---
layout: default
title: 'Поиск'
date: 2025-08-21
published: true
---

<style>
/* Стили для поиска */
#search-container {
  max-width: 700px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
}
#search {
  width: 100%;
  padding: 10px 15px;
  font-size: 16px;
  border: 2px solid #333;
  border-radius: 6px;
  margin-bottom: 15px;
}
#results {
  list-style: none;
  padding: 0;
}
#results li {
  background: #f3f3f3;
  margin-bottom: 8px;
  padding: 10px;
  border-radius: 5px;
  transition: background 0.2s;
}
#results li a {
  text-decoration: none;
  color: #333;
  font-weight: bold;
}
#results li:hover {
  background: #e0e0e0;
}
</style>

<div id="search-container">
  <input type="text" id="search" placeholder="Введите текст для поиска...">
  <ul id="results"></ul>
  <a href="index.html">⬆ Вернуться к оглавлению</a>
</div>

<!-- Подключаем elasticlunr.js -->
<script src="https://unpkg.com/lunr/lunr.js"></script>
<script src="https://unpkg.com/lunr-languages/lunr.stemmer.support.js"></script>
<script src="https://unpkg.com/lunr-languages/lunr.ru.js"></script>
<script src="https://unpkg.com/lunr-languages/lunr.multi.js"></script>
<script>
fetch('{{ "/search.json" | relative_url }}')
  .then(res => res.json())
  .then(data => {
    const idx = lunr(function () {
      this.use(lunr.multiLanguage('ru','en'))  // русский + английский
      this.ref('url')
      this.field('title')
      this.field('content')
      data.forEach(doc => this.add(doc))
    })

    const input = document.querySelector('#search');
    const results = document.querySelector('#results');

    input.addEventListener('input', function() {
      const query = this.value.trim();
      results.innerHTML = '';
      if (query.length < 2) return;
      const searchResults = idx.search(query, {expand: true});
      results.innerHTML = searchResults.map(r => {
        const doc = data.find(d => d.url === r.ref);
        return `<li><a href="${doc.url}">${doc.title}</a></li>`;
      }).join('');
    });
  });
</script>

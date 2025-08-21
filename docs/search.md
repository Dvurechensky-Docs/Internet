---
layout: default
title: 'Поиск 🔍'
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

/* Стили для поиска */
#results {
  list-style: none;
  padding: 0;
}

.search-result {
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 10px 15px;
  margin-bottom: 10px;
  background: #f9f9f9;
  transition: background 0.2s;
}

.search-result a {
  text-decoration: none;
  color: #333;
  font-weight: bold;
  font-size: 16px;
}

.search-result p {
  margin: 5px 0 0 0;
  font-size: 14px;
  color: #555;
}

.search-result:hover {
  background: #e0e0e0;
}

</style>

<div id="search-container">
  <input type="text" id="search" placeholder="Введите текст для поиска 🔍">
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
      this.use(lunr.multiLanguage('ru','en'))
      this.ref('url')
      this.field('title')
      this.field('content')
      data.forEach(doc => this.add(doc))
    });

    const input = document.querySelector('#search');
    const results = document.querySelector('#results');

    input.addEventListener('input', function() {
      const query = this.value.trim().toLowerCase();
      results.innerHTML = '';
      if (query.length < 2) return;

      const searchResults = idx.search(query, {expand: true});
      
      // Группируем по url
      const grouped = {};
      searchResults.forEach(r => {
        const doc = data.find(d => d.url === r.ref && d.content.toLowerCase().includes(query));
        if (!doc) return;
        if (!grouped[doc.url]) grouped[doc.url] = {title: doc.title, rows: []};
        grouped[doc.url].rows.push(doc.content);
      });

      for (const url in grouped) {
        const catBlock = document.createElement('div');
        catBlock.className = 'search-result';
        catBlock.innerHTML = `<a href="${url}">${grouped[url].title}</a>`;
        const ul = document.createElement('ul');
        grouped[url].rows.forEach(row => {
          const li = document.createElement('li');
          li.textContent = row;
          ul.appendChild(li);
        });
        catBlock.appendChild(ul);
        results.appendChild(catBlock);
      }
    });
  });
</script>

# Foodporn
We are targeting young travelers who do physical activities and surfing here in Siargao Islands
/* Food Porn Siargao — site behaviour
   Progressive enhancement only: with JS disabled the full menu is still visible. */
(function () {
  'use strict';

  /* ---------- Sticky nav + back-to-top ---------- */
  var nav = document.getElementById('nav');
  var toTop = document.getElementById('toTop');

  function onScroll() {
    if (nav) nav.classList.toggle('is-stuck', window.scrollY > 8);
    if (toTop) toTop.classList.toggle('is-visible', window.scrollY > 700);
  }
  window.addEventListener('scroll', onScroll, { passive: true });
  onScroll();

  /* ---------- Mobile nav ---------- */
  var toggle = document.getElementById('navToggle');
  var links = document.getElementById('navLinks');

  function closeNav() {
    if (!links || !toggle) return;
    links.classList.remove('is-open');
    toggle.setAttribute('aria-expanded', 'false');
  }

  if (toggle && links) {
    toggle.addEventListener('click', function () {
      var open = links.classList.toggle('is-open');
      toggle.setAttribute('aria-expanded', open ? 'true' : 'false');
    });
    links.addEventListener('click', function (e) {
      if (e.target.closest('a')) closeNav();
    });
    document.addEventListener('keydown', function (e) {
      if (e.key === 'Escape') closeNav();
    });
  }

  /* ---------- Menu search ---------- */
  var search = document.getElementById('menuSearch');
  var noResults = document.getElementById('noResults');
  var items = Array.prototype.slice.call(document.querySelectorAll('.item'));
  var cats = Array.prototype.slice.call(document.querySelectorAll('.cat'));

  function applyFilter(raw) {
    var q = (raw || '').trim().toLowerCase();
    var shown = 0;

    items.forEach(function (el) {
      var hit = !q || (el.textContent || '').toLowerCase().indexOf(q) !== -1;
      el.classList.toggle('is-hidden', !hit);
      if (hit) shown++;
    });

    cats.forEach(function (cat) {
      var visible = cat.querySelector('.item:not(.is-hidden)');
      var isToppings = cat.id === 'toppings';
      // toppings have no individual items — keep them only while not searching
      cat.classList.toggle('is-empty', !visible && !(isToppings && !q));
    });

    if (noResults) noResults.hidden = !(q && shown === 0);
  }

  if (search) {
    search.addEventListener('input', function () { applyFilter(search.value); });
    search.addEventListener('keydown', function (e) {
      if (e.key === 'Escape') { search.value = ''; applyFilter(''); }
    });
  }

  /* ---------- Reveal on scroll ---------- */
  var revealTargets = document.querySelectorAll(
    '.section-head, .fav-card, .branch, .stats li, .board, .cta-inner'
  );

  if ('IntersectionObserver' in window) {
    var io = new IntersectionObserver(function (entries) {
      entries.forEach(function (entry) {
        if (entry.isIntersecting) {
          entry.target.classList.add('is-in');
          io.unobserve(entry.target);
        }
      });
    }, { rootMargin: '0px 0px -8% 0px', threshold: 0.08 });

    Array.prototype.forEach.call(revealTargets, function (el, i) {
      el.classList.add('reveal');
      el.style.transitionDelay = (Math.min(i, 6) * 45) + 'ms';
      io.observe(el);
    });
  }

  /* ---------- Current year ---------- */
  var year = document.getElementById('year');
  if (year) year.textContent = String(new Date().getFullYear());
})();

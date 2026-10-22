<script setup>
import { ref, nextTick, onMounted, onBeforeUnmount } from 'vue'
import { resume } from './data/resume.js'

const menuOpen = ref(false)
const navEl = ref(null)
const menuBtn = ref(null)
const year = new Date().getFullYear()

const phoneDigits = resume.phone.replace(/\D/g, '')
const tel = phoneDigits.length === 10 ? `+1${phoneDigits}` : phoneDigits

const foundYears = resume.work.flatMap((job) =>
  [...job.period.matchAll(/\d{4}/g)].map((match) => Number(match[0])),
)
const yearsActive = foundYears.length ? year - Math.min(...foundYears) : null

const place = resume.location.split(',')
const city = place[0].trim()
const region = (place.at(-1) || resume.location).trim()

const stats = [
  { value: String(resume.work.length).padStart(2, '0'), label: 'Roles', shape: 'circle' },
  ...(yearsActive == null
    ? []
    : [{ value: String(yearsActive).padStart(2, '0'), label: 'Years', shape: 'square' }]),
  { value: String(resume.skills.length).padStart(2, '0'), label: 'Skills', shape: 'diamond' },
  { value: region, label: city, shape: 'circle', compact: region.length > 3 },
]

const marks = ['circle', 'square', 'triangle']
const tones = ['red', 'blue', 'yellow']
const toneFill = { red: '#D02020', blue: '#1040C0', yellow: '#F0C020' }

function swatchClass(index) {
  if (index % 3 === 0) return ['circle', 'red']
  if (index % 3 === 1) return ['square', 'blue']
  return ['square', 'yellow', 'turned']
}

async function toggleMenu() {
  menuOpen.value = !menuOpen.value
  await nextTick()
  if (menuOpen.value) navEl.value?.querySelector('a')?.focus()
  else menuBtn.value?.focus()
}

function closeMenu() {
  menuOpen.value = false
}

function onKeydown(event) {
  if (event.key === 'Escape' && menuOpen.value) {
    menuOpen.value = false
    menuBtn.value?.focus()
  }
}

onMounted(() => document.addEventListener('keydown', onKeydown))
onBeforeUnmount(() => document.removeEventListener('keydown', onKeydown))
</script>

<template>
  <div class="site">
    <a class="skip" href="#content">Skip to content</a>

    <header class="header">
      <div class="flag" aria-hidden="true"><span></span><span></span><span></span></div>
      <div class="nav-inner">
        <a class="brand" href="#top" @click="closeMenu">
          <svg class="logo" viewBox="0 0 84 28" aria-hidden="true">
            <circle cx="14" cy="14" r="11" fill="#D02020" stroke="currentColor" stroke-width="2.5" />
            <rect x="32" y="3" width="22" height="22" fill="#1040C0" stroke="currentColor" stroke-width="2.5" />
            <polygon points="70,3 58,25 82,25" fill="#F0C020" stroke="currentColor" stroke-width="2.5" stroke-linejoin="round" />
          </svg>
          <span class="brand-name">{{ resume.name }}</span>
        </a>

        <button
          ref="menuBtn"
          class="menu-btn"
          :class="{ open: menuOpen }"
          type="button"
          :aria-expanded="menuOpen"
          aria-controls="site-nav"
          @click="toggleMenu"
        >
          <span class="sr-only">{{ menuOpen ? 'Close menu' : 'Open menu' }}</span>
          <span class="bars" aria-hidden="true"><span></span><span></span><span></span></span>
        </button>

        <nav id="site-nav" ref="navEl" class="site-nav" :class="{ open: menuOpen }" aria-label="Sections">
          <a href="#experience" @click="closeMenu">Experience</a>
          <a href="#education" @click="closeMenu">Education</a>
          <a href="#skills" @click="closeMenu">Skills</a>
          <a class="btn btn-red btn-sm" :href="`mailto:${resume.email}`">Email</a>
        </nav>
      </div>
    </header>

    <main id="content">
      <section class="hero" id="top">
        <div class="identity-wrap">
          <div class="identity">
            <p class="kicker">Data Scientist</p>
            <div class="identity-text">
              <h1 class="name">
                <span v-for="(part, i) in resume.name.split(' ')" :key="i">{{ part }}</span>
              </h1>
            </div>
            <ul class="meta">
              <li><a :href="`mailto:${resume.email}`">{{ resume.email }}</a></li>
              <li><a :href="`tel:${tel}`">{{ resume.phone }}</a></li>
              <li>{{ resume.location }}</li>
            </ul>
          </div>
        </div>

        <div class="hero-panel" aria-hidden="true">
          <div class="orb"></div>
          <div class="gem"><div class="gem-face"></div></div>
          <div class="plate">
            <svg viewBox="0 0 100 90" focusable="false">
              <polygon points="50,8 10,82 90,82" fill="#1040C0" stroke="#121212" stroke-width="5" stroke-linejoin="round" />
            </svg>
          </div>
        </div>
      </section>

      <ul class="stats" aria-label="Snapshot">
        <li v-for="stat in stats" :key="stat.label">
          <div class="fig-slot" :class="{ diamond: stat.shape === 'diamond' }">
            <p class="fig" :class="[stat.shape === 'diamond' ? null : stat.shape, { compact: stat.compact }]">
              <span>{{ stat.value }}</span>
            </p>
          </div>
          <p class="stat-label">{{ stat.label }}</p>
        </li>
      </ul>

      <section class="section experience" id="experience">
        <div class="wrap">
          <div class="section-head">
            <p class="kicker">Work</p>
            <h2 class="section-title">Experience</h2>
          </div>
          <div class="jobs">
            <article v-for="(job, i) in resume.work" :key="`${job.company}-${job.period}`" class="card job">
              <svg
                v-if="marks[i % 3] === 'triangle'"
                class="mark-tri"
                :class="{ turned: i % 3 === 2 }"
                viewBox="0 0 20 18"
                aria-hidden="true"
              >
                <polygon
                  points="10,1.6 1.4,16.6 18.6,16.6"
                  :fill="toneFill[tones[i % 3]]"
                  stroke="#121212"
                  stroke-width="1.7"
                  stroke-linejoin="round"
                />
              </svg>
              <span
                v-else
                class="mark"
                :class="[marks[i % 3], tones[i % 3]]"
                aria-hidden="true"
              ></span>

              <div class="job-head">
                <span class="index">{{ String(i + 1).padStart(2, '0') }}</span>
                <div class="job-title">
                  <h3 class="company">{{ job.company }}</h3>
                  <p class="role">
                    <span class="role-name">{{ job.role }}</span>
                    <span class="where">{{ job.location }}</span>
                  </p>
                </div>
                <p class="period">{{ job.period }}</p>
              </div>
              <ul class="bullets">
                <li v-for="(bullet, j) in job.bullets" :key="j">{{ bullet }}</li>
              </ul>
            </article>
          </div>
        </div>
      </section>

      <section class="section band band-red" id="education">
        <div class="band-deco band-deco-circle" aria-hidden="true"></div>
        <div class="wrap">
          <div class="section-head">
            <p class="kicker">Study</p>
            <h2 class="section-title">Education</h2>
          </div>
          <div class="jobs">
            <article v-for="edu in resume.education" :key="edu.school" class="card edu">
              <span class="mark circle yellow" aria-hidden="true"></span>
              <h3 class="school">{{ edu.school }}</h3>
              <p class="degree">{{ edu.degree }}</p>
              <p class="where edu-where">{{ edu.location }}</p>
            </article>
          </div>
        </div>
      </section>

      <section class="section band band-blue" id="skills">
        <div class="band-deco band-deco-gem" aria-hidden="true"></div>
        <div class="wrap">
          <div class="section-head">
            <p class="kicker">Toolbox</p>
            <h2 class="section-title">Skills</h2>
          </div>
          <div class="skills">
            <span v-for="(skill, i) in resume.skills" :key="skill" class="skill">
              <span class="swatch" :class="swatchClass(i)" aria-hidden="true"></span>
              {{ skill }}
            </span>
          </div>
        </div>
      </section>

      <section class="section cta" id="contact">
        <div class="cta-orb" aria-hidden="true"></div>
        <div class="cta-gem" aria-hidden="true"><div></div></div>
        <div class="wrap cta-inner">
          <p class="kicker">Contact</p>
          <h2 class="section-title">Start a conversation</h2>
          <p class="cta-mail"><a :href="`mailto:${resume.email}`">{{ resume.email }}</a></p>
          <p class="cta-place">{{ resume.location }}</p>
          <div class="actions">
            <a class="btn btn-red" :href="`mailto:${resume.email}`">
              Email
              <svg viewBox="0 0 24 24" width="18" height="18" aria-hidden="true" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="square" stroke-linejoin="miter">
                <path d="M5 12h14M13 6l6 6-6 6" />
              </svg>
            </a>
            <a class="btn btn-blue" :href="`tel:${tel}`">Call</a>
          </div>
        </div>
      </section>
    </main>

    <footer class="footer">
      <div class="wrap footer-inner">
        <a class="brand" href="#top">
          <svg class="logo" viewBox="0 0 84 28" aria-hidden="true">
            <circle cx="14" cy="14" r="11" fill="#D02020" stroke="currentColor" stroke-width="2.5" />
            <rect x="32" y="3" width="22" height="22" fill="#1040C0" stroke="currentColor" stroke-width="2.5" />
            <polygon points="70,3 58,25 82,25" fill="#F0C020" stroke="currentColor" stroke-width="2.5" stroke-linejoin="round" />
          </svg>
          <span class="brand-name">{{ resume.name }}</span>
        </a>
        <p class="footer-links">
          <a :href="`mailto:${resume.email}`">{{ resume.email }}</a>
          <a :href="`tel:${tel}`">{{ resume.phone }}</a>
        </p>
        <p class="footer-year">© {{ year }}</p>
      </div>
    </footer>
  </div>
</template>

<style>
:root {
  --bg: #f0f0f0;
  --fg: #121212;
  --red: #d02020;
  --blue: #1040c0;
  --yellow: #f0c020;
  --muted: #e0e0e0;
  --font: "Outfit", system-ui, sans-serif;
  --bw: 2px;
  --rule: 4px;
  --sh: 3px;
  --sh-lg: 4px;
  --ease: 200ms ease-out;
}

@media (min-width: 1024px) {
  :root {
    --bw: 4px;
    --sh: 4px;
    --sh-lg: 8px;
  }
}

* { box-sizing: border-box; }

html { scroll-behavior: smooth; background: var(--bg); }

body {
  margin: 0;
  background-color: var(--bg);
  background-image: radial-gradient(#d5d5d5 1.25px, transparent 1.25px);
  background-size: 20px 20px;
  color: var(--fg);
  font-family: var(--font);
  font-weight: 500;
  font-size: 1rem;
  line-height: 1.65;
  -webkit-font-smoothing: antialiased;
}

@media (min-width: 1024px) {
  body { font-size: 1.125rem; }
}

h1, h2, h3, p, ul { margin: 0; padding: 0; }
ul { list-style: none; }
a { color: inherit; }

::selection { background: var(--yellow); color: var(--fg); }

:focus-visible {
  outline: 2px solid var(--fg);
  outline-offset: 3px;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

.skip {
  position: absolute;
  left: 1rem;
  top: -4rem;
  z-index: 80;
  background: var(--yellow);
  color: var(--fg);
  border: 2px solid var(--fg);
  padding: 0.5rem 0.75rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  text-decoration: none;
}

.skip:focus { top: 0.75rem; }

.site { min-height: 100vh; }

.wrap,
.nav-inner {
  width: min(80rem, 100%);
  margin-inline: auto;
  padding-inline: 1rem;
}

@media (min-width: 640px) {
  .wrap,
  .nav-inner { padding-inline: 1.5rem; }
}

@media (min-width: 1024px) {
  .wrap,
  .nav-inner { padding-inline: 2rem; }
}

.kicker {
  margin-bottom: 0.75rem;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.kicker::before {
  content: "";
  display: inline-block;
  width: 0.65rem;
  height: 0.65rem;
  margin-right: 0.55rem;
  background: var(--red);
  border: 2px solid var(--fg);
  vertical-align: -1px;
}

.section-title {
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: -0.045em;
  line-height: 0.9;
  font-size: 2.25rem;
  text-wrap: balance;
}

.section-title::after {
  content: "";
  display: block;
  width: 4.5rem;
  height: 0.7rem;
  margin-top: 0.85rem;
  background: var(--red);
  border: 2px solid var(--fg);
}

@media (min-width: 640px) {
  .section-title { font-size: 3rem; }
}

@media (min-width: 1024px) {
  .section-title { font-size: 4rem; }
}

.section {
  position: relative;
  padding-block: 3rem;
  border-bottom: var(--rule) solid var(--fg);
  scroll-margin-top: 5.5rem;
}

@media (min-width: 640px) {
  .section { padding-block: 4rem; }
}

@media (min-width: 1024px) {
  .section { padding-block: 6rem; }
}

.section-head { margin-bottom: 2rem; }

@media (min-width: 1024px) {
  .section-head { margin-bottom: 2.75rem; }
}

.header {
  position: sticky;
  top: 0;
  z-index: 40;
  background: var(--bg);
  border-bottom: var(--rule) solid var(--fg);
}

.flag {
  display: grid;
  grid-template-columns: 1.4fr 0.7fr 1fr;
  height: 0.85rem;
  border-bottom: var(--rule) solid var(--fg);
}

.flag span:nth-child(1) { background: var(--red); }
.flag span:nth-child(2) { background: var(--blue); }
.flag span:nth-child(3) { background: var(--yellow); }

.nav-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  min-height: 4.25rem;
}

.brand {
  display: flex;
  align-items: center;
  gap: 0.65rem;
  min-width: 0;
  color: inherit;
  text-decoration: none;
}

.logo {
  width: 4.6rem;
  height: auto;
  flex: none;
  color: var(--fg);
}

.brand-name {
  font-weight: 900;
  letter-spacing: -0.04em;
  text-transform: uppercase;
  line-height: 0.95;
  font-size: 0.92rem;
}

.menu-btn {
  width: 2.75rem;
  height: 2.75rem;
  display: inline-grid;
  place-items: center;
  padding: 0;
  background: #fff;
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh) var(--sh) 0 0 var(--fg);
  cursor: pointer;
}

.menu-btn:active {
  transform: translate(2px, 2px);
  box-shadow: none;
}

.bars { display: grid; gap: 5px; width: 18px; }

.bars span {
  display: block;
  height: 2px;
  background: var(--fg);
  transition: transform var(--ease), opacity var(--ease);
}

.menu-btn.open .bars span:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.menu-btn.open .bars span:nth-child(2) { opacity: 0; }
.menu-btn.open .bars span:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }

.site-nav { display: none; }

.site-nav.open {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  gap: 0.35rem;
  position: absolute;
  left: 0;
  right: 0;
  top: 100%;
  z-index: 1;
  padding: 0.5rem 1rem 1rem;
  background: #fff;
  border-bottom: var(--rule) solid var(--fg);
  box-shadow: 6px 6px 0 0 var(--fg);
}

.site-nav a:not(.btn) {
  padding: 0.85rem 0.15rem;
  border-bottom: 2px solid var(--fg);
  color: var(--fg);
  text-decoration: none;
  font-size: 0.95rem;
  font-weight: 700;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  transition: color var(--ease);
}

.site-nav a:not(.btn):hover { color: var(--red); }

@media (min-width: 768px) {
  .menu-btn { display: none; }

  .site-nav,
  .site-nav.open {
    display: flex;
    position: static;
    flex-direction: row;
    align-items: center;
    gap: 1.35rem;
    padding: 0;
    background: transparent;
    border: 0;
    box-shadow: none;
  }

  .site-nav a:not(.btn) {
    padding: 0.35rem 0;
    border-bottom: 0;
    font-size: 0.75rem;
    letter-spacing: 0.16em;
  }
}

.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  padding: 0.85rem 1.15rem;
  background: #fff;
  color: var(--fg);
  border: var(--bw) solid var(--fg);
  border-radius: 0;
  box-shadow: var(--sh) var(--sh) 0 0 var(--fg);
  font-family: inherit;
  font-weight: 700;
  font-size: 0.78rem;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  text-decoration: none;
  cursor: pointer;
  transition: transform var(--ease), box-shadow var(--ease), background var(--ease);
}

.btn-sm { padding: 0.55rem 0.8rem; }

.btn-red { background: var(--red); color: #fff; }
.btn-blue { background: var(--blue); color: #fff; }
.btn-red:hover { background: color-mix(in srgb, var(--red) 88%, black); }
.btn-blue:hover { background: color-mix(in srgb, var(--blue) 88%, black); }

.btn:active {
  transform: translate(2px, 2px);
  box-shadow: none;
}

@media (max-width: 767px) {
  .site-nav .btn { width: 100%; margin-top: 0.4rem; }
}

.hero {
  scroll-margin-top: 5.5rem;
  border-bottom: 0;
}

.identity-wrap {
  position: relative;
  overflow: hidden;
  padding: 2.5rem 1rem 1.75rem;
}

.identity-wrap::before {
  content: "";
  position: absolute;
  width: 280px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--red);
  opacity: 0.14;
  left: -100px;
  bottom: -120px;
  pointer-events: none;
}

.identity {
  position: relative;
  z-index: 1;
  width: 100%;
  min-width: 0;
}

.identity-text { container-type: inline-size; }

.name {
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: -0.05em;
  line-height: 0.84;
  font-size: clamp(2.45rem, 12.6cqi, 6rem);
}

.name span { display: block; }

.meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  margin-top: 1.5rem;
}

.meta li {
  background: #fff;
  border: 2px solid var(--fg);
  box-shadow: 3px 3px 0 0 var(--fg);
  padding: 0.45rem 0.7rem;
  font-size: 0.92rem;
  font-weight: 700;
  overflow-wrap: anywhere;
}

.meta a { text-decoration: none; transition: color var(--ease); }
.meta a:hover { color: var(--red); }

.hero-panel {
  position: relative;
  min-height: 22rem;
  overflow: hidden;
  background-color: var(--blue);
  background-image: radial-gradient(#fff 2px, transparent 2px);
  background-size: 20px 20px;
  border-top: var(--rule) solid var(--fg);
}

.orb,
.gem,
.plate {
  position: absolute;
}

.orb {
  z-index: 1;
  width: min(58%, 280px);
  aspect-ratio: 1;
  top: 9%;
  left: 7%;
  border-radius: 50%;
  background: var(--yellow);
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh-lg) var(--sh-lg) 0 0 var(--fg);
}

.gem {
  z-index: 2;
  width: min(40%, 190px);
  aspect-ratio: 1;
  top: 11%;
  right: 14%;
  filter: drop-shadow(var(--sh-lg) var(--sh-lg) 0 var(--fg));
}

.gem-face {
  width: 100%;
  height: 100%;
  background: var(--red);
  border: var(--bw) solid var(--fg);
  transform: rotate(45deg);
}

.plate {
  z-index: 3;
  width: min(48%, 240px);
  aspect-ratio: 1;
  left: 26%;
  bottom: 8%;
  display: grid;
  place-items: center;
  background: #fff;
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh-lg) var(--sh-lg) 0 0 var(--fg);
}

.plate svg { width: 58%; height: auto; }

@media (min-width: 640px) {
  .identity-wrap { padding: 3.25rem 1.5rem 2rem; }
}

@media (min-width: 1024px) {
  .hero {
    display: grid;
    grid-template-columns: minmax(0, 1.08fr) minmax(300px, 0.92fr);
    align-items: stretch;
  }

  .identity-wrap {
    display: flex;
    align-items: center;
    min-height: 36rem;
    padding: 4rem 2.75rem 4rem max(2rem, calc((100vw - 80rem) / 2 + 2rem));
  }

  .hero-panel {
    min-height: 36rem;
    border-top: 0;
    border-left: var(--rule) solid var(--fg);
  }
}

.stats {
  display: grid;
  grid-template-columns: 1fr;
  background: var(--yellow);
  border-bottom: var(--rule) solid var(--fg);
}

.stats li {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 1rem;
  padding: 1rem 1.25rem;
  border-top: var(--rule) solid var(--fg);
}

.fig {
  display: grid;
  place-items: center;
  width: 4.75rem;
  height: 4.75rem;
  margin: 0;
  background: #fff;
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh) var(--sh) 0 0 var(--fg);
  font-weight: 900;
  font-size: 1.55rem;
  line-height: 1;
  letter-spacing: -0.04em;
}

.fig.circle { border-radius: 50%; }
.fig.compact { font-size: 0.95rem; text-align: center; padding: 0.2rem; }

.fig-slot.diamond {
  width: 4.75rem;
  height: 4.75rem;
  display: grid;
  place-items: center;
  filter: drop-shadow(var(--sh) var(--sh) 0 var(--fg));
}

.fig-slot.diamond .fig {
  width: 3.35rem;
  height: 3.35rem;
  box-shadow: none;
  transform: rotate(45deg);
}

.fig-slot.diamond .fig span { transform: rotate(-45deg); }

.stat-label {
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

@media (min-width: 640px) {
  .stats { grid-template-columns: 1fr 1fr; }
  .stats li {
    flex-direction: column;
    align-items: flex-start;
    justify-content: space-between;
    min-height: 10.5rem;
    padding: 1.35rem 1.25rem;
  }
  .stats li:nth-child(odd) { border-right: var(--rule) solid var(--fg); }
}

@media (min-width: 1024px) {
  .stats { grid-template-columns: repeat(4, 1fr); }
  .stats li { border-right: var(--rule) solid var(--fg); }
  .stats li:last-child { border-right: 0; }
}

.card {
  position: relative;
  background: #fff;
  color: var(--fg);
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh-lg) var(--sh-lg) 0 0 var(--fg);
  padding: 1.2rem;
}

@media (min-width: 768px) {
  .card { padding: 1.4rem 1.5rem 1.3rem; }
}

.jobs { display: flex; flex-direction: column; gap: 1.75rem; }

.job { transition: transform var(--ease); }

.mark {
  position: absolute;
  top: -0.45rem;
  right: -0.45rem;
  z-index: 1;
  width: 0.9rem;
  height: 0.9rem;
  border: 2px solid var(--fg);
}

.mark.circle { border-radius: 50%; }
.mark.red { background: var(--red); }
.mark.blue { background: var(--blue); }
.mark.yellow { background: var(--yellow); }

.mark-tri {
  position: absolute;
  top: -0.45rem;
  right: -0.45rem;
  z-index: 1;
  width: 1.05rem;
  height: auto;
}

.mark-tri.turned { transform: rotate(45deg); }

.job-head {
  display: grid;
  grid-template-columns: auto minmax(0, 1fr);
  gap: 0.75rem 0.9rem;
  align-items: start;
}

.index {
  display: grid;
  place-items: center;
  width: 2.75rem;
  height: 2.75rem;
  background: var(--yellow);
  border: var(--bw) solid var(--fg);
  box-shadow: var(--sh) var(--sh) 0 0 var(--fg);
  font-weight: 900;
  font-size: 0.95rem;
  letter-spacing: -0.04em;
}

.company {
  font-size: 1.3rem;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: -0.03em;
  line-height: 0.95;
}

.role { margin-top: 0.35rem; font-size: 1rem; }
.role-name { font-weight: 700; }

.where::before {
  content: "·";
  margin: 0 0.35rem;
}

.period {
  grid-column: 2;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.bullets {
  max-width: 68ch;
  margin-top: 1rem;
  padding-top: 0.9rem;
  border-top: var(--bw) solid var(--fg);
}

.bullets li {
  position: relative;
  padding-left: 1.05rem;
  margin-bottom: 0.55rem;
}

.bullets li:last-child { margin-bottom: 0; }

.bullets li::before {
  content: "";
  position: absolute;
  left: 0;
  top: 0.55em;
  width: 0.45rem;
  height: 0.45rem;
  background: var(--fg);
}

@media (min-width: 720px) {
  .company { font-size: 1.7rem; }

  .job-head { grid-template-columns: auto minmax(0, 1fr) auto; }

  .period {
    grid-column: auto;
    grid-row: 1;
    align-self: center;
    white-space: nowrap;
  }
}

@media (hover: hover) {
  .job:hover { transform: translateY(-4px); }
}

.band { overflow: hidden; }
.band-red { background: var(--red); color: #fff; }
.band-blue { background: var(--blue); color: #fff; }

.band .kicker::before,
.band .section-title::after { background: var(--yellow); }

.band-deco {
  position: absolute;
  pointer-events: none;
  opacity: 0.2;
}

.band-deco-circle {
  width: 240px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--yellow);
  right: -70px;
  top: -80px;
}

.band-deco-gem {
  width: 200px;
  aspect-ratio: 1;
  background: var(--yellow);
  right: -50px;
  bottom: -70px;
  transform: rotate(45deg);
}

.school {
  padding-right: 1.2rem;
  font-size: clamp(1.45rem, 3vw, 2.2rem);
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: -0.03em;
  line-height: 0.95;
}

.degree,
.edu-where { margin-top: 0.4rem; }
.edu { max-width: 40rem; }
.edu-where::before { content: none; }

.skills { display: flex; flex-wrap: wrap; gap: 0.75rem; }

.skill {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: #fff;
  color: var(--fg);
  border: 2px solid var(--fg);
  box-shadow: 3px 3px 0 0 var(--fg);
  padding: 0.45rem 0.7rem;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  transition: transform var(--ease);
}

.swatch {
  width: 0.7rem;
  height: 0.7rem;
  border: 2px solid var(--fg);
  flex: none;
}

.swatch.circle { border-radius: 50%; }
.swatch.red { background: var(--red); }
.swatch.blue { background: var(--blue); }
.swatch.yellow { background: var(--yellow); }
.swatch.turned { transform: rotate(45deg); }

.cta {
  overflow: hidden;
  background: var(--yellow);
}

.cta-orb,
.cta-gem {
  position: absolute;
  pointer-events: none;
  border: var(--bw) solid var(--fg);
}

.cta-orb {
  width: min(52vw, 280px);
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--blue);
  top: -130px;
  right: -120px;
  box-shadow: var(--sh-lg) var(--sh-lg) 0 0 var(--fg);
}

.cta-gem {
  width: min(34vw, 160px);
  aspect-ratio: 1;
  right: -60px;
  bottom: -50px;
  border: 0;
  background: none;
  filter: drop-shadow(var(--sh-lg) var(--sh-lg) 0 var(--fg));
}

.cta-gem div {
  width: 100%;
  height: 100%;
  background: var(--red);
  border: var(--bw) solid var(--fg);
  transform: rotate(45deg);
}

.cta-inner { position: relative; z-index: 1; }
.cta .section-title::after { background: var(--fg); }

@media (min-width: 1024px) {
  .cta .section-title { max-width: 12ch; }
}

.cta-mail {
  margin-top: 1.25rem;
  font-size: clamp(1.15rem, 2vw, 1.45rem);
  font-weight: 700;
}

.cta-mail a {
  color: inherit;
  text-decoration: none;
  border-bottom: 3px solid var(--fg);
  overflow-wrap: anywhere;
  transition: color var(--ease), border-color var(--ease);
}

.cta-mail a:hover { color: var(--red); border-color: var(--red); }

.cta-place {
  margin-top: 0.4rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  font-size: 0.8rem;
}

.actions {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  margin-top: 1.5rem;
}

.footer {
  background: var(--fg);
  color: #fff;
  padding-block: 1.75rem;
}

.footer .logo { color: #fff; }

.footer-inner {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  gap: 1rem 1.5rem;
}

.footer-links {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem 1.25rem;
  font-weight: 700;
}

.footer-links a {
  color: #fff;
  text-underline-offset: 0.2em;
}

.footer a:hover { color: var(--yellow); }

.footer-year {
  font-size: 0.8rem;
  font-weight: 700;
  letter-spacing: 0.14em;
}

.footer :focus-visible,
.band :focus-visible { outline-color: #fff; }

@media (hover: hover) {
  .skill:hover { transform: translateY(-2px); }
}

@media (prefers-reduced-motion: reduce) {
  html { scroll-behavior: auto; }
  .btn,
  .job,
  .skill,
  .bars span,
  .meta a,
  .cta-mail a { transition: none; }
  .job:hover,
  .skill:hover { transform: none; }
}

@media print {
  .header,
  .hero-panel,
  .mark,
  .mark-tri,
  .band-deco,
  .cta-orb,
  .cta-gem,
  .identity-wrap::before { display: none !important; }

  body { background: #fff; }

  .stats,
  .band-red,
  .band-blue,
  .cta,
  .footer {
    background: #fff !important;
    color: #000 !important;
  }

  .section-title,
  .kicker,
  .footer,
  .footer a { color: #000 !important; }

  .card,
  .skill,
  .btn,
  .meta li,
  .fig,
  .index { box-shadow: none; }

  .job { break-inside: avoid; }
}
</style>

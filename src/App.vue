<script setup>
import { resume } from './data/resume.js'
</script>

<template>
  <div class="site">
    <header class="hero">
      <h1 class="name">{{ resume.name }}</h1>
      <p class="title">{{ resume.title }}</p>
      <div class="contact">
        <a :href="`mailto:${resume.email}`">{{ resume.email }}</a>
        <span class="sep">·</span>
        <a :href="`tel:${resume.phone.replace(/\D/g, '')}`">{{ resume.phone }}</a>
        <span class="sep">·</span>
        <span>{{ resume.location }}</span>
      </div>
    </header>

    <main class="main">
      <section class="block">
        <h2 class="section-title">Experience</h2>
        <div class="jobs">
          <article v-for="(job, i) in resume.work" :key="i" class="job">
            <div class="job-head">
              <div class="job-role-loc">
                <h3 class="company">{{ job.company }}</h3>
                <p class="role">{{ job.role }} <span class="location">{{ job.location }}</span></p>
              </div>
              <span class="period">{{ job.period }}</span>
            </div>
            <ul class="bullets">
              <li v-for="(bullet, j) in job.bullets" :key="j">{{ bullet }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section class="block">
        <h2 class="section-title">Education</h2>
        <div class="education">
          <article v-for="(edu, i) in resume.education" :key="i" class="edu-item">
            <h3 class="school">{{ edu.school }}</h3>
            <p class="degree">{{ edu.degree }} <span class="location">{{ edu.location }}</span></p>
          </article>
        </div>
      </section>

      <section class="block">
        <h2 class="section-title">Skills & Technologies</h2>
        <div class="skills">
          <span v-for="(skill, i) in resume.skills" :key="i" class="skill-tag">{{ skill }}</span>
        </div>
      </section>
    </main>

    <footer class="footer">
      <p>Connor Kenneally · {{ new Date().getFullYear() }}</p>
    </footer>
  </div>
</template>

<style>
:root {
  --bg: #0d0d0d;
  --surface: #161616;
  --text: #e8e6e3;
  --muted: #8b8685;
  --accent: #7dd3b0;
  --accent-dim: #5a9d82;
  --font-sans: 'DM Sans', system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', monospace;
  --radius: 8px;
  --space: 1.5rem;
}

* {
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  margin: 0;
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-sans);
  font-size: 1rem;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

.site {
  min-height: 100vh;
  max-width: 720px;
  margin: 0 auto;
  padding: 0 1.25rem 3rem;
}

.hero {
  padding: 4rem 0 3rem;
  text-align: center;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
}

.name {
  margin: 0 0 0.35em;
  font-size: clamp(1.75rem, 5vw, 2.25rem);
  font-weight: 700;
  letter-spacing: -0.02em;
}

.title {
  margin: 0 0 1rem;
  font-size: 1.1rem;
  color: var(--accent);
  font-weight: 500;
}

.contact {
  font-size: 0.95rem;
  color: var(--muted);
}

.contact a {
  color: var(--text);
  text-decoration: none;
}

.contact a:hover {
  color: var(--accent);
}

.sep {
  margin: 0 0.5em;
  opacity: 0.6;
}

.main {
  padding-top: 2.5rem;
}

.block {
  margin-bottom: 2.5rem;
}

.section-title {
  margin: 0 0 1.25rem;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.12em;
  color: var(--muted);
  font-family: var(--font-mono);
}

.jobs {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.job {
  padding: 1.25rem;
  background: var(--surface);
  border-radius: var(--radius);
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.job-head {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: flex-start;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.job-role-loc {
  flex: 1;
  min-width: 0;
}

.company {
  margin: 0 0 0.2em;
  font-size: 1.05rem;
  font-weight: 600;
}

.role {
  margin: 0;
  font-size: 0.9rem;
  color: var(--muted);
}

.role .location {
  color: var(--muted);
  opacity: 0.9;
}

.period {
  font-size: 0.85rem;
  color: var(--muted);
  font-family: var(--font-mono);
  white-space: nowrap;
}

.bullets {
  margin: 0;
  padding-left: 1.25rem;
}

.bullets li {
  margin-bottom: 0.4em;
  font-size: 0.95rem;
  color: var(--text);
}

.bullets li:last-child {
  margin-bottom: 0;
}

.education {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.edu-item {
  padding: 1rem 1.25rem;
  background: var(--surface);
  border-radius: var(--radius);
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.school {
  margin: 0 0 0.25em;
  font-size: 1rem;
  font-weight: 600;
}

.degree {
  margin: 0;
  font-size: 0.9rem;
  color: var(--muted);
}

.skills {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.skill-tag {
  display: inline-block;
  padding: 0.35rem 0.75rem;
  font-size: 0.8rem;
  font-family: var(--font-mono);
  background: var(--surface);
  border: 1px solid rgba(255, 255, 255, 0.06);
  border-radius: 6px;
  color: var(--muted);
}

.footer {
  margin-top: 3rem;
  padding-top: 1.5rem;
  border-top: 1px solid rgba(255, 255, 255, 0.06);
  text-align: center;
  font-size: 0.85rem;
  color: var(--muted);
}

.footer p {
  margin: 0;
}
</style>

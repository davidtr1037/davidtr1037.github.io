---
layout: default
title: About
permalink: /about/
portfolio: true
---

<div class="portfolio">
  <section class="portfolio-about" id="about" aria-labelledby="about-heading">
    <div class="portfolio-about-copy">
      <h1 class="portfolio-heading" id="about-heading">About</h1>
      <p>I am a Research Fellow at the National University of Singapore, working with Prof. Abhik Roychoudhury. Previously, I was a postdoctoral researcher at the Technion with Prof. Shachar Itzhaky and a Ph.D. student at Tel Aviv University with Prof. Noam Rinetzky.</p>
      <p>My research spans symbolic execution, verification, proof assistants, and static analysis. I also have industrial experience in malware analysis, reverse engineering, and vulnerability research.</p>
    </div>
    <figure class="portfolio-portrait">
      <img src="{{ '/photo_2.jpg' | relative_url }}" alt="Portrait of David Trabish" width="360" height="420">
    </figure>
  </section>

  {% assign publications_page = site.pages | where: "path", "publications.md" | first %}
  <section class="portfolio-section" id="publications" aria-labelledby="publications-heading">
    <div class="portfolio-section-heading"><h2 id="publications-heading">Publications</h2></div>
    <div class="portfolio-content portfolio-publications">{{ publications_page.content | markdownify }}</div>
  </section>

  {% assign projects_page = site.pages | where: "path", "projects.md" | first %}
  <section class="portfolio-section" id="projects" aria-labelledby="projects-heading">
    <div class="portfolio-section-heading"><h2 id="projects-heading">Open Source Projects</h2></div>
    <div class="portfolio-content portfolio-projects">{{ projects_page.content | markdownify }}</div>
  </section>

  {% assign service_page = site.pages | where: "path", "service.md" | first %}
  <section class="portfolio-section" id="service" aria-labelledby="service-heading">
    <div class="portfolio-section-heading"><h2 id="service-heading">Service</h2></div>
    <div class="portfolio-content portfolio-service">{{ service_page.content | markdownify }}</div>
  </section>

  <footer class="portfolio-contact">
    <strong>Contact</strong>
    <a href="mailto:davidtr1037@gmail.com">davidtr1037@gmail.com</a>
    <span class="portfolio-contact-links">
      <a href="https://github.com/davidtr1037">GitHub</a>
      <a href="https://www.linkedin.com/in/david-trabish-327ab714b/">LinkedIn</a>
      <a href="https://scholar.google.com/citations?user=1r_apAoAAAAJ">Google Scholar</a>
    </span>
  </footer>
</div>

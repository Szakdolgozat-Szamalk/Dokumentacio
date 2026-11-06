---
title: Notes
nav_order: 4
ref: notes
permalink: /en/notes/
---

# Notes

<div class="notes-container">
  <!-- Rádiógombok a vezérléshez (rejtve) -->
  <input type="radio" name="note-tab" id="tab-graphql" checked>
  <input type="radio" name="note-tab" id="tab-pwa">
  <input type="radio" name="note-tab" id="tab-testing-f">
  <input type="radio" name="note-tab" id="tab-testing-b">
  <input type="radio" name="note-tab" id="tab-personastudio">

  <div class="notes-tabs">
    <label for="tab-graphql" class="note-btn">GraphQL</label>
    <label for="tab-pwa" class="note-btn">PWA</label>
    <label for="tab-testing-f" class="note-btn">Frontend Testing</label>
    <label for="tab-testing-b" class="note-btn">Backend Testing</label>
    <label for="tab-personastudio" class="note-btn">Persona Studio</label>
  </div>

  <div class="notes-content">
    
    <div class="note-pane" id="pane-graphql">
      {% capture note_graphql %}{% include notes/graphql-en.md %}{% endcapture %}
      {{ note_graphql | markdownify }}
    </div>
    
    <div class="note-pane" id="pane-pwa">
      {% capture note_pwa %}{% include notes/pwa-en.md %}{% endcapture %}
      {{ note_pwa | markdownify }}
    </div>
    
    <div class="note-pane" id="pane-testing-f">
      {% capture note_testing-f %}{% include notes/testing-f-en.md %}{% endcapture %}
      {{ note_testing-f | markdownify }}
    </div>
    
    <div class="note-pane" id="pane-testing-b">
      {% capture note_testing-b %}{% include notes/testing-b-en.md %}{% endcapture %}
      {{ note_testing-b | markdownify }}
    </div>

    <div class="note-pane" id="pane-personastudio">
      {% capture note_personastudio %}{% include notes/personastudio-en.md %}{% endcapture %}
      {{ note_personastudio | markdownify }}
    </div>

  </div>
</div>
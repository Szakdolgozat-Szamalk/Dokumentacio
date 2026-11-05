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
  <input type="radio" name="note-tab" id="tab-testing">
  <input type="radio" name="note-tab" id="tab-personastudio">

  <div class="notes-tabs">
    <label for="tab-graphql" class="note-btn">GraphQL</label>
    <label for="tab-pwa" class="note-btn">PWA</label>
    <label for="tab-testing" class="note-btn">Testing</label>
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
    
    <div class="note-pane" id="pane-testing">
      {% capture note_testing %}{% include notes/testing-en.md %}{% endcapture %}
      {{ note_testing | markdownify }}
    </div>

    <div class="note-pane" id="pane-personastudio">
      {% capture note_personastudio %}{% include notes/personastudio-en.md %}{% endcapture %}
      {{ note_personastudio | markdownify }}
    </div>

  </div>
</div>
<script setup>
import { computed } from 'vue'
import { useNav } from '@slidev/client'
const nav = useNav()
// Sections come from the deck headmatter: `rail:` is a list of { label, start }.
const sections = computed(() => {
  const rail = nav.slides?.value?.[0]?.meta?.slide?.frontmatter?.rail
  return Array.isArray(rail) && rail.length ? rail : [{ label: 'Start', start: 1 }]
})
const active = computed(() => sections.value.reduce((found, s, i) => nav.currentPage.value >= s.start ? i : found, 0))
</script>
<template>
  <nav class="module-nav" aria-label="Sections">
    <div class="nav-caption">OUR PATH</div>
    <button v-for="(section, i) in sections" :key="section.start"
      :class="{ past: i < active, current: i === active, future: i > active }"
      :aria-current="i === active ? 'step' : undefined" @click="nav.go(section.start)">
      <span class="nav-dot">{{ i < active ? '✓' : i === active ? '●' : '○' }}</span>
      <span>{{ section.label }}</span>
    </button>
  </nav>
</template>
<style>
.module-nav{position:absolute;left:26px;top:150px;width:160px;font-family:"Source Code Pro","Cascadia Mono",Consolas,monospace;z-index:30}
.module-nav .nav-caption{font-size:10px;letter-spacing:2px;color:#6f7886;margin-bottom:18px}
.module-nav button{display:flex;align-items:center;gap:9px;width:100%;text-align:left;background:transparent;border:0;border-left:2px solid transparent;padding:11px 8px;font:inherit;font-size:12px;line-height:1.4;cursor:pointer}
.module-nav button.past{color:#a3abb8}
.module-nav button.future{color:#6f7886}
.module-nav button.current{color:#48cae4;background:rgba(0,180,216,.1);border-left-color:#00b4d8}
.module-nav button:hover,.module-nav button:focus-visible{color:#fff;outline:1px solid #00b4d8}
.module-nav .nav-dot{flex-shrink:0;font-size:11px}
</style>

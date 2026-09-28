<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  visible: Boolean,
  posX: Number,
  posY: Number,
});

const emit = defineEmits(["close"]);

const activeStates = ref({});

const buttons = [
  { cmd: "bold", label: "B", class: "b" },
  { cmd: "italic", label: "I", class: "i" },
  { cmd: "underline", label: "U", class: "u" },
  { cmd: "strikeThrough", label: "S", class: "s" },
  { cmd: "insertUnorderedList", label: "• List" },
  { cmd: "insertOrderedList", label: "1. List" },
  { cmd: "formatBlock", value: "<h2>", label: "H2" },
  { cmd: "formatBlock", value: "<h3>", label: "H3" },
  { cmd: "formatBlock", value: "<p>", label: "P" },
];

const colors = ["#000", "#ef4444", "#f59e0b", "#10b981", "#3b82f6", "#7c3aed", "#ec4899"];

function exec(cmd, value = null) {
  document.execCommand(cmd, false, value);
  updateStates();
}

function addLink() {
  const url = prompt("URL do link:");
  if (url) document.execCommand("createLink", false, url);
}

function setColor(c) {
  document.execCommand("foreColor", false, c);
}

function removeFormat() {
  document.execCommand("removeFormat");
  document.execCommand("formatBlock", false, "<p>");
}

function updateStates() {
  activeStates.value = {
    bold: document.queryCommandState("bold"),
    italic: document.queryCommandState("italic"),
    underline: document.queryCommandState("underline"),
    strikeThrough: document.queryCommandState("strikeThrough"),
  };
}

function onKey(e) {
  if (e.key === "Escape") emit("close");
}

onMounted(() => {
  window.addEventListener("keydown", onKey);
  updateStates();
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", onKey);
});
</script>

<template>
  <div
    v-if="visible"
    class="toolbar"
    :style="{ left: posX + 'px', top: posY + 'px' }"
    @mousedown.prevent
  >
    <button
      v-for="b in buttons"
      :key="b.label"
      :class="['tb-btn', b.class, { active: activeStates[b.cmd] }]"
      @click="exec(b.cmd, b.value)"
    >{{ b.label }}</button>
    <button class="tb-btn" @click="addLink">🔗</button>
    <div class="color-row">
      <button
        v-for="c in colors"
        :key="c"
        :style="{ background: c }"
        class="color-swatch"
        @click="setColor(c)"
      ></button>
    </div>
    <button class="tb-btn" @click="removeFormat">✕fmt</button>
  </div>
</template>

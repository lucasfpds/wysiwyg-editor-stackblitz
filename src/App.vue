<script setup>
import { ref, onMounted } from "vue";
import FloatingToolbar from "./components/FloatingToolbar.vue";

const editor = ref(null);
const toolbarVisible = ref(false);
const toolbarX = ref(0);
const toolbarY = ref(0);

const INITIAL = "<h2>Bem-vindo ao editor</h2><p>Selecione este texto para ver a <b>toolbar flutuante</b>. Tente <i>negrito</i>, <u>sublinhado</u> ou arraste uma imagem aqui.</p>";

function checkSelection() {
  const sel = window.getSelection();
  if (!sel || sel.isCollapsed || !sel.toString().trim()) {
    toolbarVisible.value = false;
    return;
  }
  const range = sel.getRangeAt(0);
  const rect = range.getBoundingClientRect();
  if (rect.width === 0) {
    toolbarVisible.value = false;
    return;
  }
  const tbW = 360;
  const tbH = 44;
  let x = rect.left + rect.width / 2 - tbW / 2;
  let y = rect.top - tbH - 10;
  x = Math.max(8, Math.min(x, window.innerWidth - tbW - 8));
  if (y < 8) y = rect.bottom + 10;
  toolbarX.value = x;
  toolbarY.value = y;
  toolbarVisible.value = true;
}

function onMouseUp() {
  setTimeout(checkSelection, 10);
}

function onKeyUp() {
  setTimeout(checkSelection, 10);
}

function closeToolbar() {
  toolbarVisible.value = false;
}

function onDrop(e) {
  e.preventDefault();
  const file = e.dataTransfer.files && e.dataTransfer.files[0];
  if (file && file.type.startsWith("image/")) {
    insertImageFile(file, e.clientX, e.clientY);
  }
}

function onDragOver(e) {
  e.preventDefault();
}

function onPaste(e) {
  const items = e.clipboardData?.items;
  if (!items) return;
  for (const item of items) {
    if (item.type.startsWith("image/")) {
      const file = item.getAsFile();
      if (file) insertImageFile(file);
      e.preventDefault();
      return;
    }
  }
}

function insertImageFile(file, x, y) {
  const reader = new FileReader();
  reader.onload = () => {
    const img = document.createElement("img");
    img.src = reader.result;
    img.style.maxWidth = "100%";
    if (x !== undefined && document.caretRangeFromPoint) {
      const range = document.caretRangeFromPoint(x, y);
      if (range) range.insertNode(img);
      else editor.value.appendChild(img);
    } else {
      editor.value.appendChild(img);
    }
  };
  reader.readAsDataURL(file);
}

function resetContent() {
  editor.value.innerHTML = INITIAL;
}

function clearAll() {
  document.execCommand("selectAll");
  document.execCommand("removeFormat");
  document.execCommand("formatBlock", false, "<p>");
  editor.value.innerHTML = "<p></p>";
}

onMounted(() => {
  editor.value.innerHTML = INITIAL;
});
</script>

<template>
  <main class="app">
    <h1>Editor WYSIWYG</h1>
    <div class="actions">
      <button @click="resetContent">Resetar</button>
      <button @click="clearAll">Limpar formatação</button>
    </div>
    <div
      ref="editor"
      class="editor"
      contenteditable="true"
      @mouseup="onMouseUp"
      @keyup="onKeyUp"
      @drop="onDrop"
      @dragover="onDragOver"
      @paste="onPaste"
      @blur="closeToolbar"
    ></div>
    <FloatingToolbar
      :visible="toolbarVisible"
      :pos-x="toolbarX"
      :pos-y="toolbarY"
      @close="closeToolbar"
    />
  </main>
</template>

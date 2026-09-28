# Editor de Texto WYSIWYG

> Rich-text editor customizado com toolbar flutuante, formatação inline e drag-and-drop de imagens.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css`
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/components/FloatingToolbar.vue`, `src/styles.css`

## Implementação

### 1. Editor

- `div contenteditable` com placeholder (`:empty::before`) e conteúdo inicial de demonstração
- **Atenção**: não re-renderizar o conteúdo do editor por estado reativo (o caret pula) — o Vue controla a toolbar e o status, o HTML interno pertence ao usuário
- Toolbar flutuante: aparece acima da seleção quando há texto selecionado (`mouseup`/`keyup` com seleção não-colapsada); some ao clicar fora

### 2. Toolbar flutuante

- Componente `FloatingToolbar.vue`: posição via `getRangeAt(0).getBoundingClientRect()`, com clamp nos limites da janela
- Botões: **B**, *I*, U, S, link (pede a URL), listas ul/ol, blocos p/h2/h3, cor (mini paleta), remover formatação
- Estado ativo dos botões via `document.queryCommandState` (negrito ativo → botão realçado)
- Formatação via `document.execCommand` — deprecado na spec, mas suportado por todos os browsers e o caminho padrão sem libs

### 3. Drag-and-drop de imagens

- `dragover` + `drop` no editor (com `preventDefault`): `FileReader.readAsDataURL` → inserir `<img>` na posição do drop (caret via `document.caretRangeFromPoint` / `caretPositionFromPoint`)
- Imagens com `max-width: 100%` via CSS
- Também aceita colar imagem (evento `paste` → `clipboardData.items`)

### 4. Utilidades

- Botão "limpar formatação" (removeFormat em tudo) e "resetar conteúdo"
- Fechar toolbar com Esc

## Checklist — 100% da descrição

- [ ] Edição rich-text (contenteditable)
- [ ] Toolbar flutuante posicionada na seleção
- [ ] Formatação inline (negrito, itálico, sublinhado, riscado, link, listas, títulos, cor)
- [ ] Drag-and-drop de imagens (+ colar)

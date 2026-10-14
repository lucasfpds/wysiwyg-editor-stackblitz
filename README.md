# Editor de Texto WYSIWYG

> Rich-text editor customizado com toolbar flutuante, formatação inline e drag-and-drop de imagens.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **contenteditable** + `document.execCommand` para formatação
- **CSS puro** — sem pré-processador
- **FileReader API** para drag-and-drop e paste de imagens
- **Selection API** (`getRangeAt`, `getBoundingClientRect`) para toolbar flutuante

## Requisitos

- Node.js 18+

## Como executar

```bash
npm install
npm run dev
```

Abra http://localhost:5173 no navegador.

## Scripts

| Script | Descrição |
| --- | --- |
| `npm run dev` | Servidor de desenvolvimento (Vite) |
| `npm run build` | Build de produção |
| `npm run preview` | Serve o build de produção |

## Estrutura

```
src/
  main.js              entry: cria o app e importa o CSS
  App.vue              editor contenteditable, drop/paste de imagens
  components/
    FloatingToolbar.vue toolbar flutuante com botões de formatação
  styles.css           layout, editor, toolbar, botões
```

### Funcionalidades

- `div contenteditable` com placeholder (`:empty::before`)
- Toolbar flutuante aparece acima da seleção quando há texto selecionado
- Botões: negrito, itálico, sublinhado, riscado, link, listas ul/ol, blocos p/h2/h3, cor, remover formatação
- Estado ativo dos botões via `document.queryCommandState`
- Drag-and-drop de imagens (`FileReader.readAsDataURL` → `<img>` no caret)
- Paste de imagens (`clipboardData.items`)
- Fechar toolbar com Esc
- Botões "resetar conteúdo" e "limpar formatação"


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/wysiwyg-editor-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.

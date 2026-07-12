Pour un agent IA comme **OpenCode**, **Claude Code** ou **Codex**, l'objectif est de lui donner des capacités de **reverse engineering du frontend** : analyser la structure HTML/CSS, capturer les assets et reproduire l'interface sans copier le code propriétaire.

Voici un bon stack de skills.

# 1. Playwright ⭐⭐⭐⭐⭐

Le plus important.

Permet de :

* naviguer sur des sites dynamiques (React, Next.js, Vue)
* prendre des captures d'écran
* inspecter le DOM après exécution du JavaScript
* extraire HTML, CSS et images

Installation :

```bash
npm install -g playwright
npx playwright install
```

Exemple :

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage();

  await page.goto('https://example.com');

  console.log(await page.content());

  await page.screenshot({
    path: 'homepage.png',
    fullPage: true
  });

  await browser.close();
})();
```

---

# 2. Puppeteer ⭐⭐⭐⭐

Alternative à Playwright.

Installation :

```bash
npm install puppeteer
```

Très pratique pour :

* screenshots
* extraction du DOM
* récupération des assets

---

# 3. Firecrawl ⭐⭐⭐⭐⭐

Excellent pour les agents IA.

Permet :

* crawler un site entier
* récupérer le contenu en Markdown
* extraire la structure des pages
* générer des données faciles à analyser par les LLM

Installation :

```bash
npm install @mendable/firecrawl-js
```

ou

```bash
pip install firecrawl-py
```

---

# 4. Crawl4AI ⭐⭐⭐⭐⭐

L'un des meilleurs outils open source pour les agents.

Installation :

```bash
pip install crawl4ai
crawl4ai-setup
```

Fonctionnalités :

* crawl de sites complets
* extraction Markdown
* récupération du DOM
* compatible LLM
* génération de datasets pour reconstruire une UI

---

# 5. Browser Use ⭐⭐⭐⭐

Installation :

```bash
pip install browser-use
playwright install
```

Permet à l'agent de :

* ouvrir un navigateur réel
* cliquer
* scroller
* inspecter les composants
* prendre des screenshots
* explorer automatiquement les pages

---

# 6. Readability + BeautifulSoup ⭐⭐⭐

Installation :

```bash
pip install beautifulsoup4 lxml readability-lxml
```

Permet :

* nettoyer le HTML
* récupérer la hiérarchie des sections
* analyser la structure du site

---

# 7. Vision OCR ⭐⭐⭐⭐

Pour reproduire fidèlement le design :

```bash
pip install pillow pytesseract
```

ou

```bash
npm install tesseract.js
```

Permet :

* détecter les textes
* récupérer les polices
* analyser les espacements
* reconstruire le layout

---

# 8. MCP Playwright Skill ⭐⭐⭐⭐⭐

Très intéressant avec OpenCode.

Exemple :

```bash
npx @playwright/mcp@latest
```

Votre agent peut alors :

* ouvrir un site
* prendre des screenshots
* inspecter le DOM
* cliquer sur des éléments
* récupérer les styles CSS
* générer directement du code React/Tailwind.

---

# Configuration idéale pour copier le design d'un site

```
Agent IA
│
├── Playwright MCP
├── Crawl4AI
├── Firecrawl
├── Tesseract OCR
└── Browser Use
```

Workflow :

```
1. Ouvrir le site
2. Screenshot complet
3. Extraire DOM + CSS
4. Détecter couleurs/polices/icônes
5. Identifier composants réutilisables
6. Générer :
   - React
   - Next.js
   - Tailwind
   - Responsive mobile
```

Je te conseille aussi de créer un dossier `skills/website-cloner/` dans ton projet OpenCode avec :

```
skills/
└── website-cloner/
    ├── SYSTEM.md
    ├── SCRAPING.md
    ├── DESIGN_RULES.md
    ├── COMPONENTS.md
    └── TASKS.md
```

et un prompt système du style :

```text
Tu es un expert en reverse engineering frontend.

Pour chaque site :
1. Capture des screenshots desktop/mobile
2. Analyse le DOM final
3. Extrais les couleurs, polices et espacements
4. Déduis les composants UI
5. Génère uniquement du code Next.js + Tailwind
6. Ne copie jamais le code source propriétaire mot pour mot.
7. Produis une architecture réutilisable et responsive.
```

Cette approche permet de s'inspirer visuellement d'un site et de reconstruire une interface similaire sans réutiliser le code protégé du site d'origine.

# The SaaS Stack — Companion Course

A single self-contained HTML file that teaches the modern SaaS stack end to end, then makes you prove you learned it. Eighteen layers, ninety tools, four hundred and seven reference entries, plus a full HTML and CSS foundations course with its own test.

No build step. No dependencies. No network calls. No storage. Download one file, double-click it, and it works — on a plane, on an air-gapped machine, or from a USB key.

![Type](https://img.shields.io/badge/type-single--file%20web%20app-1f6feb)
![Dependencies](https://img.shields.io/badge/dependencies-0-7fd6a0)
![Offline](https://img.shields.io/badge/network%20calls-none-7fd6a0)
![Size](https://img.shields.io/badge/size-~465%20KB-e2b473)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## What it is

Most technical courses are either a video you cannot search or a PDF you cannot practise from. This is neither. It is one document with five modes over the same body of material, so reading, drilling and looking something up mid-task never means switching tools.

| Tab | What it does |
|---|---|
| **Learn** | The manual itself. Eighteen layers, each with an analogy, a failure mode, the vocabulary, the tools, a comparison matrix, decision rules, worked code and the traps. |
| **Drill** | Multiple-choice questions generated fresh from the manual on every run, across six question types, so the order can never be memorised. Pick the layers and the length; get the reasoning either way. |
| **Cards** | The fifty-four end-of-chapter questions as flashcards. No options — answer out loud, flip, mark yourself. |
| **Reference** | All 407 decision rules, traps and definitions in one searchable table. Filter by type, search by symptom. |
| **HTML & CSS** | Sixteen lessons covering the web foundations from the document skeleton to the cascade, the box model, flexbox, grid, responsive rules and the shipping checklist — followed by a 15-question test with shuffled questions and options. |

### Contents at a glance

| | |
|---|---|
| Layers | 18 |
| Tools profiled | 90 |
| Decision rules | 117 |
| Traps with fixes | 90 |
| Definitions | 200 |
| HTML & CSS lessons | 16 |
| Test questions | 15 (shuffled each attempt) |
| Files to deploy | 1 |

---

## Run it

**Locally.** Download `saas-stack-course.html` and open it in any browser. That is the entire installation procedure.

**Served locally** — only needed if you want to test with real HTTP headers:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/saas-stack-course.html
```

**GitHub Pages.** Push the file to a repository, rename it `index.html`, then enable Pages under *Settings → Pages → Deploy from a branch*. Note that GitHub Pages does not let you set custom response headers, so the header table below applies only to servers you control.

**Offline / air-gapped.** Copy the file. It has no external references of any kind — no CDN, no font download, no analytics, no telemetry.

---

## Security

The page is built so that its security claims are enforced by the browser rather than promised in a paragraph.

**Content Security Policy.** Declared in a meta tag in the `<head>`:

```
default-src 'none'; img-src 'self' data:; style-src 'unsafe-inline';
script-src 'unsafe-inline'; base-uri 'none'; form-action 'none'
```

| Directive | Effect |
|---|---|
| `default-src 'none'` | Nothing loads unless explicitly allowed below. Covers `connect-src`, so no fetch, XHR, WebSocket or beacon can leave the page. |
| `img-src 'self' data:` | Permits only the inline SVG favicon. |
| `style-src` / `script-src 'unsafe-inline'` | Required because the page's own CSS and JS are inline — that is what makes it a single file. There is no external or third-party code to allow. |
| `base-uri 'none'` | Blocks a `<base>` tag from rewriting relative URLs. |
| `form-action 'none'` | No form can submit anywhere. The page contains no forms. |

**Other measures in the file**

- `<meta name="referrer" content="no-referrer">` — no referrer is ever sent.
- **Clickjacking guard.** `frame-ancestors` cannot be delivered from a meta tag, so a script at the top of `<body>` hides the document immediately if it is loaded inside an iframe and shows a notice instead.
- **Link hardening.** Any link opening in a new tab has `rel="noopener noreferrer"` applied at runtime, closing the `window.opener` tabnabbing path.
- **Output escaping.** All content is escaped before insertion into the DOM; the reference search filters rather than interpolates its input, so there is no self-XSS path through the search box.
- **No storage.** No cookies, no `localStorage`, no `sessionStorage`, no IndexedDB. Closing the tab resets everything, including your score.

**Headers to set when you serve it**

`frame-ancestors` and `X-Content-Type-Options` are ignored inside a meta tag. On a server you control, set them for real:

```nginx
add_header Content-Security-Policy "default-src 'none'; img-src 'self' data:; style-src 'unsafe-inline'; script-src 'unsafe-inline'; base-uri 'none'; form-action 'none'; frame-ancestors 'none'" always;
add_header X-Content-Type-Options    "nosniff"          always;
add_header Referrer-Policy           "no-referrer"      always;
add_header Permissions-Policy        "geolocation=(), camera=(), microphone=(), interest-cohort=()" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

For Netlify or Cloudflare Pages, the same values go in a `_headers` file at the site root:

```
/*
  Content-Security-Policy: default-src 'none'; img-src 'self' data:; style-src 'unsafe-inline'; script-src 'unsafe-inline'; base-uri 'none'; form-action 'none'; frame-ancestors 'none'
  X-Content-Type-Options: nosniff
  Referrer-Policy: no-referrer
  Permissions-Policy: geolocation=(), camera=(), microphone=()
```

**Scope.** This is a static document with no backend, no accounts and no user data. The threat model is limited to what a static page can do to its reader: exfiltration, framing, tabnabbing and injection. All four are addressed above.

---

## Accessibility

- Semantic landmarks, `role="tablist"` navigation and a heading outline that runs in order.
- Every interactive control is a real `<button>` or `<a>`, reachable and operable by keyboard.
- Visible `:focus-visible` outlines throughout.
- `prefers-reduced-motion` is respected: transitions and animations are reduced to nothing for readers who ask for it.
- Dark and light themes, both meeting contrast requirements for body text. The theme toggle sits in the header.
- A print stylesheet strips the chrome so any view can be printed or saved as PDF.

---

## Under the hood

Vanilla HTML, CSS and JavaScript. No framework, no bundler, no package manager, no transpiler.

- **Routing** is `location.hash` based: `#/learn/07`, `#/drill`, `#/cards`, `#/ref`, `#/web/09`, `#/web/test`. Every view is linkable and the back button works.
- **Content** lives in three arrays near the top of the script — `DATA` for the eighteen layers, `WEB` for the sixteen lessons, `WEBQ` for the fifteen test questions. Rendering is pure string templating from those arrays.
- **Theming** is CSS custom properties on `:root`, swapped by a `data-t` attribute on `<html>`. Adding a theme means adding one block of variables.
- **Drill questions** are generated at runtime from the manual data across six question types, then spread so no single type dominates a run.

### Adding a lesson

Append an object to `WEB`. Block types available: `p`, `h`, `code`, `list`, `tbl`, `trap`, `note`, `check`.

```js
{id:"17", t:"Lesson title", hue:"#1f6feb",
 tag:"One sentence describing what this lesson is for.",
 blocks:[
   {h:"A section heading"},
   {p:"A paragraph. <code>Inline code</code> and <b>bold</b> are fine."},
   {code:{cap:"file or caption", lines:["line one","line two"]}},
   {list:["First point","Second point"]},
   {tbl:{head:["Column","Column"], rows:[["a","b"]]}},
   {trap:{t:"The mistake", f:"How to avoid it"}},
   {note:"An aside worth boxing."},
   {check:{title:"Checklist", items:["Do this","Then this"]}}
 ]}
```

Code samples are escaped automatically. One caveat: a literal `</script>` inside a sample would end the inline script block, so write it as `<\/script>`.

### Adding a test question

Append to `WEBQ`. Set `ch` to the `id` of the lesson it tests so the "reread lesson" link resolves.

```js
{ch:"11", q:"The question text.",
 correct:"The right answer, character for character",
 options:["The right answer, character for character","Wrong","Wrong","Wrong"],
 why:"Why the right answer is right and the tempting one is not."}
```

---

## Browser support

Any browser from 2021 onward: Chrome, Edge, Firefox, Safari, and their mobile versions. The page uses CSS custom properties, grid, flexbox and `clamp()`, all long since baseline. Internet Explorer is not supported and will not be.

---

## Roadmap

- Optional progress persistence, opt-in and local only
- Export a drill or test result as a printable report
- Arabic and French translations of the HTML and CSS lessons
- A second test bank for the eighteen SaaS layers, in the same format as the web test

---

## License

MIT. Use it, fork it, teach from it, ship it inside your own training material. Attribution is appreciated and not required.

---

## Author

**Fouad Barkaoui** — vibe coder, building on deep hands-on experience in prompt engineering.

Made with love. Every layer, lesson, rule and trap in this file was written, structured and shipped by hand.

© 2026 Fouad Barkaoui

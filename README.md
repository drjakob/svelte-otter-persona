# 🦦 Svelte-Otter

Eine importierbare Persona für [GWDG Chat AI](https://chat-ai.academiccloud.de), die dir beim Coden in **Svelte 5** hilft; mit einem System-Prompt, der die Runes-Syntax hart erzwingt und Code liefert, der ohne Nacharbeit direkt im [Svelte Playground](https://svelte.dev/playground) läuft.

Gebaut von Dr. Jakob. Otto ist German, knapp, nicht belehrend, und endet jede Antwort mit einem Coder-Spruch.

## Was drin ist

| Datei | Modell | Beschreibung |
| --- | --- | --- |
| `svelte-otter.json` | Claude Sonnet 4.6 | Standard-Persona; Runes-Wissen steckt im Prompt. Sofort einsatzbereit. |
| `svelte-otter-arcana.json` | Claude Sonnet 4.6 + RAG | Variante mit Grounding in der offiziellen Svelte-Doku (Arcana). Braucht eine eigene Arcana; siehe unten. |

Beide erzwingen Svelte 5: `$state`, `$derived`, `$effect`, `$props`, `$bindable`, Snippets mit `{@render}`, `mount()`. Verboten sind `export let`, `$:`, `on:click`, `createEventDispatcher`, `<slot>`, `new Component()`.

## Importieren

### Variante A: Raw-URL (dauerhaft, empfohlen)

Repo bei GitHub anlegen, Datei hochladen, dann diesen Link öffnen:

```
https://chat-ai.academiccloud.de/chat?import=https://raw.githubusercontent.com/drjakob/svelte-otter-persona/main/svelte-otter.json
```

### Variante B: Data-URI (Bookmark, kein Hosting)

Die ganze Persona steckt in der URL; ideal als Lesezeichen. Du erzeugst sie mit:

```bash
python3 -c "import json,base64; d=json.load(open('svelte-otter.json',encoding='utf-8')); c=json.dumps(d,ensure_ascii=False,separators=(',',':')).encode(); print('https://chat-ai.academiccloud.de/chat/?import=data:application/json;base64,'+base64.b64encode(c).decode())"
```

## Die Doku-Arcana bauen (für `svelte-otter-arcana.json`)

So bekommt Otto die echte Svelte-5-Doku als Quelle statt nur Trainingswissen.

1. Svelte liefert ihre Doku LLM-fertig. Lade dir eine Variante als Markdown herunter:
   - `https://svelte.dev/docs/svelte/llms.txt` (nur Svelte, ohne Kit; gute Default-Wahl)
   - `https://svelte.dev/llms-small.txt` (kompakt, weniger Token)
   - `https://svelte.dev/llms-full.txt` (alles, sehr groß)

   ```bash
   curl -sL https://svelte.dev/docs/svelte/llms.txt -o svelte5-docs.md
   ```

2. Auf der [Arcana-Seite](https://chat-ai.academiccloud.de/arcanas) eine neue Arcana anlegen und `svelte5-docs.md` hochladen (PDF, Text und Markdown sind erlaubt). Die Indexierung erledigt Docling.
3. Die generierte **Arcana-ID** kopieren und in `svelte-otter-arcana.json` den Platzhalter ersetzen:

   ```json
   "arcana": { "id": "DEIN-ACCOUNT/svelte5-docs" }
   ```

4. Persona importieren wie oben. Otto zitiert jetzt genutzte Stellen mit `[RREF Nummer]`.

Bei RAG bleiben `temperature` und `top_p` bewusst auf 0 bzw. 0.05; das hält die Antworten nah an der Quelle.

## Anpassen

**Modell tauschen.** Claude läuft bei GWDG extern. Willst du alles in-house (Open-Weight, höchster Datenschutz), ersetze zwei Zeilen:

```json
"model": "glm-4.7",            "model-name": "GLM-4.7"
"model": "qwen3-coder-30b-a3b-instruct", "model-name": "Qwen 3 Coder 30B A3B Instruct"
"model": "devstral-2-123b-instruct-2512", "model-name": "Devstral 2 123B Instruct 2512"
```

**Ton, Regeln, Verbote.** Alles steckt im `messages`-System-Prompt; einfach editieren. Die `info`-Nachricht ist die Begrüßung, die nur die Nutzer:innen sehen.

## Format

Persona-Dateien folgen dem [GWDG-Schema](https://github.com/gwdg/chat-ai-personas):

```json
{
  "title": "...",
  "subtitle": "...",
  "model-name": "...",
  "model": "...",
  "temperature": 0.2,
  "top_p": 0.5,
  "enable_tools": false,
  "messages": [
    { "role": "system", "content": "..." },
    { "role": "info", "content": "..." }
  ],
  "arcana": { "id": "account/name" }
}
```

## Lizenz

Mach damit, was du willst. Bleib wild. 🦦

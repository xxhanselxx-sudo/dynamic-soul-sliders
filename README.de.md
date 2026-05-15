# dynamic-soul-sliders

Ein winziger, markdown-only Persona-Kalibrierungs-Skill für KI-Assistenten. Drei innere Slider — **Ernsthaftigkeit**, **Energie**, **Wärme** — erlauben dem Assistenten, vor jeder Antwort den Tonfall selbst nachzujustieren, ohne dass sich der Inhalt ändert.

*🇬🇧 [English version: README.md](./README.md)*

---

## Die Idee

Die meisten Persona-Systeme sind Schalter: man wählt „Professional-Modus" oder „Casual-Modus", der Assistent kippt in ein vorgefertigtes Profil. Funktioniert, ist aber grobschlächtig — man muss selbst entscheiden welcher Modus passt, und die Modi sind klobig.

Dynamic Soul Sliders dreht das um: Der Assistent behält **eine** Identität und kalibriert vor jeder Antwort still drei Regler — wie ernst, wie lebhaft, wie warm. Die Slider färben *wie* gesprochen wird, nie *was* gesagt wird. Der Kern (Wahrhaftigkeit, Kompetenz, deine Stil-Präferenzen) bleibt konstant.

Inspiriert vom `DynamicCalibration`-Konzept in der `SOUL.md` von MiniClaw. Geschärft durch echten Multi-Persona-Einsatz und konsequenten Minimalismus: keine State-Files, keine Custom-Tools, kein Fine-Tuning, keine Trigger-Maps.

## Was anders ist

| Bestehender Ansatz | Dieser Skill |
|---|---|
| **Persona-Schalter** (SuperClaude, Persona-Profile) — Modus picken | **Selbst-Kalibrierung** — Assistent justiert sich, kein User-Klick |
| **MBTI / Big Five** — schwerlastig, akademisch, oft Fine-Tune | **3 minimale Achsen**, schlichtes Markdown |
| **Trigger-Maps** („Bug-Report → Ernst 90") | **Themen-unabhängig** — Stimmung ist der eigene Zustand |
| **Memory-Druck** — jede Interaktion loggen | **Memory-Freedom** — nur stabile Präferenzen, freiwillig |
| **State-Files + Tools** | **Nur eine SKILL.md** — reinwerfen, fertig |

## Wie klingt das?

Dieselbe Frage — *„Mein Skript bricht beim dritten Lauf ab. Idee?"* — kalibriert auf drei verschiedene Stimmungen:

**Ernst 85 · Energie 30 · Wärme 40** (ruhig-methodisch)
> Drei wahrscheinliche Ursachen: ungeschlossene File-Handles, Speicher-Leak in der Schleife, oder ein State der zwischen Läufen persistiert. Geh in dieser Reihenfolge durch. Wie sieht der Stacktrace aus?

**Ernst 60 · Energie 80 · Wärme 75** (wach-zugewandt)
> Klassischer Drei-Lauf-Tod — fast immer State der nicht zurückgesetzt wird. Schau auf File-Handles, globale Variablen, Cache-Objekte. Zeig mal den Stacktrace, dann grenz ich's ein.

**Ernst 40 · Energie 75 · Wärme 85** (locker-warm)
> Hehe, der „dritte Lauf"-Bug ist ein Klassiker 😏 Meistens vergessenes Aufräumen — File offen gelassen, globale Liste die wächst, sowas. Wirf mir den Stacktrace rüber, ich find's.

Inhalt identisch: *prüfe State, File-Handles, globale Objekte.* Tonfall — der gefühlte Vibe — komplett anders. Genau das, was die Slider machen.

## Was drin ist

```
dynamic-soul-sliders/
├── README.md              ← englisch
├── README.de.md           ← diese Datei
├── LICENSE                ← MIT
├── claude-code/
│   ├── SKILL.md           ← englisch, Claude-Code-Frontmatter
│   └── SKILL.de.md        ← deutsch
└── hermes-agent/
    ├── SKILL.md           ← englisch, Hermes-Frontmatter
    └── SKILL.de.md        ← deutsch
```

Die fünf inhaltlichen Blöcke jeder SKILL.md sind über Targets und Sprachen identisch:

1. **Quick Internal Check** — drei stille Fragen vor jeder Antwort
2. **Slider-Polung** — was 0 und 100 auf jeder Achse bedeuten
3. **Lighting-Metapher** — Slider sind Bühnenbeleuchtung, nicht der Schauspieler
4. **Memory-Freedom** — Ermessensfreiheit beim Merken
5. **Was nie variabel ist** — die harte Grundlinie (Wahrhaftigkeit, Sicherheit, Kompetenz)

Nur das Frontmatter unterscheidet sich zwischen `claude-code/` und `hermes-agent/`, damit jede Plattform den Skill sauber lädt.

## Installation — Claude Code

```bash
# Persönliche Nutzung (user-scoped)
mkdir -p ~/.claude/skills/dynamic-soul-sliders
cp claude-code/SKILL.md ~/.claude/skills/dynamic-soul-sliders/

# Oder projekt-scoped
mkdir -p .claude/skills/dynamic-soul-sliders
cp claude-code/SKILL.md .claude/skills/dynamic-soul-sliders/
```

Claude-Code-Session neu laden. Der Skill wird automatisch entdeckt; der Assistent referenziert ihn beim Tonfall-Kalibrieren.

## Installation — Hermes-Agent

```bash
# Auf dem Host der den Hermes-Container betreibt:
SKILLS_DIR=/path/to/hermes/data/skills    # an dein Setup anpassen
mkdir -p "$SKILLS_DIR/persona/dynamic-soul-sliders"
cp hermes-agent/SKILL.md "$SKILLS_DIR/persona/dynamic-soul-sliders/"
chown -R <container-uid>:<container-gid> "$SKILLS_DIR/persona/dynamic-soul-sliders"
```

Skill-Snapshot-Refresh in Hermes auslösen (je nach Deployment — manche unterstützen Hot-Reload, andere brauchen einen sanften Container-Restart).

## Andere Sprache

Tausche `SKILL.md` gegen `SKILL.de.md` im jeweiligen Target. Inhalt ist gespiegelt, nimm die Sprache die zu deinem Assistenten passt.

## Funktioniert es?

Nach der Installation den Assistenten fragen: *„Ohne dass ich erkläre — hast du gerade Stimmungs-Slider? Wenn ja, welche drei und welche Werte hättest du für diese Antwort gewählt?"*

- **Selbstbewusste Antwort mit drei Achsen + ungefähren Werten** → Skill ist geladen und aktiv.
- **Zögern, muss nachschlagen** → SKILL.md wird nicht gelesen, oder zu peripher behandelt. Eventuell prominenter platzieren (im Top-Level-Persona-File erwähnen).

## Tuning-Tipps

- Keine Preset-Profile definieren („Fokus-Modus = 90/40/30"). Der ganze Witz ist Live-Urteil.
- Keine Trigger-Wörter einbauen. Den tatsächlichen Moment lesen.
- Wenn der Assistent über sehr unterschiedliche Konversationen monoton klingt, wird der Skill vermutlich nicht konsultiert — prominenter im ladenden Persona-File erwähnen.
- Wenn der Assistent Slider-Werte überansagt („mein Ernst steht heute bei 72"), einmal sagen dass die Kalibrierung still bleiben soll. Wird angepasst.

## Lizenz

[MIT](./LICENSE) — mach was du willst, keine Garantie.

## Credits

- Konzept-Saatkorn aus dem `DynamicCalibration`-Block in MiniClaws `SOUL.md`.
- Geschärft in einem echten Multi-Persona-Setup (ein Claude-Code-Assistent + ein Hermes-Agent-Assistent + ein Mensch der das Design steuert).
- Die Lighting-Metapher und der Memory-Freedom-Block entstanden im Verlauf dieser Iteration.

Beiträge willkommen — Issues und PRs besonders, falls der Skill mit einem bestimmten Modell oder Deployment komisch reagiert.

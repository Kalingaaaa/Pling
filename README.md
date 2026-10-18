# Pling

**P**ortuguese **ling**ua: eine persönliche Lern-App für europäisches Portugiesisch, entstanden zur Vorbereitung auf die DIPLE-Prüfung (Niveau B2).

Pling ist bewusst klein gehalten: eine einzige HTML-Datei, ohne Build-Schritt, mit einem kleinen Server-Endpunkt, damit der Lernstand auf allen Geräten derselbe ist.

---

## Bereiche

| Bereich | Was man übt | Wiederholung |
|---|---|---|
| **Vocabulário** | Wörter und Wendungen, Deutsch → Portugiesisch | Leitner-System, 6 Caixas |
| **Calinadas** | Wiederkehrende Fehler und falsche Freunde: Die falsche Version (oder das fremde Wort) erscheint, man schreibt die richtige | Leitner-System, 6 Caixas |
| **Redação** | Lückensätze aus eigenen, vom Lehrer korrigierten Texten, dazu neue Sätze mit derselben Regel | Runden à 10 Sätze, gemischt |
| **Pronúncia** | Texte laut lesen, danach die automatisch markierte Aussprache aufdecken | Tagesziel: 2 Texte |

### Vocabulário
- Kategorien in drei Gruppen: **Tema** (Inhalt), **Texto** (feste Wendungen einer Textsorte) und **Função** (Konnektoren, Daten beschreiben).
- Nicht gewusste Wörter kommen am Ende der Runde noch einmal dran.
- Modi: Lista, Quiz (Übersetzung verdeckt), Praticar (Eingabe), Adicionar, Importar.

### Calinadas
*Calinada* = grober Sprachschnitzer. Die Kategorien sind Acentos e ortografia, Preposições e contrações, Conjuntivo, Formas verbais, Concordância e construção, Expressões e registo, Interferências · Espanhol und Interferências · Inglês.
- Nach dem Antworten erscheint ein Hinweis zur Fehlerquelle, z. B. `esp. siguiente`, `ingl. Munich` oder `Falso amigo: a oficina = die Werkstatt`.

### Redação
- Jeder Satz hat eine Lücke, einen Hinweis (z. B. Infinitiv und Person) und eine Regel, die erst nach dem Antworten erscheint.
- „do teu texto“ = Satz aus dem eigenen Text (zeigt danach die ursprüngliche falsche Version), „frase nova“ = neuer Satz zur selben Regel.
- Fehlertypen werden gemischt (Interleaving). Falsch beantwortete Sätze kommen am Rundenende noch einmal.
- Eine Runde ist fällig, wenn falsche oder ungeübte Sätze offen sind oder die letzte Runde mindestens 3 Tage zurückliegt.

### Pronúncia
Regelbasierte Markierung von vier zuverlässigen Ausspracheregeln:

| Farbe | Regel |
|---|---|
| [u] | unbetontes -o am Wortende |
| [i] | *e* als Konjunktion, *e* in *ea* |
| [z] | -s/-z am Wortende vor Vokal (Bindung) |
| [ʒ]/[ʃ] | -s/-z am Wortende vor Konsonant oder Pause |

---

## Übersicht „Hoje“

Über den Reitern steht, was heute ansteht: ein Kreis pro Bereich. **Começar a praticar** führt nacheinander durch alles Offene (Vocabulário → Calinadas → Redação → Pronúncia), am Ende jeder Runde geht es mit **Seguinte** weiter. Falsch beantwortete Karten kommen erst zum Schluss wieder.

## Leitner-System

| Caixa | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|
| Nächste Wiederholung nach | 1 Tag | 3 Tagen | 7 Tagen | 14 Tagen | 30 Tagen | 60 Tagen |

Richtig → eine Caixa weiter, falsch → zurück in Caixa 1. Mit „Só as devidas“ übt man nur, was heute fällig ist. Nicht fällige Karten lassen sich als Training üben, ohne die Caixa zu ändern.

## Antwortprüfung

- **Streng:** Akzente (*á* ≠ *à*), Groß- und Kleinschreibung (außer beim ersten Buchstaben), Bindestriche, Kommas, fehlende Leerzeichen.
- **Tolerant:** zusätzliche Leerzeichen, Punkte, `...` und `…`, Klammern, Leerzeichen um `/`.
- Bei Adjektiven mit `o/a` (z. B. `moderado/a`) gilt auch die o-Form.

---

## Importformate

Einträge werden zeilenweise eingefügt, die Felder sind mit `|` getrennt.

**Vocabulário**
```
português | alemão | categoria
abrandar | sich verlangsamen | Descrever dados · Função
```

**Calinadas**
```
versão correta | versão errada | origem / armadilha | categoria
a seguinte | a siguinte | esp. siguiente | Interferências · Espanhol
```

**Redação** (genau eine Lücke in `[ ]`, letztes Feld leer = neuer Satz)
```
frase com [resposta] | pista | tipo de erro | regra | o que escreveste
Quando [entrar] em vigor esta regra, … | entrar | Futuro do conjuntivo | Quando + futuro → futuro do conjuntivo. | Quando entre em vigor
```

**Pronúncia** (die Markierung wird automatisch erzeugt)
```
texto | tema
O tempo está bom hoje. | Tempo
```

---

## Technik

```
index.html      die komplette App (HTML, CSS, JavaScript)
api/vocab.js    Vercel Serverless Function: liest und schreibt die Daten in Upstash Redis
```

- **Hosting:** Vercel. Jeder Push bzw. Upload auf GitHub wird automatisch veröffentlicht.
- **Daten:** Upstash Redis über die REST-API. Umgebungsvariablen in Vercel: `KV_REST_API_URL`, `KV_REST_API_TOKEN` sowie das App-Passwort (Name siehe `api/vocab.js`).
- **Zugang:** Beim ersten Öffnen fragt die App nach dem Passwort und merkt es sich im Browser.
- **Schlüssel in Redis:** `vocab:*`, `erros:*` (Calinadas), `redacao:*`, `aud:*`, dazu `backup:*` und `plan:today`.
- **Bibliotheken:** keine, außer SheetJS für den Excel-Export (wird erst beim Klick geladen).

### Datensicherheit
- Wenn Daten nicht sicher geladen wurden (Netzwerk- oder Serverfehler), speichert die App nichts und zeigt einen Hinweis.
- **Sicherungskopien:** automatisch einmal pro Tag und bei jeder neuen Version der App, die letzten 7 bleiben erhalten. Unter „⟲ Cópias de segurança“ lässt sich jede wiederherstellen. Der aktuelle Stand wird vorher selbst gesichert.
- **Export:** „⬇ Exportar (Excel)“ lädt Vocabulário, Calinadas und Redação als Excel-Datei herunter.
- Änderungen an bestehenden Daten (Umbenennungen, Zusammenführungen) laufen genau einmal und werden vor der Auslieferung mit echten Exportdaten getestet.

### Neue Version veröffentlichen
1. Neue `index.html` im Repository ersetzen (hochladen oder committen).
2. Vercel veröffentlicht automatisch.
3. App einmal öffnen: Vorher wird automatisch eine Sicherung angelegt, danach laufen eventuelle einmalige Datenänderungen.

Die `index.html` verwendet Windows-Zeilenenden (CRLF). Beim Bearbeiten beibehalten, damit GitHub nicht jede Zeile als geändert anzeigt.

---

## Design

Farbpalette **Costa Atlântica**: Navy `#1B4B6B`, Sand `#FAF8F3`, Terracotta `#C97B4A`, Seegras `#3C6B62`, Fehler-Rot `#A3402B`. Reiter im Karteikarten-Look, ausgelegt fürs Handy ab 320 px Breite.

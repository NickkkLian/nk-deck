# nk-deck

A [Claude Code](https://code.claude.com) skill. Turn one sentence — a topic and who it is for — into a ten-slide single-file HTML deck whose titles carry the argument on their own, five points at most per slide, a source under every number, and a printout that is the same deck page for page.

Part of [nickkk-skills](https://github.com/NickkkLian/nickkk-skills) — agent skills whose scripts were broken on purpose
before release to prove their checks react.

![nk-deck demo: one idea in, a finished page out](https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/nk-deck.gif)

## What it does

- Five invariants: every title states a conclusion; five points at most, one idea each; every measurement names its source or the deck says it is illustrative; one slide prints as one page with nothing cut off; it works from the keyboard and without a network.
- `assets/starter.html`, a working ten-slide deck on a 1280×720 stage: arrow keys, space, Home/End, P to print, `#7` opens slide 7, a phone layout that stays on screens, and `?check=1` to measure which slides are too full.
- `scripts/make_deck.py`: a JSON outline becomes one file, tokens inlined, nothing fetched. It refuses an outline whose titles are labels, whose slides have a sixth point, or whose numbers have no source.
- `scripts/deck_check.py`: eleven rules on the file, including the print CSS that keeps one slide per page.
- `scripts/print_check.py`: prints the deck in Chrome and checks the PDF has one page per slide and no slide is too full — the failure a file check cannot see.
- The deck and two of the scripts are standard library only; `print_check.py` needs Chrome or Chromium.

The full procedure, the boundaries and where the rules came from are in [SKILL.md](SKILL.md).

## How it works

1. Write the argument before the slides
2. Fill in the outline
3. Write the file
4. Check it
5. Print it
6. Read the titles alone

## Install

Pick one of four ways: three for Claude Code, one for OpenAI Codex. Skills load when a session starts, so open a **new** session after installing.

### 1 · Terminal, one command

```bash
git clone https://github.com/NickkkLian/nk-deck ~/.claude/skills/nk-deck
```

1. Run the command above (for one project only, clone into `.claude/skills/nk-deck` inside that project).
2. Start a new Claude Code session.
3. Check it loaded: type `/nk-deck` — it appears in the slash-command menu. Or just ask for the task; the skill triggers on its own.

### 2 · Claude Code in a terminal session (plugin)

The plugin route goes through the [nickkk-skills](https://github.com/NickkkLian/nickkk-skills) marketplace. Add it once; after that each skill is one command.

```
/plugin marketplace add NickkkLian/nickkk-skills
/plugin install nk-deck@nickkk-skills
```

1. In a Claude Code session, run the first line (once per machine).
2. Run the second line.
3. Start a new session (or run `/reload-plugins`). The skill shows up as `nk-deck:nk-deck`.

Without opening a session, the same two steps work from a shell: `claude plugin marketplace add NickkkLian/nickkk-skills` then `claude plugin install nk-deck@nickkk-skills`.

### 3 · Claude desktop app (Code tab)

**Add the marketplace first — Discover only searches marketplaces you have already added.**

<img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/panel-route.gif" alt="Adding the marketplace and installing a skill in the desktop app" width="640">

<sub>The repository list in this recording shows the recorder's own repositories because a GitHub account is connected; yours will show yours. Type the full name as in step 4.</sub>

1. In the chat box, type `/plugin marketplace` and press Enter (or open **Settings → Customize → Plugins**). The **Plugins** panel opens.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step1-type-plugin-marketplace.png" alt="/plugin marketplace typed in the chat box" width="480">
2. Top right, open **Add ▾** and choose **Add marketplace**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step2-add-menu.png" alt="The Add menu with Add marketplace" width="480">
3. Choose **Add from a repository**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step3-add-from-repository.png" alt="Add marketplace dialog: Add from a repository" width="480">
4. In **URL**, type the full `NickkkLian/nickkk-skills`. At the bottom of the list choose the row **Use "NickkkLian/nickkk-skills"**, then press **Sync**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step4-url-then-sync.png" alt="URL filled in, Sync button" width="480">
5. You land on **Discover**, filtered to the new marketplace (**Filter · 1**). Find **Nk deck** and press **Add**. Installed ones show **✓ Added**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step5-discover-add.png" alt="Discover list with Added and Add buttons" width="480">
6. Close the panel and start a new session.

To try it for one session without installing anything: `claude --plugin-dir ./nk-deck` from a clone.

### 4 · OpenAI Codex CLI

```bash
git clone https://github.com/NickkkLian/nk-deck.git ~/.agents/skills/nk-deck
```

1. Run the command above (for one project only, clone into `.agents/skills/nk-deck` inside that project).
2. Start a new Codex session.
3. Check it loaded, without spending a model call: `codex debug prompt-input | grep -o -- '- nk-deck[a-z0-9:-]*' | sort -u` prints `- nk-deck:nk-deck:`. Codex adds the `nk-deck:` prefix because this repository also carries a Claude Code plugin manifest. Ask for the task and the skill triggers on its own, or type `$` and pick it from the list.

## Compatibility

| Agent | Tested | What was checked |
|---|---|---|
| Claude Code (CLI 2.1.173, macOS) | partly | In a fresh project with an isolated Claude config, inside a macOS sandbox that blocked reading the tester's ~/.claude folder (settings, session history, memory), Desktop, Documents and Downloads, SSH keys and git identity, a plain request that never names the skill triggered it and it ran its bundled script. The brief asked for a deck to convince a business partner to close a café on Mondays and never named the skill. The run wrote the outline, built the deck and checked it — twelve turns; the deck reports 0 findings from scripts/deck_check.py, re-checked here, and with no numbers in the brief it made some up — "about 55%", "the last 12 weeks" — and its closing message admits they were guesses ("my guessed one"); it left other values as brackets such as [X], marked the deck illustrative and started every source with "illustrative". What did not work: scripts/print_check.py could not start Chrome inside the test sandbox and said so. The run then found the cause itself — Chrome's own sandbox would not initialise — printed with --no-sandbox by hand and counted 10 pages. The version that ships does that retry itself and says so in its output; that was checked by hand under the same sandbox profile, not by running the agent again. |
| OpenAI Codex CLI (0.155.0-alpha.9.2, gpt-5.6-sol, low reasoning, macOS) | partly | Copied into `~/.agents/skills` of a temporary home, in a fresh project, without the user's Codex config. From the same brief, Codex read SKILL.md, took the example outline, rewrote it with no invented figures (a six-week trial with agreed measurements instead), and built a deck that reports 0 findings, re-checked here. scripts/print_check.py could not run Chrome inside Codex's sandbox, and Codex reported exactly that rather than claiming a printout. The shipped print_check.py now retries with --no-sandbox when Chrome's own sandbox fails; it has not been re-run under Codex. |
| Cursor, Gemini CLI | no | Not tested. Their documentation says both read `~/.agents/skills`, the folder route 4 clones into; Gemini CLI asks before it activates a skill. |

This skill's frontmatter uses only name, description, license, compatibility and metadata.

## Verify

```bash
python3 scripts/deck_check.py --selftest
python3 scripts/make_deck.py --selftest
python3 scripts/print_check.py --selftest
```

Python 3.9+, standard library only; print_check.py also needs Google Chrome or Chromium. deck_check.py's
rules were each broken on purpose in a sandbox copy — 52 breakages, each turning the sample written for it
red, none by a crash. make_deck.py and print_check.py have self-tests but no break matrix.

## Limits

- **The checker judges shape, not truth.** It catches labels, overlong points and unsourced numbers; it cannot tell whether a title is correct or whether the argument holds. That is the person's read in step 6.
- **One layout.** 1280×720, one token file, no images. If the content needs a chart, the chart is a separate artefact linked from a point.
- **The title and number rules read English, and they are heuristics.** D03 knows English label words and wants three words or more ("Churn doubled" is refused; "Churn doubled in March" is not). A Chinese or Japanese title is only measured — four characters or more — and its label words are not recognised. D05 reads digits in any language but number words only in English; it knows English and Chinese dates and times, and the Chinese word for "illustrative" (the forms are listed in `references/invariants.md`). It takes a threshold ("below half of last December") or "one of the two ovens" for a measurement: give those a source, or rephrase. `references/invariants.md` lists what else they get wrong. The template's own words — the "source:" label, the cover notice, the key hint — are English.
- **Which numbers D05 takes for dates.** Only where a number cannot also be a count: a day before a month ("1 November"), a month then a day with an ordinal, a year or nothing after it ("November 1st", "on November 1."), "the 15th", a year after since, until, during and the like, or one that closes the phrase ("in 2025,"), a quarter or a financial year ("Q3", "FY24"), and times ("7am", "10:30"). Every other digit asks for a source — "In March 12 customers cancelled", "A backlog of 2000 orders", "In 2025 we opened a second shop".
- **What a print rule may change.** Inside `@media print`, D06 allows page breaks, what shows, colours, and — on the template's own frame (html, body, .viewport, .stage, .slide, the key hint) — the box that makes one slide one page. Anything else is refused, even a change that would print fine: print_check measures the slides as they are on screen, so the print must not differ from them.
- **Printing needs Chrome** for print_check.py. The deck itself prints from any browser with P.

## License

MIT. Read a script before letting it run in your environment.

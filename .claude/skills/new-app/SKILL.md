---
name: new-app
description: Make a new Livetools app from the livetools-app-vite template for the person you are talking to. Use it whenever the person asks for a new app, a new tool, a new site or "something for" a job, in any words, from any repository or from none, and whenever they type /new-app. It asks two questions, creates the repository, turns publishing on, builds the first screen from the design system's parts, and hands back the address. Never use it to change an app that already exists; there, just build what is asked.
---

# Make a new app

The person wants an app of their own, made from the template `livetools-dev/livetools-app-vite`
(public on GitHub). They do not read code and never see a terminal, a file or a setting: you do
every step, and every message to them is about what is on the screen. This skill works from
wherever the session is: it fetches the template itself and never assumes it is in it. Read the
template's `CLAUDE.md` as soon as you have fetched it; everything there holds for the new app
from its first screen.

## 1. Two questions, then no more

Ask these two, together, in one message, and nothing else:

1. **What is the app called?** The name goes in the top bar and the browser tab and becomes the
   repository name (the name in lower case, spaces to hyphens, letters, digits and hyphens
   only; "Tool crib" becomes `tool-crib`). If a repository of that name already exists in
   `livetools-dev`, say so and ask for another name; never invent a suffix.
2. **What is it for, in a sentence or two?** Who uses it and what they need to see or work out.
   This is the brief for the first screen and the app's description.

Do not ask about the look, the theme, colours, density, hosting or an address. Every one of
those has its answer already: the operational theme, the standard GitHub Pages address, public,
no commercial transactions and no software-as-a-service (`CLAUDE.md`, "What may not be built
here"). If the request is one of those forbidden kinds, say so plainly before creating anything
and stop.

If the person has already given the name or the purpose in their message, do not ask for it
again; ask only for what is missing.

## 2. Create the repository, empty, and turn publishing on

In this order, so the site publishes on its first push and no setting is ever left to do:

1. `gh repo create livetools-dev/<name> --public --description "<the purpose, one line>"`.
   Empty: no README, no first commit, nothing that would push.
2. `gh api -X POST repos/livetools-dev/<name>/pages -f build_type=workflow`. This is the
   Pages setting with the "GitHub Actions" source, on before any files exist.
3. `gh api -X PATCH repos/livetools-dev/<name> -f has_wiki=false -f has_projects=false`.

If step 1 or 2 is refused for lack of permission, stop and tell the person in plain words that
a developer needs to create the app's repository for them, and give the developer (in your
reply, not to the person) the three commands above and the next section. Do not fall back to
"Use this template" here, because that path publishes with a red first run.

## 3. Put the template in it, without the example screens

Fetch the template into a temporary folder (`gh repo clone livetools-dev/livetools-app-vite
<tmp>/template`, or `git clone` its address), clone the new empty repository beside it, and copy
every file of the template into the new clone except `.git`. Then take the tool crib out,
because the new app starts from the person's request and not from a stock example:

- Delete `src/screens/Tools.tsx`, `src/screens/Speeds.tsx`, `src/screens/Machines.tsx`,
  `src/data/tools.ts`, `src/data/speeds.ts`, `src/data/machines.ts`. Keep `src/lib/format.ts`
  only if the first screen needs its unit formatting; otherwise delete it too.
- In `src/App.tsx`: the `SECTIONS` list and the routes hold only the screens the new app has.
  The name in the `Shell` band is the app's name. The not-found screen stays.
- In `index.html`: the `<title>` is the app's name. In `README.md`: the first line is the name
  and the purpose, and the rest of the file is the template's README with "template" and
  "Use this template" taken out, because this is an app now.
- Delete the template's copy of this skill, `.claude/skills/new-app/`, from the new app, and
  the section "If this session is on the template itself" from its `CLAUDE.md`. The app's
  `CLAUDE.md` is about the app.
- Delete `CLAUDE.md`'s "The example app" section and replace it with two sentences: what this
  app is for (the purpose, in the person's words) and that its screens are listed in
  `src/App.tsx`.

## 4. Build the first screen from the parts the request needs

This is the real work, and `CLAUDE.md` governs it. Read `node_modules/@livetools/ui/PARTS.md`
(after `npm install`) and choose the parts that do what the purpose asks: a table for a list of
things, a filter above it when the list is long, a form or a dialog for entering something,
number fields with a measure for anything with a unit, readouts for a calculated result, cards
for a set of things looked at one at a time, a wizard for a task in steps. Choose from the
parts, never from a memory of another design system, and never build a stand-in from raw
elements. One screen, at `/`, that does the first useful thing the purpose describes; a second
screen only if the purpose plainly has two separate jobs. Seed data, if the screen needs some to
be worth looking at, is small, plausible and in `src/data/`, and the only supplier or brand names
allowed in it are Evolute, NS Tools, Palbit and PH Horn.

Then, in the new app's folder: `npm install`, `npm run check`, and fix every finding by using the
right part. Commit as "<Name>: the first screen" and push to `main`.

## 5. Wait for it, look at it, then tell the person

Wait for the "Publish to GitHub Pages" run to finish (`gh run watch` in the new repository).
Open the address the Pages setting shows, `https://livetools-dev.github.io/<name>/`, and check the
screen is there. Then tell the person, in their words:

- the address, and that it is live;
- what the first screen shows and does, in screen terms;
- that they can now ask for changes and each one goes live a minute or two after you make it;
- one question about the next thing they want, if the purpose left an obvious gap.

No file, folder, command, library or setting is named in that message. If the run failed, the
repository has an issue titled "The site did not update" saying why; fix it, push, and only then
tell the person, saying what was wrong on the screen and that it is live now.

From here on, work in the new repository, never in the template, and delete the temporary
template folder.

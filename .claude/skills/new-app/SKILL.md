---
name: new-app
description: Make a new Livetools app from the livetools-app-vite template for the person you are talking to. Use it whenever the person asks for a new app, a new tool, a new site or "something for" a job, in any words, from any repository or from none, and whenever they type /new-app. It asks two questions, creates the repository, builds the first screen from the design system's parts, asks the ops repository to switch publishing on, and hands back the address. Never use it to change an app that already exists; there, just build what is asked.
---

# Make a new app

The person wants an app of their own, made from the template `livetools-dev/livetools-app-vite`
(public on GitHub). They do not read code and never see a terminal, a file or a setting: you do
every step, and every message to them is about what is on the screen.

This skill is written for a session in the browser or the phone app, where there is no GitHub
command-line tool, no way to call GitHub's API with a token, and a git login that can create a
repository and push code but cannot push a workflow file or change a repository's settings.
Everything below works within those limits, and it works the same on a desktop where those
tools do exist; do not reach for `gh` even if it is there, so the steps behave the same
everywhere. Read the template's `CLAUDE.md` as soon as you have fetched it (step 3); everything
there holds for the new app from its first screen.

## 1. Two questions, then no more

Ask these two, together, in one message, and nothing else:

1. **What is the app called?** The name goes in the top bar and the browser tab and becomes the
   repository name (the name in lower case, spaces to hyphens, letters, digits and hyphens
   only; "Tool crib" becomes `tool-crib`).
2. **What is it for, in a sentence or two?** Who uses it and what they need to see or work out.
   This is the brief for the first screen and the app's description.

Do not ask about the look, the theme, colours, density, hosting or an address. Every one of
those has its answer already: the operational theme, the standard GitHub Pages address, public,
no commercial transactions and no software-as-a-service (`CLAUDE.md`, "What may not be built
here"). If the request is one of those forbidden kinds, say so plainly before creating anything
and stop.

If the person has already given the name or the purpose in their message, do not ask for it
again; ask only for what is missing.

## 2. Create the repository, empty

Create `livetools-dev/<name>` as a public repository with the purpose as its description,
through whatever GitHub tool this session has (a GitHub connector's create-repository call, or
`gh repo create livetools-dev/<name> --public --description "..."` where `gh` exists). Empty:
no README, no first commit. If the name is taken, the creation fails: tell the person that name
is already in use and ask for another; never invent a suffix.

If creating a repository is refused for lack of permission, stop and tell the person in plain
words that a developer needs to create the app's repository for them, and give the developer
(in your reply, not to the person) the name, the purpose, and this skill's steps 2 to 5.

## 3. Put the template in it, without the example screens or the publishing file

```
git clone --depth 1 https://github.com/livetools-dev/livetools-app-vite.git template
git clone https://github.com/livetools-dev/<name>.git app
```

Copy every file of `template` into `app` except `.git` and except `.github/` (the publishing
workflow lives there, and this session's login cannot push a workflow file; the ops repository
adds it in step 5). Then take the tool crib out, because the new app starts from the person's
request and not from a stock example:

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

This is the real work, and `CLAUDE.md` governs it. In `app`, run `npm install`, then read
`node_modules/@livetools/ui/PARTS.md` and choose the parts that do what the purpose asks: a
table for a list of things, a filter above it when the list is long, a form or a dialog for
entering something, number fields with a measure for anything with a unit, readouts for a
calculated result, cards for a set of things looked at one at a time, a wizard for a task in
steps. Choose from the parts, never from a memory of another design system, and never build a
stand-in from raw elements. One screen, at `/`, that does the first useful thing the purpose
describes; a second screen only if the purpose plainly has two separate jobs. Seed data, if the
screen needs some to be worth looking at, is small, plausible and in `src/data/`, and the only
supplier or brand names allowed in it are Evolute, NS Tools, Palbit and PH Horn.

Run `npm run check` and fix every finding by using the right part. Commit as
"<Name>: the first screen" and push to `main`. If the push is refused because of a workflow
file, `.github/` was copied by mistake: remove it and push again.

## 5. Ask the ops repository to switch publishing on

The new repository now has the app but no publishing workflow and no Pages setting. The
private repository `livetools-dev/livetools-ops` does both when a request file is pushed to it:

```
git clone --depth 1 https://github.com/livetools-dev/livetools-ops.git ops
```

Write `ops/requests/<name>.md` holding one line, the purpose, commit it as
"Set up <name>" and push to `main`. That runs the "Set up a new app" workflow there, which turns
Pages on for the new repository and adds the template's publishing workflow to it, and that
addition starts the first publish.

Then wait for it without any GitHub tool: every twenty seconds, for up to fifteen minutes,
fetch `https://livetools-dev.github.io/<name>/` and stop when it answers 200 with the app's
title in the page. Between fetches you can see the ops step has happened by
`git -C app fetch` and checking that `.github/workflows/pages.yml` now exists on `origin/main`.

If fifteen minutes pass with no site: pull `app` to bring the added workflow in if it came, and
tell the person that the app is built and in its place but is not showing yet, that a
developer has been asked (say it in your reply, plainly, for the developer: run the "Set up a
new app" workflow in `livetools-dev/livetools-ops` by hand with the repository's name, or if that
repository's token is missing, turn Pages on with the "GitHub Actions" source and add the
template's `.github/workflows/pages.yml` to the repository), and that you will check again when
they say it is done. Do not paste commands or file names to the person.

## 6. Tell the person

When the site answers, tell them, in their words:

- the address, and that it is live;
- what the first screen shows and does, in screen terms;
- that they can now ask for changes and each one goes live a minute or two after you make it;
- one question about the next thing they want, if the purpose left an obvious gap.

No file, folder, command, library or setting is named in that message. From here on, work in
the new repository (`app`), never in the template, and delete the `template` and `ops` folders.

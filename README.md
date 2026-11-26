# Kleia skills

Agent Skills for [Kleia](https://kleia.dev), the knowledge base built on the Open Knowledge Format (OKF).

| Skill | What it does |
|---|---|
| `kleia-okf-setup` | Turns an existing docs folder into an OKF v0.2 Knowledge Bundle without changing what the docs say. It works in three stages, Analyze, Act and Validate, and asks for your go-ahead at each one. |
| `kleia-okf` | Keeps that bundle correct as the docs are edited. It adds frontmatter to new documents, updates changed ones, fixes links to moved ones, and keeps the folder indexes and the update log current. On request, it also improves the Markdown: headings, links between pages and citations, without changing a word. |

## Install

```sh
npx skills add kleiahq/kleia-skills
```

The installer asks which agent to install the skills for, and whether to install them for one project or for every project.

The skills need [Node.js](https://nodejs.org) 20 or later. They install nothing else: each skill carries one bundled script.

## Use

Ask your agent, for example: *"Set up our docs for Kleia."* The setup skill takes it from there, and every step waits for your approval.

Afterwards, agents use `kleia-okf` whenever they edit the docs. Setup can add a line to `AGENTS.md` that tells them to.

## What the skills write

- OKF frontmatter, folder `index.md` files and a `log.md`, all inside your docs folder.
- `.kleia-okf/vocabulary.yml`: the types and tags you agreed on. Commit it with your docs.
- `.kleia-okf/work/`: working files, which git ignores.

They never change the words of a document. The only edits to its text are Markdown markup: a link to a document that moved, and the Improve edits you say yes to. They make no git commits and no network calls.

## About this repository

This repository is generated from the Kleia source, and changes made here are overwritten. To report a problem or suggest a change, contact the Kleia team through [kleia.dev](https://kleia.dev).

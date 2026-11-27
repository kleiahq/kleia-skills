# Improve

Improve makes a bundle read better in Kleia without changing what any page says. Every edit is Markdown markup. The script checks each one and refuses it when a word would change, so you never edit a page's text yourself to get an improvement in.

## The kinds

The scan numbers only the kinds that have edits, I1, I2, …, in this order.

**Drop a first heading that repeats the title.** Kleia shows the title above the page, so `# Late data` under a page titled "Late data" is read twice. The heading line goes, with the blank line after it.

**Fix heading levels.** Kleia shows the title as the page heading, so the text's headings start one level below it (`##`) and never skip a level. A page that uses `#` for its sections has every heading moved down one level; a `###` straight under the title becomes `##`. Only the number of `#` changes. Headings in code blocks stay.

**Fix broken links.** A link to a page that isn't there is fixed only when exactly one page fits:
- the same path with `.md` added: `(setup)` → `(setup.md)`;
- the same path in another letter case: `(Errors.md)` → `(errors.md)`.

A page with the same file name in another folder is no fit: it is often a different page. Any other broken link stays as it is, and `check` keeps listing it. Tell the user which ones, and let them decide.

**Link pages that name each other.** The first time a page names another page by its exact title, the name becomes a link: `rotate API keys first` → `[rotate API keys](rotate-api-keys.md) first`. Only titles of two words or more count, never in code, headings or existing links, at most 5 new links per page, and never to a page it already links to.

**Cite outside links.** A link to another website becomes a declared source: an entry in `sources` (its `id`, the URL as `resource`, the link text as `title`), a footnote reference right after the link, and a footnote at the end of the page that repeats the link text. Kleia then lists it under **Sources**. The user's own `sources` stay, and nothing is fetched.

## Judging new links and sources

A script can't tell a mention from a coincidence, or a source from a pointer. Before you show the user the report, read each new link and source the scan lists, in its sentence:

- **A new link: keep it** when the sentence means the other page: "To begin, rotate API keys first" next to a page titled "Rotate API keys". **Drop it** when the words only happen to match: "the local setup of the server" next to a page titled "Local setup" about developer machines.
- **A new source: keep it** when the link backs a statement in the sentence: a specification, a standard, a study, the documentation a fact comes from. **Drop it** when the link is only a way to get somewhere: a download, an install page, a tool's or product's home page.

Drop with one line each: `"guides/start.md" -> "guides/rotate-api-keys.md"`, or `"agents/cli.md" -> "https://pypi.org/project/kleia/"`. When unsure, drop it.

## Asking the user

Show the report (`.kleia-okf/work/improve.html`): one card per kind with what it does, how many pages, and examples before and after. Then ask one short question per kind, with your recommendation. For example: "I1: drop the repeated first heading on 12 pages? (recommended)".

- A yes or no per kind is enough. If they want a page left out, pass it with `--skip`.
- Never apply a kind they didn't say yes to, and never treat a silence as a yes.
- If they ask for an edit that isn't one of the kinds (rewording, a new section, a fact checked), say that Improve doesn't change words, and that it's theirs to write.

## After apply

Each improved page gets a fresh `generated` with the user's git name: they approved the edit. The log gets one **Improved** entry. Then ask the one question apply prints, exactly as Maintain does. A yes records the user's verification.

When apply refuses:
- **changed since the scan:** a page was edited after the scan. Run `improve scan` again, and show the user the new report.
- **would change its words:** tell the user which page and kind. That page is left out; nothing was written.

## What Improve never does

- Change, add or remove a word of prose.
- Find new sources, check a fact, or fetch anything.
- Touch links that leave the docs folder, or the user's hand-written index pages.
- Commit. Committing is up to the user.

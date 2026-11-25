# Stage 2: Act

Goal: the approved plan written into the docs folder. Every document gets OKF frontmatter, every folder gets an `index.md`, and the root gets a `log.md`. No document's text changes. The user reads about 10 descriptions plus a few flags, then says OK.

Start only when the user says to go ahead after approving the plan.

## 1. Start

Run `act start --plan <id>` with the approved plan id.

It refuses when:
- the plan isn't the approved one;
- a file changed since the approval;
- an `index.md` or `log.md` that Act must write already exists and isn't one Act generated.

In each case nothing has changed. Tell the user what's in the way. For a file in the way, they can rename it themselves, or you go back to Analyze and keep it as the folder index. Act never moves files.

`start` works out each title:
1. the user's own `title` key;
2. otherwise the cleaned file name (`adr-0004-oauth.md` becomes "ADR 0004 OAuth"; a `README.md` takes its folder's name);
3. otherwise you write it, when the name is too weak (`notes`, `untitled`, a bare date).

It also reads the git provenance. You never write `generated` yourself.

## 2. Descriptions

Repeat until none are left:

1. `act next` lists the next 25 documents, with type, title and word count.
2. **Read each file in full.** Not just the outline. Above about 20 KB, read the outline, the start and the end, and every section your description draws on. For a page with little text (`act next` says so), describe only what is on it, or mark it `unsure`; never describe it from what you know elsewhere.
3. Send one line per file to `act describe` (on stdin, or `--text`):

```
"guides/rotate-api-keys.md" = Rotate API keys without downtime, including the order to revoke old ones.
"reference/notes.md" title = Error handling notes
"reference/notes.md" = What each error code means and what to do about it.
"handbook/misc.md" unsure = Mixes on-call rules with a team lunch schedule.
```

A description:
- is one sentence of at most 160 characters, ending with a full stop;
- says what the reader gets from the document;
- doesn't open with "This document…" or similar, and doesn't repeat the title;
- follows every style rule `act next` prints.

Write a title where `act next` says one is needed. You may also correct a title that came from the file name, but only when it says too little: the same as another one in its folder, a single word ("Custom"), or a name with a number ("Release hatch 190"). `act next` marks these. You may always fix its casing ("Http2" to "HTTP/2"). Use a short name for what the page is about, never its first heading. Leave every other title as it is; `act describe` refuses a first heading, and a correction of any other file-name title. The user's own titles stay. Every title you write is flagged in the review.

When every document has a description, `act next` may list titles to check on pages whose description is the user's. Send a new title, or the same title to keep it.

Use `unsure` when you can't tell what a document is for; it becomes a flag for the user. The file still needs a description: describe what is on the page.

`act describe` rejects a line that breaks the rules and says why. Fix it and send it again. **Never show a rejected description to the user.** Files that already have a `description` keep it.

## 3. Write

Run `act write`.
- It backs up each file into `.kleia-okf/work/backup/`, inserts the OKF keys after the user's own, and writes the indexes and the log.
- A folder's `README.md` is its entry point: it is listed first in that folder's index, and the index takes its title as heading.
- Then it checks every file in the folder against its approval fingerprint.
- If anything outside the frontmatter, or a key the user wrote, would change, it puts every file back and stops. Tell the user Act stopped and their docs are as they were.

It also refuses when a file Act wrote has been changed since. Ask the user before writing again.

## 4. Review

Run `act review`. It prints what Act wrote, picks 10 example descriptions across types and folders and at most 5 flag items, and re-renders the report with a **Review** chapter.

In chat, say:
- what Act wrote: the documents, the values it changed as the user decided, and the indexes (name any it replaced);
- that every fingerprint matches.

Then show the examples (title and description, as in an index) and the flags. Always give the report link. Ask for **one OK**, or a comment on style. This is a style check, not a proofread.

When every document kept its own description, there are no examples and no style to check. Still show what Act wrote and give the report link, and ask for one OK.

- **OK:** run `act done`. Open flags are carried into Validate.
- **A style comment** ("shorter", "no 'learn how to'"): run `act restyle --rule "<their words>"`. Every description you wrote is then cleared. Describe every file again under the new rule (step 2), run `act write` and `act review`, and show a new sample.
- **A comment on one file:** send that file's new line to `act describe`, then run `act write` and `act review`.

Flags never block. Don't ask about each one.

## 5. Done

After `act done`, tell the user Act is done and what's next: Validate. Start it only when they say so. Don't commit; that's up to the user.

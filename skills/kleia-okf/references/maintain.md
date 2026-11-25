# Maintain: the rare cases

Read this when `maintain scan` or `maintain write` shows one of these cases.

## A moved or renamed document

`scan` sees a move when a generated index lists a path that is gone and an unlisted document has the same text. `write` then:
- fixes every link to it in other documents, keeping the anchor (`#section`);
- fixes the document's own relative links when it moved to another folder;
- lists it in its new folder's index, and adds a "Moved" bullet to the log.

Those link targets are the only body text the skill ever changes.

If the text changed in the same move, `scan` can't match it. It shows the old path as **deleted** and the new one as **new**. Tell the user so, and handle both as below.

## A deleted document

`scan` lists the documents that still link to it. The script never fixes those links: the right fix depends on the text.
- Tell the user which documents link to it.
- Propose one fix per link: point it at the document that replaced it, or remove the link and keep its words.
- Make an edit only when the user agrees. Your edit is then a change like any other, so run the loop again.

`check` reports these links as `okf.link.broken_local` until they are fixed.

## No type or tag fits

Use the closest type or tag when one is reasonable. When none fits, ask the user in one question. Name the type or tag you propose, give its definition and test question (or its meaning), and say which documents need it.
- **On a yes:**
  - `vocabulary add-type --name <Name> --definition "<what it is for>" --test "<yes-or-no question>"`;
  - `vocabulary add-tag --name <kebab-case> --meaning "<one line>"`.
- **Otherwise,** use the closest existing one.

A tag can't repeat a type's name. The vocabulary holds at most 8 types. The user commits `.kleia-okf/vocabulary.yml` with the docs.

## A folder with a hand-written index

Setup may have kept an `index.md` that the user wrote. The script never writes to it. When a new document or subfolder lands in that folder, `write` names it. Tell the user to add it to their index if they want it listed.

## A folder that is new, or empty

- **A new folder with documents** gets a generated `index.md`, and its parent's index lists it.
- **A folder whose documents are all gone** loses its generated index.
- **A hand-written index** stays as it is.

## Documents the script leaves alone

A document whose frontmatter isn't YAML (TOML `+++`, JSON) or is broken is listed as left alone. Mention it to the user once. Fixing it is their call.

## `generated`, and outside git

- **`generated`** records the last person who changed the document's text: the author of the commit that changed it, or, while the change isn't committed, the person `git config user.name` names. It never names you.
- **Committing.** Once the user commits the run's work, the next `scan` finds nothing to do. Committing is never a change in itself.
- **Without a git user name**, write leaves `generated` as it was and says so. Ask the user to set the name.
- **Outside git**, there are no `generated` stamps. `scan` then compares only with what the last `write` on this machine left.

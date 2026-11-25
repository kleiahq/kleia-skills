# Stage 3: Validate

Prove the folder is a valid OKF bundle, the way Kleia reads it. Show the trust it carries, then finish setup. Start this stage only when the user says so after Act.

## 1. Check

Run `check`. It reports problems with Kleia's own codes and severities:
- **error:** Kleia can't read part of a document, or it breaks OKF;
- **warning:** something works worse than it should (a broken link, a wrong shape);
- **info:** worth knowing (a draft, a title repeated as the first heading). Say "info", as the report does.

During setup, each finding is marked **caused by the pivot** or **already in the docs**. `check` exits 1 on any error.

**Errors the pivot caused block finishing.** Fix their cause; never fix them by editing a document's text.
- `kleia-okf.integrity.changed`: a file changed after the user approved the plan, beyond what the pivot may change. Tell the user which file. If they changed it themselves, that's theirs to keep: run Analyze again for that folder.
- `kleia-okf.trust.*`: the pivot wrote an email, a future time, or a verification the user didn't give. That should never happen: tell the user, and don't finish.
- Any other error the pivot caused: tell the user what `check` says and what you suggest. Don't change their files without their yes.

**Problems already in the docs** are only listed. Setup leaves them as they are. Kleia shows them too, and `check` fails on their errors until someone fixes them; say so plainly.

## 2. Show the results

Run `report`. It adds two chapters at the top: **Results** and **Trust**. Show it as in Analyze: `serve` and `wait` where there is a browser, otherwise the report file.

In chat, say in a few lines:
- whether the folder is a valid OKF bundle (no errors);
- how many problems the pivot caused (it should be none), and how many were already there;
- that a document counts as verified only when the user says they reviewed it.

## 3. Offer what can be fixed

`report` prints **what the user can do**: one item per kind of problem, errors already in the docs first, numbered R1, R2, …, and the Results chapter shows the same list. Each item has a kind:
- **fix:** you can do it now, after a yes. Offer each as one short choice with your recommendation, for example: "R1: 4 integration guides got descriptions that read almost the same. Shall I rewrite them so each says what sets its page apart? (recommended)".
- **look:** one line pointing to the report. Change nothing unless the user asks.
- **later:** one line saying why setup doesn't change it and where it can be done (the user, or the optional Improve step). Group these into a single short paragraph.

Offer fixes before anything else, and before the user verifies a folder: Act refuses to rewrite a file that changed after it wrote it, and `verify` changes files.

On a yes to a description fix:
1. Read each file again, and write a new description that says what sets that page apart. The rules from Act hold.
2. `act describe` with the new lines, then `act write`, `act review` and `act done`. Don't show the 10 examples again; this is not a new style check.
3. `check` and `report`, then tell the user in one line what changed.

An answer about a page's purpose (an unsure flag) is the same: rewrite that description from what the user said. Never change a document's text or the user's own keys to fix anything here.

## 4. Verify, only on the user's word

When the user says, in their own words, that they reviewed a folder or a type, run `verify --folder <dir>` or `verify --type <name>` for exactly that scope. Then confirm in one line how many documents were recorded, and under which name.

- Never offer to verify, never suggest it, and never verify a scope the user didn't name. Reading the descriptions in Act is not a review.
- `verify` refuses when git has no `user.name`. Ask the user to set it; don't make one up.
- After a `verify`, run `report` again so the Trust chapter is current.

## 5. Finish

Ask one question: **"Should agents keep this up to date from now on?"** Explain in one sentence: a yes adds one line to `AGENTS.md` telling agents to use the `kleia-okf` skill for this folder.

Run `finish --agents yes` or `finish --agents no`. It writes `.kleia-okf/vocabulary.yml` (the rules Maintain follows) and `.kleia-okf/.gitignore` either way, and the `AGENTS.md` line only after a yes.

Then give one summary:
- the folder is a valid OKF bundle, with how many documents;
- problems that were already there, if any, with their count. When some are errors, say plainly that `check` fails on them, so CI would fail on every pull request until they are fixed (`finish` prints a warning);
- the CI snippet `finish` printed, for the user to copy if they want `check` on every pull request, with its CI note (the skill folder must be in the repository for CI to run it). Never install it yourself;
- what to commit: the docs folder, `.kleia-okf/vocabulary.yml`, `.kleia-okf/.gitignore`, and `AGENTS.md` after a yes. `.kleia-okf/work/` stays out of git;
- **commit when ready**. You don't commit.

# Local Pages Pipeline — Architecture &amp; Known Bugs

This is the exact n8n workflow pattern used across every site so far. Clone it for new sites rather than designing from scratch.

## Workflow family (per site, prefixed with the site's short code)

| Workflow | Trigger | Job |
|---|---|---|
| `<SITE>_01_Draft_Generator` | Scheduled (daily) | Reads next unprocessed queue row → Claude generates content → creates WordPress draft → marks row `Drafted` |
| `<SITE>_05_Publish_Approval` | Scheduled (after 01) | Batches all `Drafted` rows into ONE Telegram message → Oscar approves → marks `Approved` |
| `<SITE>_02_Publisher` | Scheduled (after 05) | Takes `Approved` rows → flips WordPress status to `publish` → marks `Published` |
| `<SITE>_04_Internal_Linking_Engine` | Scheduled, separate cadence | Goes back through already-published pages, adds the 5+ internal links (needs sibling pages to exist first, so this runs after, not during, drafting) |
| `<SITE>_06_Error_Handler` | Error workflow (not scheduled) | Every workflow above should reference this as its error workflow. Build once, wire everywhere. |
| `<SITE>_07_Blog_Generator` | Scheduled (daily) | Separate evergreen blog content, same Draft→Approve→Publish shape as the local pages |

Queue sheet (Google Sheets, not Airtable/Data Table): one row per city × topic × language combo. Minimum columns: `city`, `topic`, `language`, `status`. Add site-specific columns as needed (e.g. real estate sites might add `neighborhoodName`, `parentCity`).

## Known bugs already found in production — check for these before calling a new build done

**Bug A — Duplicate Telegram messages.** If a `splitInBatches` loop feeds directly into a Telegram send node on its "done" output with no gate, the message fires once per item processed in that run instead of once total. Fix: insert a "Limit to 1" node before any summary/digest Telegram send that follows a loop.

**Bug B — Literal `\n` instead of a real line break.** Shows up as backslash-n as visible text in the Telegram message instead of an actual newline. Fix: use real newline characters in the node parameter, not an escaped string.

**Bug C — Auto-assigned credentials pick the wrong one.** When `create_workflow_from_code` or `update_workflow` auto-assigns a credential by type (e.g. any `telegramApi` or `githubApi` credential), it sometimes picks the wrong one from several that match — has happened repeatedly with Telegram bots and GitHub tokens across different projects. Always check `autoAssignedCredentials` in the tool response and verify against what you actually intended before publishing; fix with an explicit `setNodeCredential` operation if it's wrong.

**Bug D — Code node has no `URL` global.** n8n's Code node sandbox does not expose JavaScript's `URL` constructor. Any code that does `new URL(someString)` to parse a path will silently throw inside a try/catch and produce zero output with no visible error. Parse URLs with plain string operations (`.replace()`, `.indexOf()`, `.split()`) instead.

**Bug E — Telegram's `parse_mode` defaults unpredictably.** A message containing an underscore (common in URLs, e.g. a Google Sheets ID) can fail with "can't parse entities: can't find end of the entity" if Telegram treats it as Markdown-style italics formatting. Fix: explicitly set `parse_mode: 'HTML'` in the Telegram node's `additionalFields` rather than relying on the documented default — HTML mode doesn't treat underscores as special characters.

**Bug F — Claude's JSON output can silently truncate.** If you ask Claude for a large structured JSON array (e.g. 60-100+ roadmap entries) and the response hits the model's max-output-token ceiling, the response cuts off mid-object with no error — it just stops. Two things to do: (1) set a generous `maxTokens` (16000 is a safe starting point for genuinely large structured outputs), and (2) make the parsing code extract individual complete `{...}` objects via regex rather than requiring the whole outer array to close cleanly — that way a truncation only loses the last unfinished entry instead of the entire result.

**Bug G — `setNodeParameter` on an existing node can silently no-op.** Using the `setNodeParameter` operation with a nested path (e.g. `/parameters/jsCode`) on a node that already exists can write into a dead duplicate key instead of the real parameter — the tool reports success and the workflow validates, but the node keeps running its old logic. Fix: use `updateNodeParameters` with the FULL parameters object for that node instead of a single nested path. Always re-read the workflow after a critical edit to confirm it actually landed, especially for Code nodes.

**Bug H — Draft vs published version mismatch.** A workflow's draft version and its published (`activeVersionId`) version are separate. `execute_workflow` always runs the PUBLISHED version. After any edit via `update_workflow`, you must call `publish_workflow` again before testing, or the test will silently run the old logic.

## Credential naming convention

Name WordPress credentials clearly per site: `"<SITE NAME> Wordpress"` (e.g. `"CHVO Wordpress"`, `"SMHFTM Wordpress"`) — never leave it as n8n's generic default `"Wordpress account"`. With 100+ credentials typically in this n8n account, a generic name is unfindable later and risks getting reused for the wrong site by mistake.

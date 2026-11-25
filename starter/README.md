# The Storm Inbox

You’re an engineer at MonsoonSure Insurance. Overnight rain in Bengaluru has left the property claims team with twelve claims and two field adjusters. Divya, the supervisor, uses the existing dashboard to inspect documents and ownership. To decide who takes the next claim, she still keeps a spreadsheet.

> “At 8:10 a family called about a roof leak. I’m waiting for their photo. The roof claim that came in at 9:00 is ready now. When the first family sends their photo, remember when they first called.”

**Your assignment:** Add a dispatch queue to Divya’s existing dashboard. By the end of the session she should be able to pick the next claim, explain its position and assign it to someone with capacity.

## Start your shift

Open the starter directory in IBM Bob. From that directory, run the following with Node.js 22 or newer. No dependency installation is needed.

```sh
npm test
npm start
```

Open [the claims register](http://127.0.0.1:7991). Click **CLM-003** to see the missing photo. Filter by **Assigned** and open **CLM-006** to find its owner. Both adjusters have one active claim. Five baseline tests cover the existing register. Keep claim selection, details and status filtering working as you extend it.

## Dispatch rules

1. **Eligibility.** A claim is Ready when it has no owner and all its required documents have arrived. Show incomplete, unassigned claims under **Waiting for documents** and name every missing document. Assigned claims stay with their current owner.
2. **Priority.** Order Ready claims by severity (High, Standard, Low), then original filing time (oldest first), then ascending claim ID. Show the severity and filing time alongside each ID so Divya can explain the order.
3. **Assignment.** Divya selects a Ready claim and an available adjuster. Each adjuster can hold two active claims, including existing assignments. All assigned claims in this exercise count as active. On success remove the claim from Ready, show its owner, and update the workload. Keep the accepted state on the server so both tabs read the same desk.
4. **Stale views.** Recheck eligibility, ownership, availability and capacity on the server when accepting an assignment. If a second request arrives after circumstances have changed, decline it, explain why, and bring that tab up to date. Two near-simultaneous requests for the last slot must result in exactly one assignment.
5. **Morning update.** Add a **Load storm update** action that applies the two events in the supplied [fixture](fixtures/storm-update.json). At 10:30, the roof photo arrives for CLM-003. At 10:35, Meera becomes unavailable. Keep the original filing time and every existing owner. Label Meera’s assigned claim **Owner unavailable**. Ravi remains available while he has capacity.
6. **Refresh and replay.** Keep accepted changes across page refreshes. Applying the same update again must produce the same queue, documents, workloads and owners. A single local server process is sufficient. You may use in-memory state for the workshop.

## Show Divya the result

Start this demo with the original twelve claims. Run the update before making any new assignments.

| Action | Expected result |
| --- | --- |
| Open Ready | Seven claims. CLM-008 and CLM-009 are both High and filed at 09:00. CLM-008 comes first. CLM-002 lists two missing documents. |
| Load the update | Eight ready. CLM-003 leads because its original filing time is 08:10. Meera remains the owner of CLM-006, now marked Owner unavailable. |
| From a tab opened before the update, try assigning CLM-008 to Meera | The server declines. CLM-008 stays ready and Meera still has one claim. |
| Assign CLM-003 to Ravi | Ravi now has two active claims. CLM-008 returns to the top. Nobody can take another assignment until availability or capacity changes. |
| Refresh and replay | Seven ready, two waiting, three assigned. The photo appears once. CLM-003 still belongs to Ravi and CLM-006 to Meera. |

Run the original tests and add tests for priority ties, document arrival, stale or competing assignments, capacity and replay. Demonstrate one test that would catch a plausible dispatch mistake and explain one design decision you made with Bob.

## Find your way around

| Path | Purpose |
| --- | --- |
| `data/desk.json` | Fictional claims, required and received documents, owners and adjusters |
| `server.mjs` | Local HTTP server and read-only starting API |
| `public/claims.js` | Existing document, status and filter logic |
| `public/app.js` | Register, roster, selection and details |
| `public/index.html`, `public/styles.css` | Dashboard layout and styling |
| `fixtures/storm-update.json` | The two events to apply and replay |
| `test/desk.test.js` | Baseline regression tests |

## Optional IBM Bob prompts

Paste one prompt at a time. All business rules and acceptance checks are above.

1. **Explore.** “Run the baseline tests. Trace CLM-003 from the seed through the API to its document list. Where does the existing app calculate status? Stop before editing.”
2. **Plan.** “Read the dispatch rules. Propose the smallest changes that let two tabs share one desk. Explain which checks belong on the server and how to test the last available slot.”
3. **Build the queue.** “Implement Ready and Waiting for documents using the stated priority rules. Preserve claim selection and filters. Test the 09:00 tie and a claim missing two documents.”
4. **Add assignment.** “Implement assignment through the UI and server. Recheck every rule at write time. Test two requests competing for one slot, and show what Divya sees when an assignment is declined.”
5. **Handle the update.** “Apply the supplied storm events, refresh the queue and preserve owners. Test repeated delivery, the stale tab, and a refresh. Walk through the acceptance table and report any gaps.”

# Insurance · The Storm Inbox

A 45–60 minute IBM Bob brownfield workshop.

At 9:15 on a rainy Bengaluru morning, Divya has twelve property damage claims on her screen and two field adjusters available. A family in JP Nagar reported a leaking bedroom roof at 8:10. Their claim is missing a photo. Another family in Koramangala reported roof damage at 9:00 and has already sent everything Divya needs.

> “I can send someone to Koramangala now. But when the JP Nagar photo comes in, I need to remember that family called first. Right now the only place I track that is my spreadsheet.”

You’re an engineer at **MonsoonSure Insurance**. Divya supervises the claims desk. The family filing a claim is the **claimant**. The **assigned adjuster** is the employee who investigates the damage and follows up with the family. **Your assignment is to add dispatch to the existing claims register** so Divya can see which claim comes next, give it to someone with room, and respond when the morning changes.

## Open the dashboard

Open the repository in IBM Bob. From the repository root, run the following with Node.js 22 or newer. The starter uses Node’s standard library.

```sh
npm --prefix starter test
npm --prefix starter start
```

Expect five passing tests. Open the [claims register](http://127.0.0.1:7991) and look at the desk Divya starts with.

| What you see | What it means |
| --- | --- |
| Meera Rao and Ravi Kumar each show **1 of 2 active claims** | Each adjuster is already handling one family’s claim and can take one more. |
| **Assigned** shows 2 claims | Open CLM-006 to see Meera listed as the assigned adjuster. Ravi is handling CLM-005. |
| **Awaiting documents** shows 3 claims | Open CLM-003. The family filed at 08:10 and the roof photo is still missing. |
| **Unassigned** shows 7 claims | These families have supplied all required documents. Their claims are waiting for an adjuster. |

## What is missing today?

Select **Unassigned**. The first row is CLM-001, a Standard claim for water damage to a floor. Farther down are two High claims, CLM-008 and CLM-009. Divya can inspect all three, but the register leaves her to work out which family should get the next available slot. Once she decides, there’s no action to record the assignment. If a photo arrives or an adjuster becomes unavailable, she has to revise her spreadsheet again.

Extend this dashboard. Keep the existing register, status filter and clickable claim details working.

## 1. Show Divya which claim comes next

Build a **Ready** queue for claims with no assigned adjuster and every required document received. Show incomplete, unassigned claims under **Waiting for documents** and list each missing item. A claim with an assigned adjuster stays with that person.

Order Ready claims by severity (**High**, **Standard**, **Low**), then by original filing time (oldest first), then by claim ID (ascending). Display the severity and filing time beside the ID so Divya can explain her decision.

On the original desk, **CLM-008** should lead. CLM-009 has the same severity and 09:00 filing time, so the smaller ID decides. CLM-003 is older than both and stays in Waiting until its roof photo arrives.

## 2. Assign a claim to an adjuster

Let Divya select a Ready claim and an available adjuster with room. Each adjuster can handle **two active claims**, counting the ones they already have. After a successful assignment, remove the claim from Ready, show its assigned adjuster in the details, and update that adjuster’s workload.

Two browser tabs share one desk. When an assignment arrives, the **server** must check the claim’s documents, current assignment, adjuster availability and remaining capacity. If the desk has changed, decline the request, explain why and bring the tab up to date. If two requests compete for the last slot, exactly one succeeds and the other claim stays ready.

## 3. Handle the mid-morning update

Add a **Load storm update** action that applies the two events in the supplied [storm update file](starter/fixtures/storm-update.json).

| Time | What happens | What Divya should see |
| --- | --- | --- |
| 10:30 | The JP Nagar family sends the missing roof photo for CLM-003. | The claim moves into Ready with its original 08:10 filing time. On the starting desk it moves ahead of CLM-008. |
| 10:35 | Meera calls from a site visit. She can finish the claim she’s handling and cannot take another. | Meera becomes unavailable for new assignments. CLM-006 still lists Meera as its assigned adjuster and shows **Owner unavailable**. Other existing assignments stay intact. |

Keep accepted changes when Divya refreshes the page. Replaying the file should leave the same queue, photo list, workloads and assignments. Keeping state in a single running server process is sufficient for the workshop.

## What to demonstrate

1. Open the original desk in two tabs. Show seven Ready claims, explain why CLM-008 precedes CLM-009, and find both missing documents for CLM-002 in Waiting.
2. Load the storm update in the first tab **before making any new assignment**. Show eight Ready claims with CLM-003 first. Open CLM-006 and show who is still handling it.
3. From the older tab, try to assign CLM-008 to Meera. Show the rejection and the updated desk. Then assign CLM-003 to Ravi. His workload should reach two, and CLM-008 should return to the top.
4. Refresh and replay the update. Show **seven ready, two waiting and three assigned**. CLM-003 has one roof photo and stays with Ravi. CLM-006 stays with Meera.
5. Run the original and new tests. Cover priority ties, missing documents, assignment checks, two requests for one slot, the storm update and replay. Walk through one test and one decision your team made with Bob.

## Where to look

| File | What you’ll find |
| --- | --- |
| `starter/data/desk.json` | Starting claims, documents, adjusters and workloads inferred from assignments |
| `starter/public/claims.js` | Existing rules for missing documents, status and filtering |
| `starter/public/app.js` | The roster, selectable claim rows and details panel |
| `starter/public/index.html` and `starter/public/styles.css` | The dashboard layout and styles |
| `starter/server.mjs` | The local server and its read-only starting API |
| `starter/fixtures/storm-update.json` | The document arrival and Meera’s availability change |
| `starter/test/desk.test.js` | The five baseline tests |

The [facilitator guide](facilitator/README.md) includes timing and demo checkpoints.

## Optional IBM Bob prompts

If you’re new to Bob, paste **one prompt at a time** into the same conversation. Check the result in the app or tests before moving on.

**1. Understand Divya’s desk**

```text
Read this README and run the baseline tests. Trace CLM-003 from starter/data/desk.json to its document list on screen. Explain what “1 of 2 active claims” means for Meera and show me where the existing app decides a claim’s status. Stop before editing.
```

**2. Plan the change**

```text
Plan the three features in this README using the existing dashboard. Identify what we can reuse, where shared desk state will live, and which checks must happen when the server receives an assignment. Give me a short implementation and test plan to review.
```

**3. Build and check the queue**

```text
Implement part 1. Preserve claim selection, details and filters. Add tests for missing documents, severity, original filing time and the 09:00 tie. Run the tests and tell me where to look in the UI to verify why CLM-008 leads and CLM-003 waits.
```

**4. Add assignment and capacity checks**

```text
Implement part 2 from the dashboard through the server. Test a successful assignment and rejected requests for an incomplete claim, an already assigned claim, an unavailable adjuster and a full adjuster. Send two competing requests for Ravi’s last slot and verify only one succeeds. Run the tests and show me how to inspect the resulting workload.
```

**5. Apply the storm update**

```text
Read the supplied events and implement part 3. Test CLM-003 moving into Ready at its original filing time and Meera keeping CLM-006 when she becomes unavailable. Cover a request from an older tab and replay after assigning CLM-003 to Ravi. Run the tests and explain how I can verify the result after refreshing.
```

**6. Rehearse the demonstration**

```text
Review our changes against “What to demonstrate”. Run the full test suite. Identify and fix any missed requirement, then give me a short two-tab demo checklist starting from the original twelve claims. Ask me to explain why CLM-003 moved and why CLM-006 stayed with Meera.
```

# Facilitating The Storm Inbox

Allow about 50 minutes. Participants start with a functioning, read-only register and implement dispatch themselves. For a participant-only distribution, share the complete starter directory.

| Minutes | Activity |
| --- | --- |
| 0–10 | Run the app and baseline tests. Ask someone to locate the 08:10 filing, its missing photo and Meera’s active claim. |
| 10–25 | Teams derive and display the Ready and Waiting lists. Ask which field decides between the two High claims filed at 09:00. |
| 25–40 | Add server-side assignment, capacity and the storm update. Encourage a two-tab check. |
| 40–50 | Replay, refresh, run regression tests and demonstrate the result to Divya. |

## Check the demonstration

The initial Ready order is 008, 009, 001, 004, 010, 007, 012. Waiting contains 002, 003 and 011. Ravi owns 005 and Meera owns 006. Start from the seed with two tabs open and avoid assigning anyone before the morning update.

Apply the update in the first tab. Claim 003 should move ahead of 008 even though its photo arrived later. In the second tab, try assigning 008 to Meera. Check that the decline leaves the claim unassigned. Assign 003 to Ravi in the updated desk. Both existing owners should remain in place. Replay the update and refresh both tabs. Look for seven ready, two waiting and three assigned.

For a separate capacity check, start a fresh desk. Attempt to give Ravi two ready claims in parallel while he has one slot left. Ask the team to demonstrate that only one succeeds and the other remains ready. Teams using in-memory state can restart their server between scenarios. Teams using disk storage should document how to restore the seed.

Close with Divya’s question: “Why did 003 move, and why did 006 stay with Meera?” Ask where the team used Bob and which test gave them confidence in the answer.

# Testing

This document records what was actually tested, by whom, and what was found. It separates **manual tests I performed** from **automated checks I did not perform**.

## Summary

| | |
|---|---|
| App version tested | `index.html` (identical to `2026-10-01-leggee-techops-v7.html`) |
| Date of manual pass | October 4, 2026 |
| Environment | Google Chrome on a Mac (version not recorded), opened as a local file (`file://`), 1 normal window |
| Manual tests | 11 (T0 to T10): **9 passed, 2 failed and accepted as known issues** |
| Issues found | 2 (one P2, one P1), plus 4 minor observations |
| Issues fixed | None. Both issues are documented as known limitations by decision |

**How the manual pass was run.** I performed every click and typed every value myself, in the browser, and reported what I saw. The step-by-step instructions and expected results were prepared by Claude (the AI assistant used to build the app), one step at a time, and I reported the actual result after each step. A result is marked Pass only when I reported it matched. Where I reported "it all worked" without listing exact values, the notes say so.

## Manual test results

| ID | Test | Result | Severity |
|---|---|---|---|
| T0 | Environment and first load | Pass | |
| T1 | Work queue and filtering | Pass | |
| T2 | Start a work order and document checks | Pass | |
| T3 | Connection path | Pass | |
| T4 | Resolve and close | Pass | |
| T5 | Room and equipment history | **Fail (accepted)** | P2 |
| T6 | Equipment inventory and search | Pass | |
| T7 | Receive and set up equipment | Pass | |
| T8 | Swap equipment | **Fail (accepted)** | P1 |
| T9 | Replace a part | Pass | |
| T10 | Persistence and reset | Pass | |

### T0 Environment and first load
- **Purpose:** Confirm the file loads and resets cleanly in the browser I will demo from.
- **Steps:** Open the downloaded file in Chrome. Click **Reset demo data** twice.
- **Expected:** A reset confirmation, the Today's work page, a scoreboard reading 10 To do / 3 High priority / 2 Waiting / 0 Resolved today, the striped sidebar band, and the "SIMULATED ENVIRONMENT" label.
- **Actual:** Reported as matching. Individual scoreboard values were not itemized.
- **Result:** Pass.

### T1 Work queue and filtering
- **Purpose:** Verify the home screen groups and filters work correctly.
- **Steps:** (1) Count rows per priority group. (2) Type `161` in the filter. (3) Choose category Printer. (4) Click By route. (5) Open Resolved and closed.
- **Expected:** High 3, Medium 4, Low 3, Waiting 2. `161` leaves 1 row. Printer leaves 1 row. By route shows wing groups with stop lists. Resolved tab shows 8 rows.
- **Actual:** All five checks reported as passing.
- **Result:** Pass.

### T2 Start a work order and document checks (WO-1042)
- **Purpose:** Verify a technician can start a job and record what was checked.
- **Steps:** Open WO-1042. Start work. Type a result for the first two checks and tick them. Add a custom step. Read History.
- **Expected:** Status In Progress. "2 of 9 checked." Five History lines (reported, started, two checks with results, added step).
- **Actual:** "2 of 9 checked" and History matching were reported.
- **Result:** Pass.
- **Notes:** Whether the page jumps to the top when ticking a box was not reported.

### T3 Connection path
- **Purpose:** Verify the physical-path view and problem marking.
- **Steps:** Confirm the five points. Click Patch panel to mark it. Click again to clear. Click again to re-mark.
- **Expected:** Laptop `LEG-LT24161`, USB-C hub (DA310, `LEG-24318`), Wall jack `161-A`, Patch panel `IDF-B PP2-14`, Network switch port `IDF-B-SW02 Gi1/0/14`. Marking turns the point red with "Problem found here", shows a confirmation, and writes a History line. Clearing and re-marking behave the same way.
- **Actual:** All five points were present. Marking, clearing and re-marking worked as expected.
- **Result:** Pass.
- **Notes:** I also clicked the USB-C hub point once during testing, which logged a "Problem found at USB-C hub" event. That is expected behavior.

### T4 Resolve and close
- **Purpose:** Verify the work order lifecycle and its guard rules.
- **Steps:** Click Resolve with an empty Result. Fill in What I found, What I did and Result, then Resolve. Close (box ticked). Reopen. Resolve again. Close without ticking the confirmation. Tick and Close. Review the queue.
- **Expected:** An empty Result is blocked with a message. Resolved shows the confirmation checkbox. Close is blocked without confirmation. Closed shows only Reopen and locks the fields. The queue shows 9 To do / 2 High / 2 Waiting / 1 Resolved today, with WO-1042 first in Resolved and closed.
- **Actual:** All as expected. A screenshot of the Resolved and closed list confirmed the scoreboard and the 9 rows.
- **Result:** Pass.
- **Notes:** On the first attempt I ticked the confirmation before clicking Close, so the blocked-without-confirmation rule was retested after Reopen and passed.

### T5 Room and equipment history
- **Purpose:** Verify service history is shown on the room and device pages.
- **Steps:** Open Room 161. Open laptop `LEG-24161`. Read its History.
- **Expected:** Room 161 shows WO-1042 events newest first and 7 pieces of equipment. The laptop shows its facts row and its full history, including the RAM upgrade and "Received and tagged" at the bottom.
- **Actual:** Room 161 and the laptop facts were correct. The History list showed exactly 25 lines, and the oldest records (RAM upgrade, deployment, receipt) were missing.
- **Result:** **Fail (accepted).** See ISSUE-1.
- **Severity:** P2.

### T6 Equipment inventory and search
- **Purpose:** Verify inventory lookup and filters.
- **Steps:** Search the serial `7HQK2X3`, the IP `10.58.21.61`, `bessey`, and `zzzz`. Filter by kind Printers. Filter by status In Repair.
- **Expected:** 1 row; 1 row; 4 rows (laptop, monitor, USB-C hub, keyboard and mouse); the no-match message; 4 printers; 2 In Repair (`LEG-24102`, `LEG-25317`).
- **Actual:** All matched exactly, including tags, models, rooms and statuses.
- **Result:** Pass.

### T7 Receive and set up equipment (WO-1053)
- **Purpose:** Verify the full equipment lifecycle and its guard rules.
- **Steps:** Open WO-1053 and Receive equipment. Try a duplicate serial. Receive a laptop with serial `9ZZ1X44`. Try a wrong serial, then the right one. Apply the software profile. Try a bad MAC, then a missing jack/switch/port, then a valid network save. Assign to Kristi Wise, Room 195. Try to deploy with one check unticked, then deploy.
- **Expected:** Each bad input is refused with a plain message. The valid path ends with the device Deployed, and every step writes a History line.
- **Actual:** All guards worked: duplicate serial refused (naming `LEG-24161`), wrong serial refused, bad MAC refused, missing jack/switch/port refused, incomplete checklist refused. The new device `LEG-30527` ended Deployed in Room 195 assigned to Kristi Wise, with correct Connection, Hardware, 6-app Software list and Network sections. The device History had the six expected lines. WO-1053 showed "Equipment received 1" and its own 7-line History.
- **Result:** Pass.
- **Notes:** The status chip on WO-1053 and some green confirmation messages were not reported. The new laptop's connection path shows 4 points (no hub) because nothing links it to a hub. That is a design limitation, not a defect.

### T8 Swap equipment (WO-1045)
- **Purpose:** Verify a hardware swap keeps inventory accurate.
- **Steps:** View the Repair section. Swap `LEG-23552` for `LEG-30508`. Check the new monitor's page. Click Swap a second time.
- **Expected:** First swap: the new monitor takes over Room 152, Carl Isonhart and his hub; the old monitor goes to In Repair in Room 107; both record the swap. Second click: should be refused.
- **Actual:** The first swap was correct on every point. The second click was **accepted**: it reported "Replacement LEG-30509 assigned to Room 107", changed the "Replaced by" line to `LEG-30509`, and `LEG-30509` then showed as Deployed in Room 107 with no assignee.
- **Result:** **Fail (accepted).** See ISSUE-2.
- **Severity:** P1.
- **Notes:** This problem was first identified by Claude through code review and reproduction in its own test copy, and then confirmed by my manual click. Whether `LEG-30508` stayed in Room 152 after the second swap was not checked manually.

### T9 Replace a part (WO-1052)
- **Purpose:** Verify part replacements are logged and update specs.
- **Steps:** Start WO-1052. Replace RAM (16 GB to 32 GB). Replace Motherboard (Original board to Replacement board, same model).
- **Expected:** Confirmation messages; What I did gains both lines; Specs becomes `Core i5-1345U, 32 GB RAM, 256 GB SSD` and is unchanged by the motherboard entry; History gains both lines.
- **Actual:** Matched. The motherboard History line was confirmed after the refresh in T10.
- **Result:** Pass.

### T10 Persistence and reset
- **Purpose:** Verify saved data survives and Reset restores the starting state.
- **Steps:** Refresh. Close the tab and reopen the file. Click Reset demo data. Check the scoreboard, the received laptop, the next tag counter, and `LEG-30509`.
- **Expected:** Work survives a refresh and a close/reopen. After Reset: scoreboard 10 / 3 / 2 / 0, `LEG-30527` gone, next tag `LEG-30527`, `LEG-30509` back to In Stock in Room 107.
- **Actual:** All matched. The scoreboard after close/reopen read 9 / 2 / 2 / 1, and the received laptop was still present.
- **Result:** Pass.
- **Notes:** After Reset, I reported "it all worked" without itemizing every value.

### Workflow coverage

| Workflow | Covered by |
|---|---|
| Work queue and filtering | T1 |
| Opening and working a work order | T2 |
| Troubleshooting and documenting checks | T2 |
| Problem in the connection path | T3 |
| Resolving and closing | T4 |
| Inventory and search | T6 |
| Receiving and setting up equipment | T7 |
| Assigning equipment to a room and person | T7 |
| Swapping equipment | T8 |
| Replacing a hardware part | T9 |
| History and service events | T5, T7, T8, T9 |
| Reset and persistence | T10 |

## Automated checks (reported, not performed by me)

During development, the AI assistant that generated the code ran **78 scripted click-through checks** in headless Chromium using Playwright, with **zero console errors**, and reviewed screenshots at 1280, 1366, 1440 and 900 pixels wide. I did not run these. The script is not included in this repository.

**What they show:** scripted paths through most screens produced the expected stored state, and the page raised no JavaScript errors in Chromium at desktop sizes.

**What they do not show:** whether the interface is clear to a person, whether anything looks wrong, behavior in other browsers or on other devices, or correctness on any path the script did not take. The two issues below passed through all 78 checks undetected.

### Covered by automated checks only (not manually tested by me)
Creating a new work order, the Waiting status, the Staff list and staff pages, moving a staff member, the Locations list, renumbering a room, moving and retiring equipment, deploying a Chromebook to a cart (WO-1051), the global Search page, the "page not found" screen, the skip-to-content link, and layouts narrower than a laptop window.

## Issues

| ID | Severity | Description | Status |
|---|---|---|---|
| ISSUE-1 | P2 | Device History shows only the latest 25 events, and Room and Staff History show the latest 12, with no notice that older records are hidden. The records are stored; only the display is cut off. | Known limitation. Press Reset before a demo. |
| ISSUE-2 | P1 | After a swap, the Swap equipment box stays on the page. Clicking it again swaps a second spare in: that spare is marked Deployed in Tech Storage with no assignee, and the work order's "Replaced by" line points at the wrong device. | Known limitation. Click Swap once only. |

### Minor observations
- **OBS-1 (P3):** The starting data for WO-1045 shows 2 checks ticked but only one "Checked" History line.
- **OBS-2 (P3):** On an equipment page, the Software list sits well below the Equipment setup box and is off-screen on a laptop window.
- **OBS-3 (design limitation):** A newly received laptop shows 4 connection points because it has no hub linked.
- **OBS-4 (not verified):** After a second swap, I did not check whether the first replacement monitor remained in Room 152.

## Remaining issues and limitations
- ISSUE-1 and ISSUE-2 above, unfixed by decision.
- The barcode on the equipment tag label is decorative and does not scan.
- Saved data is kept per browser. After loading a new version, press Reset demo data.
- Desktop only. There is no phone layout.
- The Equipment list shows up to 300 rows.
- Not tested in browsers other than Chrome.

# Testing

This page explains how I checked that the important parts of Leggee Tech Ops work, what I found, and what I did not check. It keeps what I tested myself separate from what an automated tool checked without me.

## The short version

- I tested by hand 11 times, walking through a technician's workflows in Chrome on a Mac on October 4, 2026.
- **9 tests passed. 2 found real problems.** I wrote both up instead of fixing them.
- Separately, 78 automated checks were run during development by Claude, the AI assistant I built the app with. I did not run those.
- Neither kind of testing covers everything. The list of what I did not test by hand is below.
- No problems were fixed after testing. Both are documented as known limitations.

| Test | What I tested | Result |
|---|---|---|
| T0 | The app opens and resets | Pass |
| T1 | The work queue and its filters | Pass |
| T2 | Starting a work order and recording checks | Pass |
| T3 | The connection path | Pass |
| T4 | Resolving, closing and reopening | Pass |
| T5 | History on the room and equipment pages | **Problem found (minor)** |
| T6 | Equipment search and filters | Pass |
| T7 | Receiving and setting up new equipment | Pass |
| T8 | Swapping a device | **Problem found (serious)** |
| T9 | Replacing a part | Pass |
| T10 | Saving changes and resetting | Pass |

## How I tested

### By hand

I opened the app and worked through it the way a technician would, one step at a time. Claude prepared the instructions: what to click, what should happen, and what would count as a failure. I did every click myself, typed the information, and reported what I actually saw. A test counted as a pass only if what I reported matched what was supposed to happen. Where I said "it all worked" without reading out every value, the notes say so.

### With automated checks

An automated check is a program that clicks through an app on its own and verifies the results. During development, Claude ran 78 of them with a tool called Playwright, and no errors were reported. I did not run them, and the script is not included in this repository.

They show that the main paths through the app produced the expected results and caused no errors in a Chrome-type browser on desktop-sized screens. They do not show whether the app is clear to use, whether it works in other browsers or on other devices, or whether a path nobody scripted is safe. Both problems below got past all 78 checks.

## What I checked after the click

A button working is not enough. I wanted to know whether the records stayed correct afterward. In my hand tests I checked these:

- **Swapping a monitor.** The old monitor became "In Repair" and moved to Tech Storage. The new monitor took over the room, the teacher and the USB-C hub connection. The swap was written into the work order's history and onto the new monitor's history.
- **Replacing a part.** Changing the memory updated the laptop's hardware details, and replacing a motherboard left them alone. Both replacements were written into the history.
- **Setting up a new device.** Every step wrote a history entry on both the device and the work order.
- **Closing and reopening.** The status, the buttons and the history followed each change. Closing was blocked until the teacher's confirmation was ticked. After reopening, my typed notes and the marked problem point were still there.
- **Rooms and history.** Room 161 showed the closed work order's history, and the laptop's page showed the right room, person and serial number.
- **Saving.** After a refresh, and after closing and reopening the page, my changes were still there. Reset demo data put everything back to the starting state, including clearing the damage from the second-swap problem below.
- **Mistakes.** The app refused, with a plain message, a duplicate serial number, a serial number that didn't match the label, a badly formatted MAC address, missing wall jack and switch details, an unfinished install checklist, and a work order with no result written.

## What I found

I found two real problems and four small things.

**Problem 1: History list cut-off (minor).** A device's History shows only its 25 newest entries, and room and staff pages show 12. It doesn't say when older entries are hidden. The entries are still saved; only the display is cut off. I found it when the oldest entries on the laptop's page, such as the memory upgrade and the original receipt, were missing after a lot of testing. *Called ISSUE-1 in the log below.*
- Workaround: press Reset demo data before a demo.

**Problem 2: The second swap (serious, but unlikely in a normal demo).** After a swap, the Swap button stays on the page. Clicking it a second time swaps in another spare. That spare then shows as "Deployed" in Tech Storage with nobody assigned, and the work order names the wrong replacement. Claude first spotted this by reading the app's code and reproducing it in its own copy. I then confirmed it by clicking. In Claude's tests, an accidental double-click did not trigger it. It takes a deliberate second click. *Called ISSUE-2 below.*
- Workaround: click Swap once only.
- Not checked: whether the first replacement monitor stayed in Room 152, the room of that work order, after the second swap.

**Four small things:**
1. In the starting data, one work order shows two checks ticked but only one "Checked" line in its history.
2. On an equipment page, the Software list sits well below the setup steps, off screen on a laptop.
3. A newly received laptop shows only 4 points in its connection path, because nothing links it to a USB-C hub. That is how the app is built, not a mistake.
4. I did not check what happened to the first replacement monitor after the second swap (see above).

**How I labeled how serious things are:**
- **Serious (P1):** a record could end up wrong.
- **Minor (P2):** a display problem; the saved data is fine.
- **Small (P3):** cosmetic, or something to improve later.

## What I did not test by hand

These were covered only by the automated checks:
- Creating a new work order
- Putting a work order in Waiting
- The Staff list and staff pages, and moving a staff member to another room
- The Locations list, and renumbering a room
- Moving equipment between rooms, and retiring equipment
- Deploying a Chromebook to a cart
- The Search page
- The "page not found" screen and the skip-to-content link
- Windows narrower than a laptop screen

Also not tested: any browser other than Chrome, any phone or tablet, and the Chrome version number was not recorded.

## The full test log

Each entry says what I did, what should have happened, what happened, and the result.

### T0 The app opens and resets
- **Did:** Opened the downloaded app in Chrome and clicked Reset demo data twice.
- **Should happen:** A reset message, the Today's work page, a scoreboard of 10 to do, 3 high priority, 2 waiting and 0 resolved today, the striped band on the sidebar, and the SIMULATED ENVIRONMENT label.
- **Happened:** I reported everything matched. I did not read out each scoreboard number.
- **Result:** Pass.

### T1 The work queue and its filters
- **Did:** Counted the rows under each priority. Typed `161` in the filter. Chose the Printer category. Switched to By route. Opened Resolved and closed.
- **Should happen:** High 3, Medium 4, Low 3, Waiting 2. `161` leaves 1 row. Printer leaves 1 row. By route groups jobs by wing with a list of stops. Resolved and closed shows 8 rows.
- **Happened:** All five checks passed.
- **Result:** Pass.

### T2 Starting a work order and recording checks (WO-1042)
- **Did:** Opened the Room 161 work order and started work. Typed a result for the first two checks and ticked them. Added my own step. Read the history.
- **Should happen:** Status In Progress. "2 of 9 checked." Five history lines: the report, started, the two checks with my results, and the added step.
- **Happened:** The "2 of 9 checked" count and the history matched.
- **Result:** Pass.
- **Note:** I didn't report whether the page jumps to the top when a box is ticked.

### T3 The connection path
- **Did:** Confirmed the five points. Clicked the patch panel to mark it. Clicked again to clear it. Clicked again to mark it.
- **Should happen:** Laptop, USB-C hub, wall jack 161-A, patch panel IDF-B PP2-14, and switch port IDF-B-SW02 Gi1/0/14, in that order. Marking turns the point red with "Problem found here," shows a message and adds a history line. Clearing and re-marking do the same.
- **Happened:** All five points were there, and marking, clearing and re-marking worked.
- **Result:** Pass.
- **Note:** I also clicked the USB-C hub point once while testing. That correctly added a history line.

### T4 Resolving, closing and reopening
- **Did:** Clicked Resolve with the Result box empty. Filled in what I found, what I did and the result, then resolved. Closed it. Reopened it. Resolved it again. Tried to close without the confirmation ticked. Ticked it and closed. Checked the queue.
- **Should happen:** An empty Result is refused. A resolved order shows the confirmation tick box. Closing is refused without the tick. A closed order shows only Reopen and its fields are locked. The queue shows 9 to do, 2 high priority, 2 waiting and 1 resolved today, with this order first in Resolved and closed.
- **Happened:** All of it matched. A screenshot of the Resolved and closed list confirmed the numbers and the 9 rows.
- **Result:** Pass.
- **Note:** The first time, I ticked the confirmation before clicking Close, so I tested the refusal again after reopening, and it passed.

### T5 History on the room and equipment pages
- **Did:** Opened Room 161, then the laptop `LEG-24161`, and read its history.
- **Should happen:** Room 161 shows the work order's newest entries first and 7 pieces of equipment. The laptop shows its status, room, person, serial and warranty, and its whole history down to the memory upgrade and the original receipt.
- **Happened:** The room page and the laptop's details were right. The history list showed exactly 25 lines, and the oldest ones were missing.
- **Result:** **Problem found (minor, P2).** See Problem 1.

### T6 Equipment search and filters
- **Did:** Searched the serial number `7HQK2X3`, the IP address `10.58.21.61`, the name `bessey` and the nonsense word `zzzz`. Filtered by Printers. Filtered by In Repair.
- **Should happen:** 1 row; 1 row; 4 rows (laptop, monitor, USB-C hub, keyboard and mouse); a "no equipment matches" message; 4 printers; 2 items In Repair.
- **Happened:** All matched exactly, including tags, models, rooms and statuses.
- **Result:** Pass.

### T7 Receiving and setting up new equipment (WO-1053)
- **Did:** Opened the receiving work order. Tried a duplicate serial number. Received a laptop with serial `9ZZ1X44`. Tried a wrong serial number, then the right one. Applied the software profile. Tried a bad MAC address, then missing wall jack, switch and port, then a correct network entry. Assigned it to Kristi Wise in Room 195. Tried to deploy with one check unticked, then deployed it.
- **Should happen:** Each mistake is refused with a plain message, and the correct path ends with the device Deployed and a history entry for each step.
- **Happened:** Every mistake was refused. The new laptop ended Deployed in Room 195 with the right connection path, hardware, 6-app software list and network details. Its history had the 6 expected lines, and the work order showed the laptop under "Equipment received" with its own 7-line history.
- **Result:** Pass.
- **Note:** I didn't report the status label on the work order, or some of the green confirmation messages.

### T8 Swapping a device (WO-1045)
- **Did:** Looked at the Repair section. Swapped the flickering monitor `LEG-23552` for the spare `LEG-30508`. Checked the new monitor's page. Clicked Swap a second time.
- **Should happen:** After the first swap, the new monitor takes over the room, the teacher and the hub, and the old one moves to Tech Storage as In Repair, with both recorded. The second click should be refused.
- **Happened:** The first swap was correct in every way I checked. The second click was accepted: the message said "assigned to Room 107," the work order's "Replaced by" line changed to `LEG-30509`, and that spare showed as Deployed in Room 107 with nobody assigned.
- **Result:** **Problem found (serious, P1).** See Problem 2.

### T9 Replacing a part (WO-1052)
- **Did:** Started the Art Room work order. Replaced the memory (16 GB to 32 GB). Replaced the motherboard (original to replacement, same model).
- **Should happen:** A message each time, both lines in "What I did," the hardware details showing 32 GB and staying that way after the motherboard entry, and both replacements in the history.
- **Happened:** All matched. The motherboard's history line was confirmed after the refresh in T10.
- **Result:** Pass.

### T10 Saving changes and resetting
- **Did:** Refreshed the page. Closed the tab and reopened the file. Clicked Reset demo data. Checked the scoreboard, the laptop I had received, the next equipment tag, and the spare monitor `LEG-30509`.
- **Should happen:** My work survives a refresh and a reopen. After a reset: the scoreboard returns to 10, 3, 2 and 0, the received laptop is gone, the next tag is `LEG-30527` again, and `LEG-30509` is back to In Stock in Room 107.
- **Happened:** All matched. After the reopen, the scoreboard read 9, 2, 2 and 1 and the received laptop was still there.
- **Result:** Pass.
- **Note:** After the reset I reported "it all worked" without listing every value.

### What the manual tests covered

| Workflow | Tests |
|---|---|
| Work queue and filtering | T1 |
| Opening and working a work order | T2 |
| Troubleshooting and recording checks | T2 |
| Marking a problem on the connection path | T3 |
| Resolving and closing | T4 |
| Equipment inventory and search | T6 |
| Receiving and setting up equipment | T7 |
| Assigning equipment to a room and person | T7 |
| Swapping equipment | T8 |
| Replacing a hardware part | T9 |
| History and records | T5, T7, T8, T9 |
| Saving and resetting | T10 |

## Environment

- **Browser:** Google Chrome on a Mac (version not recorded)
- **How the app was opened:** as a file saved on my computer
- **Version tested:** the same app as `index.html` in this repository, which I saved earlier as `2026-10-01-leggee-techops-v7.html`. The live demo link serves the same file.
- **Date:** October 4, 2026

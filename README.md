# Leggee Tech Ops

**SIMULATED ENVIRONMENT • Portfolio Demonstration**

Leggee Tech Ops is a practice school IT support tool that runs in a web browser. A technician can work through repair requests, look up rooms, staff and equipment, troubleshoot a problem, replace a part or swap a device, set up new equipment, and keep a written history of everything that was done.

Every room, device, serial number, IP address and work order in it is made up. It is not connected to any school district's real systems, and it does not represent them.

**Live demo:** https://evan-dawkins.github.io/leggee-tech-ops/

![Work queue](screenshots/01-work-queue.png)

## Why I built this

I applied for the Mobile Technician position at Huntley Community School District 158. The job posting describes two jobs in one: getting hardware and software into service, and keeping an accurate record of equipment while helping troubleshoot problems.

I built this to practice thinking through that work, so I could show how I would approach it instead of just saying it. It follows a problem from the first report to the final fix, and it follows new equipment from the day it arrives to the day it is in use.

It is a practice project. It does not replace real hands-on experience.

## What it simulates

A pretend version of Leggee Elementary School's technology: 72 rooms and spaces, 98 staff members, about 660 pieces of equipment, 12 open work orders and 8 completed ones. Each classroom teacher has a laptop connected to a USB-C hub and a monitor. Classrooms have an interactive display and a document camera. The building has network closets with switches.

| Made up | Real |
|---|---|
| All rooms and room numbers | Staff names and job titles, taken from the public Leggee staff directory (where they work is made up) |
| All devices, serial numbers, IP and MAC addresses, wall jacks and switch ports | Product names, like Dell Latitude 5440 and Cisco Catalyst, used to make it realistic |
| All work orders and history | The kind of work a Mobile Technician does, as described in the public job posting |
| The technician user ("E. Dawkins") | The way the tool behaves |

No email addresses, student information or other personal details are included. The app does not diagnose anything. The troubleshooting list is a record of what the technician checked and what they saw. This project is not affiliated with or endorsed by Huntley Community School District 158.

## What you can do

**Work orders.** Each repair request shows the problem as reported, the room, the person who reported it and the device. It moves through New, In Progress, Waiting, Resolved and Closed. A work order cannot be closed until the fix is confirmed with the person who reported it, and it can be reopened.

**Troubleshooting.** A checklist of things to check, which changes with the type of problem (network, printer, display and so on). You record what you checked and what you saw, then write what you found, what you did and the result. A connection path shows how a device reaches the network: laptop, USB-C hub, wall jack, patch panel, switch port. You click the point where you found the problem.

**Equipment.** Every device has a tag, serial number, model, room, assigned person, status, hardware details, installed software, network details and history. You can search by tag, serial number, hostname, IP address, name or room.

![Equipment record](screenshots/04-equipment-record.png)

**Device setup.** New equipment is received, tagged, checked against its serial number, loaded with software, connected to the network, assigned to a room and person, and checked with the user before it is marked deployed. The app refuses a duplicate serial number, a serial number that doesn't match the label, a badly formatted MAC address, and missing network details.

![Equipment setup](screenshots/05-equipment-setup.png)

**Hardware replacement.** Swap a device for a spare from Tech Storage, or record a replaced part such as a power supply, motherboard, processor or memory. The replacement takes over the room, person and connections. Memory, storage and processor changes update the device's hardware details.

![Repair and swap](screenshots/06-repair-swap.png)

**Locations.** Each room shows who works there, what is installed, what is broken and what happened recently. Room numbers can be changed without breaking the links to its equipment, people and work orders.

![Room 161](screenshots/03-room-161.png)

**Staff.** A person's page shows their room, their equipment, their open work and their history. It is not a contact directory.

**History.** The key actions (starting work, each check, repairs, moves, setup steps and closing) are written down. Each one shows up on the work order, the device, the room and the person it involves.

## A real example: the Room 161 network problem

This scenario is built into the starting data.

Christina Bessey, a 5th grade teacher in Room 161, reports that her laptop has no network connection when it is plugged in at her desk. It is marked High priority.

1. I open the work order. It shows the room, the teacher, the laptop (a Dell Latitude 5440) and what has happened to it before.
2. I start work and go down the checklist: is the link light on, reseat the cable, try a known-good cable, and so on. I type what I see next to each one.
3. The connection path shows the chain from the laptop to the hub, the wall jack, the patch panel and the switch. When I find where the problem is, I click that point to mark it. In the demo I mark the patch panel. The starting data includes a clue: the patch panel was relabeled during a closet cleanup two days earlier.
4. I write what I found, what I did and the result. I mark the work order resolved, confirm with the teacher that it is fixed, and close it.
5. The fix now appears in the history of the work order, the laptop and Room 161.

The problem and its cause are made up for the demo. The app doesn't figure out the cause. The technician decides what was found and types it in.

![Work order with checklist and connection path](screenshots/02-work-order-connection-path.png)

## What this demonstrates

| Duty in the job posting | What the project shows |
|---|---|
| Install hardware and replace parts: power supplies, motherboards, cards, processors, fans, memory, network cards | Replacing a part: record the old and new part, update the device's hardware details for memory, storage and processor, and write it into the history |
| Install and connect monitors, keyboards, printers, disc drives | Swapping equipment: a replacement monitor takes over the room, person and hub connection, and the failed one goes back to Tech Storage marked In Repair |
| Connect hardware to the network | The connection path, and the network step of device setup |
| Load software and applications | Device setup applies a standard software profile, and each device lists what is installed |
| Keep an accurate inventory | Equipment records with tag, serial number, model, room, person, status, hardware, software, network details and history. Receiving checks the serial number and blocks duplicates |
| Help with troubleshooting | Work orders with a checklist, what I found, what I did and the result |
| Communicate with staff | Every work order is tied to the person who reported it, and cannot be closed until they confirm the fix |

It shows how I think through and document the work. It does not show hands-on skills like physically installing a part or working on real network equipment.

## How I tested it

I tested it two ways.

**By hand.** I worked through the app as a technician would, in 11 tests in Chrome on October 4, 2026. Nine passed and two found real problems, which I documented. Claude, the AI assistant I built this with, prepared the step-by-step instructions. I did every click myself and reported what I saw.

**With automated checks.** An automated check is a program that clicks through the app on its own and verifies the results. Claude ran 78 of them during development, using a tool called Playwright, with no errors reported. I did not run these. Both problems I found by hand got past all 78 checks.

The full results, including what I did not test, are in [TESTING.md](TESTING.md).

## Technical details

- **It is one file** that runs in the browser. There is nothing to install, no server and no account.
- **Changes are saved in the browser**, so work orders and equipment changes are still there after a refresh. Each browser keeps its own copy. The Reset demo data button restores the starting state. (The technical term is local storage.)
- **The starting data is the same every time**, so the same scenarios can be demonstrated again and again. Serial numbers and tags never change; only the dates are set relative to today.
- **It tracks five kinds of information:** locations, staff, equipment, work orders and history entries. Each room, person and device has a permanent ID, so changing a room number doesn't break anything linked to it.
- **History is one shared list.** Each page shows the entries that involve it, so one repair appears on the device, the room and the person without being copied.
- **Parts like memory and storage are details of a device,** not separate inventory items. A part replacement updates the detail and writes a history entry.
- **Chromebooks belong to carts, never to students.**
- **It is written in plain HTML, CSS and JavaScript** with no outside libraries or services.

## Known limitations

- **History list cut-off.** A device's history shows only its 25 newest entries (room and staff pages show 12) and doesn't say when older ones are hidden. The entries are saved; they just aren't displayed. This is called ISSUE-1 in TESTING.md.
- **Second swap.** After a swap, the Swap button is still on the page. Clicking it a second time swaps in another spare and leaves that spare marked as in use while it sits in storage. Swap once only. This is called ISSUE-2 in TESTING.md.
- The barcode on the equipment tag label is for looks only and does not scan.
- It is built for desktop browsers. There is no phone layout.
- The equipment list shows up to 300 rows at a time.
- There are no real connections to school systems, no device management, no logins, no multiple users, no charts, no notifications, no printing and no export.

## How to run it

1. Open the live demo above, or download `index.html` from this repository and open it in a web browser (I tested in Chrome).
2. The first time, click **Reset demo data** at the bottom of the left sidebar. Click it twice to confirm.

Your changes are saved in your browser, and each browser keeps its own copy. If the data ever looks out of date, use Reset demo data.

## How it was built

I built this with Claude, an AI assistant from Anthropic. I decided what it should do, how the workflows should work, what had to stay fictional, and how honest the documentation had to be. I directed each round of changes and ran the manual testing. Claude wrote the code and ran the automated checks.

I'm saying this plainly because the point of the project is the thinking about the work and the checking of it, not hand-written code.

## What I learned

- Turning a job posting into a working model of the real work (rooms, people, equipment, problems and history) was harder and more useful than building the screens.
- Passing automated checks did not mean the app was correct. Testing it by hand found two problems the automated checks had missed, including one that could quietly make an inventory record wrong.
- Accurate inventory depends on small rules: unique serial numbers, checking a serial number against the label, recording where a device is and who has it, and keeping a history.
- Honest documentation matters. Keeping what I tested myself separate from what an automated script reported makes the project easier to trust.

# Instructions for your agent: a phone-sized travel document

Paste this into your AI agent along with your booking emails or PDFs. Ask it to build a single, phone-first trip PDF you can open on your phone and use at the airport or hotel desk.

The result should look like a small travel app: one screen per page, large type, a contents list, a timeline, watch-outs, one boarding-pass screen per passenger per flight, and one card per stay. Do not invent facts. If something is missing, say so on the watch-outs page.

---

## 1. Gather the documents

Search only the mailbox or folder the user names. Do not send, reply to, or change any mail.

Look for, and ask the user to upload if they are missing:

- International and domestic tickets or e-tickets (every passenger, every leg)
- Hotel and rental confirmations
- Visa or entry-permit documents
- Travel insurance

Useful search terms: airline or agency names, city or airport names, “itinerary”, “e-ticket”, “confirmation”, “booking”, “PNR”, “reservation”, and the travel dates.

Save source files in one place. Note the message date and who issued each document.

---

## 2. Extract the facts (invent nothing)

For **each flight leg and each passenger**, record only what is on the document:

- Airline, flight number, date, local departure and arrival times
- Airports and terminals (and the three-letter codes as printed)
- Passenger name as printed, plus adult/child if stated
- Seat, if assigned; otherwise write “Not assigned”
- PNR and ticket number
- Cabin or baggage allowance, if shown
- The real barcode or QR on that ticket (see section 5)

For **each stay**:

- Property name, address, and phone only if printed
- Confirmation number, guest name, room type, party size
- Check-in and check-out dates and clock times (local)
- Cancellation deadline and the charge if they cancel after it
- Check-in method (for example “host greets you”) if stated

Times are local unless the document says otherwise. If two sources disagree, keep both and flag the conflict. If a visa, permit, insurance policy, or a seat is not in the files, do not guess — note it as missing.

---

## 3. Put everything in chronological order

Build one trip timeline from the first departure to the last arrival. Interleave flights and stays in the order they actually happen (for example: arrive, check in, later flight, next stay, return flight).

Do not treat a same-day airport transfer as a “gap” if the family is handling it themselves, unless the user asks you to.

---

## 4. Screen list and order

Use a portrait page of **90 × 195 mm** (a 9:19.5 phone aspect, so the page fills a phone when zoomed to width). Keep a single column. Split sections across pages rather than cramming.

A typical trip uses this order (page count grows with the number of passengers and legs):

1. **Contents** (travellers, then tappable rows in trip order; continue on a second page if needed)
2. **Timeline** (one vertical timeline, split across two pages if needed)
3. **Watch-outs** (timing first, then cancellations, visas/permits and insurance)
4. **Each international or first-leg boarding pass**, one page per passenger
5. **First stay**
6. **Each later flight**, one page per passenger per leg, followed by the stay that comes after it
7. **Return flights**, one page per passenger

Every screen has:

- A top bar with the trip name, a **Contents** control, the page number, and **Back** / **Next**
- A bottom tab bar with working links: **Timeline**, **Flights**, **Stays**, **Alerts**
- Working internal links from the contents list to the matching screen

---

## 5. Barcodes

Give **each passenger, on each leg, their own page with a single large barcode or QR**.

- Crop the real code from the source ticket at high resolution (around 10× zoom).
- Trim to the ink, then pad it on a white panel with a quiet zone so it will scan from a phone.
- Do not invent, redraw, or reuse another passenger’s code.
- If a source has one code for the whole booking rather than one per person, use one page per real code and say whose booking it is.
- If a stay has a booking barcode or QR, give that code its own page too. Listing photos, maps and avatars are not booking codes — ignore them.

---

## 6. Design system

Treat each page as an app screen, not a printed A4 sheet.

- **Type:** a good embedded sans (for example Inter). Clear scale: large title, section header, body, caption. Body at least ~14 pt, line-height 1.4–1.5, so it stays at least ~11 pt when the page is zoomed to a phone’s width. Use uppercase, tracked labels for fields such as SEAT, PNR, TICKET, CHECK-IN. Use tabular figures for times and numbers.
- **Colour:** a warm paper-grey background, greyscale text, and one calm accent for primary buttons. Use subtle grey tones to tell sections apart: a cool light grey for flights (about #EEF0F2, chip #E2E5E8), a slightly warmer grey for stays (about #F2F1EE, chip #E4E2DD), and a mid-light grey with a stronger chip and border for watch-outs (about #EAEAEA, chip #D6D6D6). Contents and timeline stay neutral. High contrast, no tiny tables. Airline-specific colour is fine on a real boarding pass (only from the carrier’s real identity).
- **Icons:** one consistent line set (for example Lucide) for plane, hotel, home or rental, calendar, clock, seat, map pin, ticket, warning, person and luggage.
- **Components:** rounded cards, a boarding pass with large airport codes, a plane icon between them, a perforation, labelled fields and a white barcode panel; stay cards with check-in/out time chips, confirmation number, address and a cancellation badge; a vertical timeline with day headers and icon dots; alert cards with icons for watch-outs.
- **Buttons:** pill or rounded-rectangle, filled primary and outlined secondary. They must be real links:
  - **Open in Maps** — a Google Maps search URL for each hotel or rental address, and for each airport and terminal
  - **Call hotel** — only if a phone number is in the source documents, using a `tel:` link
  - **Contents**, **Back**, **Next**, and the tab bar — internal page anchors

---

## 7. Build with HTML/CSS and headless Chrome

1. Lay out every screen as a fixed 90 × 195 mm section in HTML, with one `id` per screen.
2. Use internal `#anchors` for contents rows, Back, Next, Contents and the tab bar.
3. Use real `https://` Maps URLs (and `tel:` only for numbers you extracted).
4. Embed the font files and the cropped barcode images.
5. Print with headless Chrome, no default headers or footers, page size 90 × 195 mm, margins zero, background colours on.
6. Set the PDF title and author metadata. Check that the printed page size is actually 90 × 195 mm and that the page count matches the screen list.

---

## 8. Watch-out rules

Call these out on the watch-outs screens and, where useful, on the relevant stay card:

- An early start when official checkout is after a departing flight (suggest a leave-by time)
- A long gap between a landing and official check-in
- A late flight after a morning checkout
- Every cancellation deadline, in local time
- Visas or entry permits: in progress, still needed, or not found
- Travel insurance: present, or not found in the documents

Do not invent advice (for example luggage storage or transfers) that is not in the sources or was not asked for.

---

## 9. QA checklist

Before you hand the PDF over:

- [ ] Render the contents page, a timeline page, a watch-outs page, one boarding pass per airline and one stay page to PNG
- [ ] Type is legible at phone width; nothing overflows into the tab bar or is clipped mid-word
- [ ] Every barcode is sharp, on white, with a quiet zone, and large enough to scan
- [ ] Page count and chronological order are correct
- [ ] Count the PDF link annotations. Every internal link must jump to the right screen (Contents, Back, Next, tabs, contents rows). Every Maps (and any Call) button must have a working URL
- [ ] No invented names, codes, times, seats, phone numbers or barcodes
- [ ] Missing items are stated, not filled in

---

## 10. Privacy

Keep the user’s documents private. Do not put their files, ticket numbers or personal details into a public sample, a shared folder, or another person’s project. Do not forward or modify their email. When you write a public demo of this format, replace every fact with obviously fictional data and clearly mark the file as a sample.

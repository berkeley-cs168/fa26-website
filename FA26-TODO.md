# FA26 Website TODO

Schedule, config, Ed, and lecture data are done and the site builds clean.
What's left needs information that doesn't exist yet.

Excluded from the Jekyll build via `_config.yml`. Delete before launch.

```bash
grep -rn "TODO(FA26)" --exclude-dir=.git --exclude=FA26-TODO.md .
bundle exec jekyll serve      # preview at http://localhost:4000
```

---

## Done

- `_config.yml` — title, url, slugs, `TuTh, 11:00 AM-12:30 PM`, Dwinelle 155,
  Ed course `103463`, `under_construction: true`
- `_data/syllabus.yml` — `2026-08-26` to `2026-12-03`, Tu/Th, SP26 rowspan geometry
- `_data/lectures.yml` — 29 entries mapped to the 29 Tu/Th class days,
  26 numbered lectures, Thanksgiving and midterm rows in place
- `_data/discussions.yml` — 15 slots, Spring Break entry removed
- `CNAME` -> `fa26.cs168.io`, all filename prefixes -> `fa26`
- All SU26 slides, recordings, Gradescope links, and dates cleared

## 1. Waiting on you

**Zoom + YouTube** — `_config.yml` `heading_links`. Both blank.

**OH calendar** — `_data/calendar.yml` needs `google_calendar_id`,
`google_calendar_embed_link`, `google_calendar_add_link` from the new
`[CS168 FA26] Website` calendar (create as SPA). `google_api_key` already carries
over. `remove_prefix` is already `"[CS168 FA26] "` — keep the trailing space.

**Feedback form** — `_config.yml` `aux_links`.

**Staff photos, emails, and bios.** The FA26 roster is in with names and roles
only, as requested. Everyone renders with `blank.webp`. To finish, add a `photo:`,
`email:`, and a bio body to each file in `_staffers/`. Run every photo through the
`mogrify` script before committing (SU26 kept the whole folder to 332 KB).

Emails already collected, ready to paste into the `email:` field:

| File | Name | Role | Email |
|---|---|---|---|
| `zoir.md` | Zoir Imomaliev | Head TA | zoirimo@berkeley.edu |
| `sam.md` | Sam Son | TA | sam.son@berkeley.edu |
| `tess.md` | Tess Despres | TA | tdespres@berkeley.edu |
| `abhi.md` | Abhi Nambiar | TA | abhijitnambiar@berkeley.edu |
| `mehdi.md` | Mehdi Khfifi | TA | mehdikhfifi@berkeley.edu |
| `riya.md` | Riya Sehgal | Tutor | riya_sehgal@berkeley.edu |
| `sydney.md` | Sydney Dinh | Tutor | sydneydinh@berkeley.edu |
| `valerie.md` | Valerie Eng | Tutor | valerie_eng@berkeley.edu |
| `arnav.md` | Arnav Gupta | Tutor | 12arnavg@berkeley.edu |

Ion Stoica and Sylvia Ratnasamy have no email on file yet.

Also set `spaaccess: True` on whoever has SPA inbox access and `dspdata: True` on
whoever has DSP data access — those render as tags on the staff page.

`assets/staff/` still contains unused SU26 photos (dennis, jonah, lavanya,
matthew, michael, peyrin). Riya's and Arnav's old photos are still there and may
be reusable. Delete the rest when you add the new ones.

## 2. Waiting on other people

**Exam dates** — `_data/exams.yml` shows `TBD`. Finals week is **Mon Dec 14 - Fri
Dec 18, 2026**; the exact slot comes from the exam group for a TuTh 11:00am class
(registrar.berkeley.edu/calendars/final-exam-groups/, or CalCentral once
registration opens). The `Exam Logistics` page is `nav_exclude: true` until the
data exists — flip it back in `exam.md`. The lecture schedule puts the
midterm on **Thu Oct 29**, but the time isn't confirmed and prior semesters ran
evening exams on non-class days. Once Carlos Garcia confirms:

- Fill in the `midterm` and `final` blocks
- If either exam lands on a non-Tu/Th day, add it to `extra_days` in
  `_data/syllabus.yml` (currently `[]`) — that is what creates its calendar row
- Update the lecture titles `No Lecture (Midterm)` and `**Midterm** (Thu Oct 29)`
  if the plan changes

**Gradescope assignment links** — `_data/projects.yml`, every `gradescope:` field.
Course is `1365286`; you need per-assignment IDs:
`https://www.gradescope.com/courses/1365286/assignments/<id>`

**Due dates** — `_data/projects.yml`, every `due:`.

## 3. Needs a content decision

**Project column rowspans.** The column encodes the project timeline as row
counts, inherited from SP26. To make it fit FA26's 58 rows I shrank only the
trailing spacer (18 -> 8). Once due dates exist, rebuild the whole column —
current values are `8, 8, 8, 18, 4, 4, 8`.

**Discussion content needs a review pass.** The column is now structurally correct
(week 1 is Thursday-only at `rowspan: 2`, weeks 2-15 are full weeks at `rowspan: 4`),
but the topics came from SP26 and three weeks look wrong for FA26:

- **Week 10 (Oct 27, Oct 29) is midterm week** - no lecture Tue, midterm Thu - but
  it currently shows `9. ARP, DHCP, NAT`. Probably wants exam prep or no discussion.
- **Week 14 (Nov 24, Nov 26) includes Thanksgiving** and shows
  `13. Wireless and Cellular / Final Minilectures`. Nov 26 is a holiday, and "final
  minilectures" is early when the final is in December.
- **Week 15 (Dec 1, Dec 3) is an empty placeholder** titled `14.` - fill it in or
  label it "No discussion".

Topics also drift from the lectures in the back half, because SP26 ordered a few
lectures differently. For example week 12's discussion is `Host Networking and SDN`
but Host Networking isn't lectured until week 13. Walk the table with whoever owns
discussion content.

**Lecture titles now follow the CSV where topics differ.** Lectures 23 and 24 are
`Beyond Client-Server 1: Overlays` and `Beyond Client-Server 2: AI (Collectives)`,
per the schedule spreadsheet. Their textbook readings still point at the Multicast
and Collectives chapters — confirm those are the right readings for the retitled
lectures.

Other CSV topics are shorthand working notes ("class overview", "bottom-up",
"concepts, DV, LS") rather than student-facing titles, so those kept the SP26
wording. One worth a second look: CSV lecture 7 reads "IP addressing; header; v6"
but the site says `Routing 3: Link-State, Addressing`.

**Lecture numbering differs from the CSV.** The CSV numbers the two midterm rows
as lectures 18 and 19, ending at 29. The site marks them `nonumber` (SP26 and
SU26 both did), so students see 26 numbered lectures and Buffer is unnumbered.

**Lecture schedule: resolved.** Formal classes end Fri Dec 4 (Dec 7-11 is RRR week,
Dec 14-18 is finals), so Tu/Th lectures run Aug 27 - Dec 3 = 29 class days, one of
which (Nov 26) is Thanksgiving. The spreadsheet listed 30 slots, one more than
exists. `Buffer/Guest?` was dropped, and Ion and Sylvia have confirmed that was the
right call - the extra row was a mistake in the spreadsheet.

Final lineup: Nov 24 AI (Collectives), Nov 26 Thanksgiving, Dec 1 Wireless,
Dec 3 Cellular.

**Final exam date.** Finals week is **Mon Dec 14 - Fri Dec 18, 2026**. The exact
slot comes from the exam group for a TuTh 11:00am class - check the Fall Final
Exam Groups calendar at registrar.berkeley.edu/calendars/final-exam-groups/, or
read it off CalCentral, which is authoritative once registration opens. Room
assignments publish in the 11th week of instruction. Once you have it, fill in
`_data/exams.yml` `final` and add the date to `extra_days` in `_data/syllabus.yml`.

## 3b. Deliberately removed for now

**Homework column.** FA26 has no homeworks, confirmed. The `HW` column stays
commented out in `_includes/syllabus.html` with `homework_width: "0%"` - the same
as SU26 and SP26. `_data/homeworks.yml` is dormant and unused; leave it alone.
The `Labs` column is hidden the same way.

**Empty cells are expected.** Lecture cells have no Slides/Recording links,
discussions have no Worksheet/Solutions/Video/Slides links, and projects have no
due dates - none of that content exists yet. This makes rows visibly shorter than
a mid-semester site. Nothing is wrong with the CSS; the rows grow as content
lands.

**Project 3.** `proj3/index.md` is a "coming soon" placeholder so links to
`/proj3/` keep resolving. Assets and both schedule entries are still removed while
the project is rewritten.

To restore: copy `assets/projects/proj3/` from the SU26 repo, rename the zip to
`cs168-fa26-proj3-transport.zip`, replace the placeholder page with the real spec,
re-add the two entries to `_data/projects.yml`, and shrink the trailing spacer
(currently `rowspan: 16`) so the project column still sums to 58.

Note the SU26 spec referenced `site.data.projects.projects[3]` and `[4]` by index
for its Gradescope links — those indices shift when you re-add the entries.

**Exam Logistics page.** `nav_exclude: true` in `exam.md`. Unhide once
`_data/exams.yml` is real.

## 4. Content review

**Starter code links are broken.** `assets/projects/` and `assets/discussions/`
were deleted (12.8 MB of 28 MB) to keep them out of git history. These pages link
into them and need new FA26 files:

- `proj1/setup.md` — `cs168-su26-proj1-traceroute.zip`
- `proj2/index.md` — `cs168-su26-proj2-routing.zip`
- `proj3/index.md` — `cs168-su26-proj3-transport.zip`

Those pages also carry a bug inherited from SU26: download links say
`cs168-su26-...` while the `cd` instructions in the prose say `cs168-sp26-...`.
Fix both to `fa26`.

**`resources.md`** — past-exam archive stops at Spring 2026. Add a Summer 2026 row.

**`resources/faqs.md`** — line 130 points at the SU26 site as the "past semester"
example. Still true, but worth a look.

**`policies/`** — read for anything semester-specific.

**`policies/dsp.md`** renders an empty accommodations table: it reads
`site.data.exams.midterm.dsp_150` and similar fields that have never existed in
`exams.yml`. SU26 shipped this way too, so it is pre-existing, not new. Either add
the `dsp_*` fields or trim the table.

**`.su26-reference/`** — SU26's `lectures.yml`, `discussions.yml`, `projects.yml`,
kept for content diffing. Delete when done.

## 5. Before launch

- `under_construction: false`
- Consider `discussions_under_construction: true` for the first few weeks
- Delete this file and `.su26-reference/`
- `grep -rn "su26\|sp26\|Summer 2026\|Spring 2026" --exclude-dir=.git .`

## 6. Deploy

1. Create **private** repo `berkeley-cs168/fa26-website`, push
2. PR rules: squash only, no merge commits, no rebase, auto-delete head branches
3. Squarespace as SPA: CNAME `fa26` -> `berkeley-cs168.github.io`
4. Settings -> Pages: `main` / root, custom domain `fa26.cs168.io`, Enforce HTTPS
5. Set `outdated: true` in the SU26 repo's `_config.yml`
6. Update `berkeley-cs168/current-site-redirect` — currently sends `cs168.io`
   to `su26.cs168.io`

---

## How the schedule table works

Everything is row arithmetic. `syllabus.yml` sets `day_rowspan: 2`, so one class
day is two table rows. 29 Tu/Th class days = **58 rows**, and every column must
sum to exactly that:

| Column | Entries | Rows each | Total |
|---|---|---|---|
| Lectures | 29 | 2 | 58 |
| Discussions | 15 | 14 at 4 + 1 at 2 | 58 |
| Projects | 7 | varies | 58 |

Overflow silently truncates — entries past row 58 vanish with no error.
Underflow leaves blank cells at the bottom. If you change `class_days` or the
date range, recompute all three columns.

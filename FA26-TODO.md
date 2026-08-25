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
- `_data/syllabus.yml` — `2026-08-26` to `2026-12-08`, Tu/Th, SP26 rowspan geometry
- `_data/lectures.yml` — 30 entries mapped to the 30 Tu/Th class days,
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

**Staff** — `_staffers/` and `assets/staff/` still hold SU26's 8 people.
Rebuild from the welcome form; run photos through `mogrify` before committing
(SU26 kept the folder to 332 KB).

## 2. Waiting on other people

**Exam dates** — `_data/exams.yml` is fully blank. The lecture schedule puts the
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

**Due dates** — `_data/homeworks.yml` and `_data/projects.yml`, every `due:`.

## 3. Needs a content decision

**Project column rowspans.** The column encodes the project timeline as row
counts, inherited from SP26. To make it fit FA26's 60 rows I shrank only the
trailing spacer (18 -> 10). Once due dates exist, rebuild the whole column —
current values are `8, 8, 8, 18, 4, 4, 10`.

**Discussion slot 15 is a blank placeholder.** FA26 has 30 class days = 15
discussion slots at two class days each. Slot 14 spans Thanksgiving (Nov 26) and
Dec 1; slot 15 covers Dec 3 and Dec 8. Decide what runs those weeks and fill in
the last entry in `_data/discussions.yml`.

**Two lecture titles drift from the CSV.** The CSV column is labeled
"Topic (to be updated!)", so I kept SP26's polished titles and their textbook
readings. Confirm with the content lead:

| CSV topic | Site title |
|---|---|
| Overlays | Beyond Client-Server 1: Multicast |
| AI (Collectives) | Beyond Client-Server 2: Collectives |

**Lecture numbering differs from the CSV.** The CSV numbers the two midterm rows
as lectures 18 and 19, ending at 29. The site marks them `nonumber` (SP26 and
SU26 both did), so students see 26 numbered lectures and Buffer is unnumbered.

**Dec 10 has no lecture.** It is a Tu/Th day inside the instruction window
(ends Dec 11), but the CSV's last lecture is Dec 8. `end_date` is `2026-12-08`
so Dec 10 does not render. Change if you want an RRR row.

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
day is two table rows. 30 Tu/Th class days = **60 rows**, and every column must
sum to exactly that:

| Column | Entries | Rows each | Total |
|---|---|---|---|
| Lectures | 30 | 2 | 60 |
| Discussions | 15 | 4 | 60 |
| Projects | 7 | varies | 60 |

Overflow silently truncates — entries past row 60 vanish with no error.
Underflow leaves blank cells at the bottom. If you change `class_days` or the
date range, recompute all three columns.

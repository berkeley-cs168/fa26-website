# FA26 Website TODO

Everything mechanical is done. What's left needs real Fall 2026 data.
Excluded from the Jekyll build via `_config.yml`. Delete this file before launch.

Find every remaining placeholder with:

```bash
grep -rn "TODO(FA26)" --exclude-dir=.git --exclude=FA26-TODO.md .
```

---

## 1. Blocking — nothing renders correctly until these are right

**`_data/syllabus.yml`** — this file drives the entire schedule table.

- `start_date` / `end_date` — currently **provisional** (`2026-08-26` / `2026-12-11`).
  Verify against the registrar's academic calendar before trusting them.
- `class_days` — currently `['2', '4']` (Tu/Th), inherited from SP26.
  Confirm on classes.berkeley.edu. **If FA26 is not a two-day-per-week semester,
  the rowspan values below it are all wrong** — see the note at the bottom.
- `extra_days` — placeholder dates for the midterm and final. Replace once
  Carlos Garcia confirms.

**`_config.yml`**

- `lecture.time`, `lecture.location.name`, `lecture.location.link`
- `heading_links` → Zoom URL, YouTube playlist URL
- `aux_links` → Ed course URL, feedback form URL

**`_data/calendar.yml`**

- `google_calendar_id`, `google_calendar_embed_link`, `google_calendar_add_link` —
  all three come from the new `[CS168 FA26] Website` calendar (created as SPA).
- `google_api_key` carries over from SU26 and is already correct.
- `remove_prefix` is already set to `"[CS168 FA26] "` — keep the trailing space.

## 2. Waiting on other people

**`_data/exams.yml`** — every field blanked. Needs confirmed midterm and final
dates, alternate-exam times, the exam-request form links, scope, and arrival times.

**`_data/projects.yml`** — every `gradescope:` link blanked. Re-point at the new
FA26 Gradescope course once assignments exist.

**`_data/homeworks.yml`** — every `due:` blanked.

**`_staffers/` and `assets/staff/`** — still holds SU26's 8 staffers. Rebuild from
the welcome form. Run every photo through the `mogrify` script *before* committing;
SU26 kept the whole folder to 332 KB.

## 3. Content review

**Schedule data came from SP26, not SU26.** `lectures.yml`, `discussions.yml`,
`projects.yml`, and `syllabus.yml` are the SP26 versions (32 lectures /
17 discussions / 7 projects) because SU26 was a compressed 8-week session with a
different table geometry. SU26's versions are preserved in `.su26-reference/` —
diff them and pull forward any content improvements:

```bash
diff .su26-reference/lectures.yml _data/lectures.yml
```

Delete `.su26-reference/` once you're done with it.

**Starter code links are broken.** `assets/projects/` and `assets/discussions/`
were deleted (12.8 MB of the repo's 28 MB) so they stay out of git history. These
pages link into them and need new FA26 files:

- `proj1/setup.md` — `cs168-su26-proj1-traceroute.zip`
- `proj2/index.md` — `cs168-su26-proj2-routing.zip`
- `proj3/index.md` — `cs168-su26-proj3-transport.zip`

Note those pages also have a pre-existing inconsistency inherited from SU26: the
download links say `cs168-su26-…` but the `cd` instructions in the prose say
`cs168-sp26-…`. Fix both to `fa26` when you post the new starter code.

**`resources.md`** — the past-exam archive. Add a Summer 2026 row (it currently
stops at Spring 2026) and add SU26 to the past-semesters list.

**`resources/faqs.md`** — line 130 points students at the SU26 site as the
"past semester" example. Still true, but worth a look.

**`policies/`** — read through for anything semester-specific.

## 4. Before launch

- Set `under_construction: false` (currently `true`)
- Consider `discussions_under_construction: true` for the first few weeks if
  discussion times are still moving
- Delete this file and `.su26-reference/`
- Run the leftover check:
  ```bash
  grep -rn "su26\|sp26\|Summer 2026\|Spring 2026" --exclude-dir=.git .
  ```

## 5. Deploy

1. Create **private** repo `berkeley-cs168/fa26-website`, push
2. PR rules: squash only, no merge commits, no rebase, auto-delete head branches
3. Squarespace as SPA: CNAME `fa26` → `berkeley-cs168.github.io`
4. Settings → Pages: `main` / root, custom domain `fa26.cs168.io`, Enforce HTTPS
5. Set `outdated: true` in the SU26 repo's `_config.yml`
6. Update `berkeley-cs168/current-site-redirect` — it currently sends
   `cs168.io` to `su26.cs168.io`

---

## Note on rowspans

If FA26 is **not** two lecture days per week, `_data/syllabus.yml` needs more than
a `class_days` change. Every rowspan is relative to `day_rowspan`:

| | SU26 (4 days/wk) | SP26 (2 days/wk) |
|---|---|---|
| `day_rowspan` | 1 | 2 |
| `default_lecture_rowspan` | 1 | 2 |
| `default_discussion_rowspan` | 2 | 4 |
| `default_homework_rowspan` | 2 | 4 |
| `default_lab_rowspan` | 4 | 8 |

The per-entry `rowspan:` values in `projects.yml` scale the same way (SU26 used
4/4/4/10/4/18; SP26 used 8/8/8/18/4/4/18). Getting this wrong produces a visually
mangled schedule table with no error message.

`labs.yml` is dormant template placeholder data — `labs_width` is `0%`, so it
isn't rendered. Leave it alone unless you add labs.

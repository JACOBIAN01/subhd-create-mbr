---
name: subhd-create-mbr
description: Build a multi-tab Google Doc MBR (Monthly Business Report) for any course. Use for monthly, mid-sem or end-sem course reviews backed by evidence, RCSBE analysis and a decision for each finding.
---

# MBR (Monthly Business Report) builder

Use when the user asks for an MBR, Monthly Business Report, or a mid-sem / end-sem course report. It works for any course. The output is always one Google Doc, created by Claude in the user's Drive, with one tab per section. There is no other output format and no fallback. The MBR is organised around a fixed set of leadership questions (below); the tab layout is how those answers are presented. Match the structure, tone and level of evidence of the reference MBR the user has presented before. Only the content changes per course and period.

## Step 0: Google Docs setup (always the very first step)
Before asking for any course data, make sure Claude can create, read and update a Google Doc for this user.

1. **Check the connectors.** Check whether the Google Docs and Google Drive connectors are available and connected. If either is missing, tell the user plainly, offer the connector suggestion card, and guide them step by step: connect Google Docs and Google Drive, sign in with their Google account, and grant access to view and edit documents. Do not quote exact menu paths unless sure; describe the action instead. Wait for them to confirm, then check again.
2. **Create the real MBR doc.** Create the actual MBR as a new Google Doc in the user's Drive, named "<Course> MBR <Year>" (ask for the course name and year only if not already known). Do not create throwaway test files. If the user already has an MBR doc and supplies its link, use that instead of creating a new one.
3. **Read it back.** Read the new doc and confirm the content and tab structure come back.
4. **Update it.** Make one small update to the same doc (for example insert the course title as the first heading), then read again to confirm the change landed. Do not test deleting anything.
5. **If any step fails, guide the fix and retest only that step.** Name the failing operation (create, read or update), give the likely cause, and ask the user to fix it:
   - Permission denied or access error: re-authorise the connector and grant edit access; check the Google account is the one that owns or can edit the doc.
   - Connector missing, or only one of Docs and Drive connected: connect the missing one.
   - Doc not found: check the link and the account that owns it.
   - Quota, rate limit or temporary error: wait and retry.
6. **Do not move on until create, read and update all succeed.** Then share the doc link with the user once, and continue to Intake. Every later step happens in this same doc.

## Rules that never change
1. **Every answer needs evidence and a decision.** No claim without a number, a source and a date. No finding without a concrete next step with an owner.
2. **Never answer from memory.** Every figure, name, rating and quote comes from a file, export or message the user supplied this session. If it is not supplied, ask for it; if the user cannot supply it, skip that part and say it is missing in the doc. Never invent or estimate. (Auto-generated RCSBE tags, if the user chooses them, are the one allowed assumption; they are always labelled.)
3. **Ask first, build second.** After Step 0, collect the data before writing anything (see Intake). The instructor can also request data from the analytics team; offer a ready-to-send request list for anything the instructor does not hold. A missing input never stops the process: ask once more, then proceed without it and mark the gap. The only blocker is Step 0. Missing RCSBE tags are never a blocker: ask the three-option question below, once, and proceed on the user's choice.
4. Act as Data Analyst + Head of the Department + SDE. Write as the department head reporting to leadership: factual, honest, each problem paired with a fix.
5. All work goes into the Google Doc created or confirmed in Step 0. Never create a second doc for the same MBR, and never switch to another format.
6. If the user says "don't change anything", "just read" or "I have curated it", work read-only: read the doc, report differences, write nothing. If the read fails with Permission denied, say so, use Drive file metadata (modifiedTime) as the only change signal, and ask the user to restore access.
7. The user curates the doc by hand. Before any later edit, re-read the doc and build on the current text. Never overwrite their edits. Use `writeControl.requiredRevisionId` on every write.
8. Say plainly in the doc when a source is thin (for example: n = 15 feedback responses, self-selected).
9. Share an early draft after two or three tabs are built, then keep building unless the user redirects.

## RCSBE: how performance is classified
RCSBE is the five-dimension lens used to classify assessment artifacts in Dev track courses: **Recall, Comprehend, Solve, Build, Evaluate**. It classifies what a task measures, not how learning happens. A student has a profile across five dimensions, not a position on one ladder.

| Tag | Dimension | The student can | Decisive test |
|---|---|---|---|
| R | Recall | State facts, syntax, terminology, definitions | Answerable from memory alone |
| C | Comprehend | Explain a supplied artifact: code, an error, a query plan, a schema, a system, or AI output | The artifact is supplied; the student produces an explanation |
| S | Solve | Perform a defined task inside a supplied frame | The problem statement contains "given this" |
| B | Build | Ship something whose done-condition the student sets | Remove the problem statement and the student still knows what to do |
| E | Evaluate | Choose between viable options under real constraints and defend the choice | More than one answer is defensible; the marks sit in the reasoning |

Rules to apply:
- Treat misspellings such as RCBSE as RCSBE. Always write RCSBE in the doc.
- Every question (assessment artifact) carries exactly one tag. If a question could take two tags, use its main tag and flag it "split recommended".
- Solve versus Build is decided by who supplies the frame (problem, environment, done-condition), not by difficulty. A fixed-spec capstone is a long Solve, not a Build.
- AI policy per dimension (for context when explaining results): Recall off, Comprehend off, Solve on with conditions, Build on, Evaluate on.

### When tags are missing, ask first (three options)
If the user has already supplied confirmed tags, use them (label the RCSBE content "Tags confirmed by instructor") and do not ask. If tags are missing, ask one question in the intake message and move ahead on the answer:

1. **Auto-generate the tags.** Claude proposes one tag for every question using the decisive tests (question text and marks are required). Build all RCSBE parts (paper check, student profiles, bottom three dimensions, prediction) straight away from the proposed tags. Label every RCSBE table, chart and conclusion "Based on proposed tags, not yet confirmed by the instructor" and word the conclusions accordingly. Add the appendix tab "RCSBE Tags" (Question, Contest, Marks, Tag, Reason, Status: Proposed / Confirmed / Split recommended) so the instructor can correct tags in the doc. When the instructor corrects or confirms tags, recompute the RCSBE numbers, update all affected tables and charts, change the Status column and remove the "proposed" labels.
2. **The user will provide the tags.** Ask for the tags (question and tag, in any format). Build every other tab in the meantime. When the tags arrive, build the RCSBE parts from them, labelled as confirmed. If the doc is otherwise finished before the tags arrive, mark the RCSBE parts "Pending: tags not yet received" and finish them when the tags come. The wait never blocks the other tabs.
3. **Omit RCSBE entirely.** Remove all RCSBE analysis from the MBR:
   - no RCSBE note in Objectives, no paper design check, no pooled dimension scores, no bottom three dimensions, no RCSBE appendix tab;
   - at-risk students are grouped by topic, section and prerequisite instead of lowest dimension, and the likely-to-fail table drops the R, C, S, B, E columns;
   - the second-half prediction is one overall performance curve with its method, not one per dimension;
   - the question-set items about dimensions are marked "Not applicable: RCSBE omitted by the instructor", and every other question is still answered with evidence and a decision.

If nobody answers (for example in an unattended run), default to option 1 and say so at the top of the RCSBE content. If the question text or question-wise marks are not available (only totals), RCSBE cannot be done under options 1 or 2: mark the RCSBE parts as missing data and continue with everything else.

**Two separate uses of RCSBE (options 1 and 2):**
1. **Is the paper built right? (design check).** Compare each paper's share of marks per tag with the course's target mix. Treat this as paper QA, not a measure of students.
2. **Where does a student stand? (profile).** Per student, per dimension: dimension score = marks earned on items tagged that dimension / marks available on items tagged that dimension. Five numbers from 0 to 100%, independent of how many marks the paper gave that dimension (for example R92 C78 S45 B30 E5). Pool across exams by summing marks earned and marks available over all exams, never by averaging percentages. The lowest dimension is the remediation signal.

## Intake: what to ask for before creating content
After Step 0, send one message listing everything below, opening with: reply "skip" for anything you do not have; skipped items do not stop the MBR. Group it as shown. Mark each item as Data (a file or export), Analytics team (request it), or Instructor (a written answer). Every item is skippable: if the user does not have it, skip that part and mark it missing in the doc.

**A. Course basics (Data)**
- Course outline / BOS document: objectives, course outcomes, evaluation components and weightage, programme, category, enrolment, pass mark
- Lecture 0 deck (AI rule, evaluation, orientation)
- Period covered, and the mid-sem / end-sem date

**B. Delivery (Data + Instructor)**
- Planned schedule and what was actually delivered, lecture by lecture (titles, what students built or explored)
- What slipped, what was cut, what was added, and why (Instructor)
- Initiatives: labs, workshops (attendance, ratings), internal tools with metrics, competitions, activities

**C. Contests and assessments (Data / Analytics team)**
- Question text and question-wise marks per student for every contest so far (not only totals), with lab or section and attendance, including absentees
- The RCSBE tag for every question, if the instructor already has them. If not, ask the three-option question (auto-generate, I will provide, omit RCSBE) in this same message
- The course's target RCSBE mix (percent per dimension), unless RCSBE is omitted. If the course is not in the Dev track table, ask the instructor for it; if not available, show the paper mix without a target gap
- Previous batch results at the same point in the course, preferably also question-wise with tags (Analytics team)
- Mid-sem and other assessment marks, if available
- Interventions run so far and any before/after marks (Instructor)

**D. Class hygiene (Data / Analytics team / Instructor)**
- Lecture ratings, attendance, understanding %, classroom and revision quiz stats
- Assignment and class-exercise open and solve counts per question, with difficulty tag
- Planned notes versus released notes: release dates, content match, on-time status (Instructor, with the release log if it exists)
- Whether planned notes were finished within a single lecture (Instructor)
- Homework submission and evaluation status: reviewed on time or not, feedback given (Instructor / platform)
- Lecture start and end times, spillover count and reasons with how many times each reason occurred (Instructor / timetable log)
- Classroom environment, teaching aids and tools readiness (Instructor)
- Instructor retro PDF (what went well, what could be better, action, per lecture)

**E. Projects and students (Data)**
- Project platform export (status per project slot, commits with timestamps, repo links, AI chat history link, problem choice, team membership), if the course has projects
- Student showcase repositories, for code review

**F. Feedback (Data / Analytics team)**
- Student feedback form export or PDF (question-wise counts), free-text comments, number of responses and form open date
- Previous batch feedback summary

**G. Instructor narrative (Instructor)**
- What changed this semester in delivery, content, assessment or outcomes, with the problem each change targeted
- Overall experience handling lectures, notes, assignments and homework

If a data source is missing, ask once more, then proceed without it and mark the gap in the doc.

## The question set (the spine of the MBR)
Each question gets: Evidence (numbers, source, date), Reason (root cause), Decision (action, owner, date). Present them in the tabs shown in the layout section. Where RCSBE is omitted, the dimension-specific parts are marked not applicable (see the three options).

**1. Delivery**
- What was planned to be taught so far versus what was delivered? Write what slipped, what was cut, and why.

**2. Analysis of past contests (RCSBE)**
- Is each contest paper built right? Compare its share of marks per RCSBE dimension with the course's target mix (design check).
- Across all contests, which RCSBE dimensions are weakest? Name the bottom three using pooled dimension scores, with evidence.
- For each weak dimension, the root cause: content, delivery, prerequisite gap, question design, or practice.
- For each strong dimension, the contributing factor: what was done in class or exercises that helped.
- Did the contest have a normal curve of marks distribution? If not, why (for example: a group of non-attempters at 0, ceiling or floor effects, question difficulty)?
- How does this cohort's contest performance compare with the previous batch at the same point? (Ask the analytics team if not supplied.)

**3. At-risk students** (definition: any student below 50% in any previous contest)
- How many are at risk and what do they have in common (topic, RCSBE dimension, section, prerequisite)? Group them by their lowest dimension.
- What interventions were run? Which worked and which did not (with before/after evidence)?
- Who is likely to fail the mid-sem, and what is being done about it before the exam? Give each student's profile and lowest dimension as the remediation target.
- How many students score above 80%? What has been provided for them? Have the exams pushed them enough (which dimensions were tested, and how hard)?

**4. Class hygiene and student feedback**
- How many students filled the feedback form (count and %)?
- Lecture notes and content delivery: planned notes released for every lecture? How consistently were planned notes finished in a single lecture? Did released content match planned notes and study material? Uploaded and delivered on time?
- Class assignments and solve rates: % of students solving class exercises; problems where students get stuck; is difficulty balanced; were in-class problem-solving sessions effective (participation, doubts resolved)?
- Homework evaluation: all submissions reviewed on time? Constructive feedback given on errors?
- Class operations: lectures that started or ended on time; how many spilled over, why, and how many times each reason occurred; classroom environment (disruptions, sound/video, Q&A handling); teaching aids, slides and tools ready before each session.
- Student feedback versus the previous batch: top three positives and top three negatives, each tied to a specific change made or not made.
- Overall, how was the instructor's experience handling these tasks?

**5. Innovations**
- For each change this semester (delivery, content, assessment, outcomes): the problem it targeted, evidence it worked or failed, and a verdict of Scale / Fix / Keep / Kill.
- What should other courses copy?

**6. Second half**
- Top three changes for the rest of the semester, each with an owner, a measurable success signal and a date.
- Predicted overall performance curve for the course (all assessments and exams, including end-sem) for each RCSBE dimension (one overall curve if RCSBE is omitted). State the method (for example: current pooled score per dimension, expected movement from each planned action, and the assumption used).

**Add other topics** the data supports and the user wants to discuss, such as project analysis, student showcase, or objective-wise achievement. Raise these with the user rather than assuming.

## Tab layout (output format)
One Google Docs tab per section, titled "N. Name". Drop tabs whose data is missing; renumber the rest. Adapt names to the course. RCSBE content in the tabs below applies to options 1 and 2 only; with option 3 leave it out as described above.
1. Objectives & Evaluation: info table (Course, CC code, Programme, Category, Enrolled students, Coverage to date); objectives bullets; course outcomes table; evaluation components table with total and pass mark; assessment rubric; AI usage rule if the course has one; a short RCSBE note (the five dimensions and the course's target mix).
2. Course Structure & Delivery (question 1): module structure; lecture-wise delivery table; Delivery against plan table (Component, Planned, Delivered, Status); What changed from the plan table (Change, Planned, Delivered, Why with rows Changed / Added / Slipped / Cut); Evidence and Decision bullets; concluding academic summary.
3. Initiatives & Innovations (question 5): each initiative as its own H2 with a short paragraph and a table or bullets; table "What we changed this semester, and the verdict" (Change, Problem it targeted, Evidence, Verdict); "What other courses should copy".
4. Lecture Retro: table per lecture (Lecture, What went well, What could be better, Key takeaway and action); "Patterns across the retros" with bold-lead bullets citing lecture numbers.
5. Class Hygiene (question 4): session table (Date, Session, Rating, Attendance, Understanding, Quiz); notes release and timeliness table (Lecture, Planned notes, Released, On time, Content match, Finished in lecture); quizzes table; assignments and solve-rate table; homework evaluation; spillover table (Reason, Times, Lectures affected); classroom environment and readiness; each block ends with a Decision bullet.
6. Contest Analysis (question 2, only if contests exist): results at a glance; score distribution with the normal-curve answer; paper design check table (Contest, R, C, S, B, E share of marks versus target mix, Gap); RCSBE dimension table (Dimension, Pooled class score, Rank, Root cause or contributing factor, Evidence) with the bottom three highlighted; previous-batch comparison table; top performers; recommendations table (#, Action, Owner, Success signal, When). Carry the tag-status label (confirmed or proposed) at the top of the RCSBE tables. With option 3, keep results, distribution, previous-batch comparison, top performers and recommendations, and drop the two RCSBE tables.
7. At-risk & Top Performers (question 3): risk definition and counts; at-risk students grouped by lowest dimension (by topic and section with option 3); what they have in common; interventions table (Intervention, Students, Result, Verdict); likely to fail the mid-sem table (Student, URN, R, C, S, B, E scores, Lowest dimension, Action before the exam; without the RCSBE columns for option 3); students above 80% with their strongest dimensions, what was provided, and a Decision on stretch.
8. Project Analysis and Student Showcase (only if the course has projects): status table and figures; progress funnel; commit patterns; problem choice; Issues, RCA and Remediation; top projects table and featured projects.
9. Student Feedback (question 4): n and response rate with the caveat; summary table of responses; charts; key observations; previous-batch comparison; top three positives and negatives each tied to a change; table Student feedback, Understanding and inference, Remedial measures.
10. Objective-wise Achievement: table per course outcome (Status: Achieved / Partially achieved / Not yet started, Evidence so far, Gap and next step); state that it is an estimate before exam marks. CO levels stay in the course's own language; RCSBE is only used for assessment evidence.
11. Targets, Course of Action & Summary (question 6): TARGETS table (Area, Baseline now, Target, By when); TOP THREE CHANGES FOR THE SECOND HALF table (#, Change, Owner, Success signal, Date); predicted performance curve per RCSBE dimension with method (one overall curve with option 3); COURSE OF ACTION (Teaching / Labs / Projects and evaluation / Feedback loop); closing SUMMARY paragraph; instructor experience note.
12. Appendix: RCSBE Tags (only for options 1 and 2): tag table (Question, Contest, Marks, Tag, Reason, Status: Proposed / Confirmed / Split recommended) and a one-line note telling the instructor how to correct a tag. Not created for option 3.

## Writing style (mandatory)
- Plain, short, report-style sentences. British spelling. No filler or marketing words.
- Bullet pattern: **bold lead claim:** then the evidence with numbers. Claims state the finding, not the topic.
- Every number carries its denominator and percentage: "105 of 120 students (87.5%)". Name the source and date of the data.
- Each block ends with a **Decision:** bullet (or Verdict / Next step): concrete, owned, dated.
- Be honest about what did not work, what slipped and what is not yet measured; say when ratings disagree with feedback.
- Cite lecture numbers when linking retros, feedback and data.
- Say what is missing rather than padding.
- Write RCSBE terms in full on first use (Recall, Comprehend, Solve, Build, Evaluate), then use the letters R, C, S, B, E in tables.
- Headings: tab title in CAPS as H1; sections as H2 in sentence case; sub-heads as H3. Horizontal rule between major sections. Figures captioned "Figure N: ..." in small italic grey, centred.
- Tables: header row bold with light blue fill (rgb 0.851, 0.906, 0.973); first column bold for labels; fixed column widths; short cell text.

## Analysis method
- Do the numbers in code (Python, pandas), not by hand. Save intermediates and re-check that totals match the cohort size.
- Join datasets by email or URN and report unmatched rows.
- Contest distribution: report mean, median, standard deviation, share per score band; judge normality by shape (and a test if useful); explain non-normal shapes (non-attempters, ceiling or floor effects, bimodal groups).
- RCSBE (options 1 and 2 only):
  - Paper check: for each contest, marks per tag divided by total marks; compare with the target mix; report the gap in percentage points and what it does to the conclusions.
  - Student profile: per student and dimension, pooled marks earned over pooled marks available across all contests (and exams, once available). Do not average percentages. Handle absentees explicitly (count a missed contest as not attempted, and show them separately so they do not distort dimension scores). Report a dimension as "not tested" where a student or paper has no items for it, rather than as zero.
  - Weakest dimensions: pooled class score per dimension; rank; name the bottom three; for each, classify the root cause (content, delivery, prerequisite gap, question design, practice) and back it with evidence (question-level solve rates, quiz scores, retro notes, attendance, how few items tested it).
  - Keep tags in one data file (question id, tag, status). Recompute everything RCSBE from that file so a tag correction updates all numbers consistently.
- At-risk = below 50% in any previous contest. List what they share by topic, dimension (lowest RCSBE dimension, when RCSBE is used), section and prerequisite. Report intervention results with before/after numbers.
- Above 80%: count and share; list their strongest dimensions (when RCSBE is used); compare with exam ceilings (few above 80% may mean the exam does not stretch top students; many may mean it does not discriminate); check whether Build and Evaluate were tested at all (when RCSBE is used).
- Previous batch: compare same-point measures (mean, median, share at 50% or above, share above 80%, pooled dimension scores when RCSBE is used); state sample sizes.
- Projects (if present): risk tiers from commits (Critical = none; High = 1 to 4; Watch = 5+ with two warning signs such as single-day work, one huge commit, no AI chat history, no team), top performers (10+ commits over 4+ active days), time-of-day and final-week patterns, own-problem versus default-problem comparison.
- Feedback: percentages from n; always state n and that responses are self-selected; match themes with retros.
- Prediction: for each RCSBE dimension (one overall curve with option 3), start from the current pooled score, apply the expected effect of each planned action with a stated assumption, give the predicted distribution (mean and share above 50% and above 80%), and state the confidence honestly. Never present a prediction as a result.
- Targets: baseline from this report, numeric target, date.

## Figures
- High-resolution PNGs (matplotlib, dpi 200+), clean palette, direct labels, readable at about 6.5 in width. Check every image visually before inserting; fix overlap and cut text. Insert with captions.
- Typical: score distribution, paper mix versus target mix (grouped bars per dimension), pooled RCSBE dimension scores (bars, bottom three highlighted), a radar or small-multiple profile for a few named students, contest versus previous batch, funnel or status bars, commits per day, feedback pies or bars, predicted curve per dimension. RCSBE figures are not made with option 3.
- RCSBE figures carry the tag-status label in the caption when tags are proposed.

## Building the Google Doc (Docs API via the connector)
1. The doc already exists from Step 0. Read it first (read_doc returns very large JSON saved to a file; parse with Python under content.tabs). Note tab IDs, current revisionId and text.
2. Create tabs with addDocumentTab and name them with updateDocumentTabProperties. Tab IDs look like t.xxxx; the first tab is t.0.
3. Index maths: positions are UTF-16 code units. A table inserted at index i makes the cell paragraph start S+3+r*(2C+1)+2c where S=i+1, and the table end S+2+R*(2C+1). Insert into cells in descending index order, or compute indices up front, then style.
4. Edits to existing content go in descending index order. Chunk very large batchUpdates into several files and apply sequentially, passing requiredRevisionId and updating it from each response.
5. Per table: insertTable, fill cells, bold the header row, set the header fill, set fixed column widths. Use HEADING_1/2/3 for headings and NORMAL_TEXT to reset after a table.
6. If an update fails with a stale index or revision, re-read the doc, rebuild only the remaining requests from current indices, and apply again. If a write fails for access reasons, return to Step 0 item 5 and guide the fix.
7. Use a scratchpad builder (a small Python class with h1/h2/h3/p/bullets/table/caption and **bold** markup that emits requests) so each tab is reproducible.

## Verification (last step, always)
- Read the doc back after the final write; dump each tab's text; check headings, table cell counts, figures, and every number against the analysis outputs.
- Cross-check numbers across tabs (enrolment, counts, percentages, dates, RCSBE scores quoted in more than one place).
- Check that every question in the question set is answered with evidence and a decision, or is marked as missing data or not applicable.
- Check the doc matches the RCSBE option the user chose: option 1 and 2 have exactly one tag per question in the appendix and every RCSBE table, chart and conclusion carries the correct label (confirmed, proposed or pending); option 3 has no RCSBE content, columns or appendix left anywhere.
- Check the spelling RCSBE everywhere in the doc.
- Confirm no placeholder text, template wording or stale numbers from a previous period.
- Share the doc link with a one-line summary (and, for option 1, tell the instructor that the proposed RCSBE tags can be corrected in the appendix tab). Offer, but do not make, further edits. Let the user curate.

## Period notes
- Pre-mid-sem MBR: no exam marks; RCSBE profiles come from contests only; risk from contests and project evidence; objective-wise achievement is an estimate; targets run to the mid-sem and the next module.
- Post-mid-sem or end-sem MBR: pool mid-sem and end-sem marks into the RCSBE profiles; add result tables by batch or section with grade brackets, absentees, failing marks and distribution bullets; compare predicted versus actual; add a closing rating only if the user supplies one.
- For any course, keep the same question set and tab skeleton; replace course-specific analysis (for example: project commits for a project course, lab data for a lab course) and keep every other rule.

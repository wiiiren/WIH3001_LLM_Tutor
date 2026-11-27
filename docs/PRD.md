# Product Requirements Document (PRD)

**Product:** LLM Maths Tutor ("AI Maths Tutor" in the UI)
**Project:** WIH3001: Agentic AI for Personalized Adaptive Learning
**Author:** Tan Wei Ren · **Supervisor:** Dr. Kasturi Dewi Varathan
**Version:** 0.1 (draft) · **Date:** 27 November 2026
**Related:** [TRD](TRD.md) · [Design](Design.md) · [UI/UX Survey](UX_Survey.md)

---

## 1. Summary

A free web app where a primary-school learner practises maths with an AI tutor that knows what they have and have not mastered. A knowledge tracing model estimates mastery for each concept from the learner's answers. An LLM agent uses those estimates through tools to:

- choose the next question,
- give step-by-step hints from verified worked solutions, and
- explain why it chose each question.

It never gives the final answer before the learner has tried. A dashboard shows progress per concept, and a teacher overview shows the class at a glance.

## 2. Problem

- **Practice is not personalised.** Most practice tools give every learner the same questions in the same order. Strong learners repeat what they know, weaker learners move on too early, and teachers cannot track each learner across hundreds of concepts.
- **General chatbots don't fix this.** They keep no record of what a learner knows, cannot tell which concepts are weak, and often hand over the answer.
- **Unguided chatbot help can hurt learning.** Bastani et al. (2025) found high school students did worse on later exams after practising with a general chatbot.

The gap in existing products (proposal, Table 1): no tool that learners can use today joins all of these:
- a per-concept learner model,
- an open conversation,
- answer withholding that cannot be switched off,
- hints from verified solutions, and
- an explanation of why each question was chosen,

while also being free in Malaysia.

## 3. Goals and non-goals

### Goals
| ID | Goal | Linked objective (proposal §3.3) |
|---|---|---|
| G1 | Recommend practice that targets each learner's weak, early concepts at the right difficulty | O3 |
| G2 | Help learners through hints and explanations without giving the answer away | O3, O4 |
| G3 | Make the tutor's choices understandable ("why this question") | O5 |
| G4 | Show learners and teachers progress per concept | O5 |
| G5 | Be easy to use (SUS above the published average of 68) | O5 |
| G6 | Run at near-zero cost on free hosting | Scope §3.2 |

### Non-goals (this project)
- Image-based questions. These are excluded from the question bank.
- Content beyond XES3G5M (Grade 3 maths), or Malay or Chinese language UI.
- Testing with children, or measuring real learning gains.
- Accounts with personal details, payments, or parent portals.
- Voice input, handwriting input or mobile apps (the web app should still work on a tablet browser).

## 4. Users

| Persona | Context | Main jobs |
|---|---|---|
| **Aina, 9, Grade 3 learner** | Uses a family laptop or school tablet; reads English as a second language; easily put off by long text | Practise for 10–15 minutes; get unstuck without being told the answer; see that she is improving |
| **Mr. Lim, primary maths teacher** | Class of 35 with mixed abilities; checks in between lessons | See which pupils struggle on which concept; know who to help first |
| **Adult test volunteer** | Takes part in the SUS study for 20–30 minutes | Complete set tasks; judge ease of use |
| **Examiner / supervisor** | Assesses the project | Check that recommendations follow the learner model and that guardrails hold |

## 5. User stories

**Learner**
1. As a learner, I start with a few short questions so the tutor knows my level.
2. As a learner, I get one question at a time at a level I can manage.
3. As a learner, I type my answer and find out right away if it is correct.
4. As a learner, when I'm stuck I ask for a hint and get a small step, not the answer.
5. As a learner, after I've tried, I can get a bigger hint or a similar worked example.
6. As a learner, I can ask the tutor a question in my own words.
7. As a learner, I can see why the tutor picked this question.
8. As a learner, I can see which topics I've mastered and which to work on.
9. As a learner, I can choose between a few suggested topics to practise.
10. As a learner, I can report a question that looks wrong or confusing.

**Teacher**
11. As a teacher, I see a grid of my pupils and their mastery by topic group.
12. As a teacher, I see who is stuck, who asks for hints before trying, and who is inactive.
13. As a teacher, I see a short list of who to help next.
14. As a teacher, I open one pupil to see their weakest concepts and recent attempts.

**Researcher / examiner**
15. As a researcher, I can export anonymous logs (attempts, hints, recommendations) for evaluation.
16. As an examiner, I can see the model's numbers behind each recommendation.

## 6. Functional requirements

Priority: **M** = must, **S** = should, **C** = could (MoSCoW).

### 6.1 Access and onboarding
| ID | Requirement | Pri | Acceptance criteria |
|---|---|---|---|
| F1 | Learner starts with a learner code (no name, email or password) | M | A new code is generated; returning with the code restores progress |
| F2 | First screen discloses the AI tutor and data use | M | Shown before the first question; also on the About page |
| F3 | Diagnostic of 6–8 questions for new learners | M | Covers early concepts across top-level groups; produces initial mastery; no hints during the diagnostic, but "I don't know" is allowed |
| F4 | Teacher area protected by a passcode | M | Wrong passcode shows no data |
| F5 | Teacher creates a class code; learners can join it | S | Learners who join appear in that teacher's grid |

### 6.2 Practice session
| ID | Requirement | Pri | Acceptance criteria |
|---|---|---|---|
| F6 | Agent picks the next question from the learner model | M | Target concept = weakest unmastered concept early in the tree with prerequisites met; predicted P(correct) for the question is in [0.60, 0.85] when such a question exists, otherwise the closest available |
| F7 | Show one question with an answer box | M | Multiple-choice shows option buttons; fill-in-the-blank shows a text box |
| F8 | Check the answer in code and show the result instantly | M | Result appears before any LLM text; the attempt is saved; mastery is updated |
| F9 | Attempt-gated hint ladder (levels 1–3) | M | L1 any time; L2 after ≥1 attempt; L3 after ≥2 attempts; locked levels are shown with the reason |
| F10 | Final answer never shown before an attempt | M | Automated test: 0 leaks across the red-team prompt set (TRD §7) |
| F11 | Full worked solution after the question is finished | M | Shown after a correct answer or 3 wrong attempts, labelled "Worked example"; followed by a similar question when 3 wrong |
| F12 | Learner can chat with the tutor | M | Replies are on-topic, ≤ about 60 words, streamed; off-topic requests are redirected |
| F13 | "Explain it another way" | S | Uses the related worked example (L3 content) when unlocked; otherwise re-words L1 |
| F14 | "Why this question?" panel | M | Shows concept, mastery band and value, predicted chance, one-sentence reason; matches the values the agent used |
| F15 | Session of 10 questions with progress shown | S | "Question 3 of 10"; summary at the end showing concepts practised and band changes |
| F16 | Report a problem with a question | S | Saves question ID, reason and comment; flagged questions are excluded after 2 reports |
| F17 | Tool-call status shown while the agent works | S | Status text appears within 0.5 s of an action |

### 6.3 Learner dashboard
| ID | Requirement | Pri | Acceptance criteria |
|---|---|---|---|
| F18 | Mastery by concept, grouped by top-level concept | M | Bars with word bands; exact value on hover or tap; groups collapsed by default |
| F19 | "Ready to learn next": 3 suggested concepts with a "Practise this" button | S | Picking one starts practice on that concept |
| F20 | Mark low-evidence concepts | S | Fewer than 3 answers gives a dashed bar and "Not enough answers yet" |
| F21 | Recent activity | C | Last 10 attempts with result and hints used |

### 6.4 Teacher overview
| ID | Requirement | Pri | Acceptance criteria |
|---|---|---|---|
| F22 | Class grid of students × top-level concept groups | M | Cells show bands; sortable by weakest |
| F23 | Flags: Stuck, Hints before trying, Inactive | S | Rules as defined in TRD §8.3; no pop-ups |
| F24 | "Who to see next" list | S | Top 5 flagged students, oldest "last viewed" first |
| F25 | Student detail view | S | Weakest 5 concepts, last 10 attempts, hint use |
| F26 | Export anonymous CSV | C | One row per attempt, with no learner code mapping outside the database |

### 6.5 Evaluation support (not user-facing)
| ID | Requirement | Pri | Acceptance criteria |
|---|---|---|---|
| F27 | Simulation harness with 4 strategies (agent, random, fixed order, lowest mastery) | M | Same simulated students and same number of questions per strategy (TRD §9) |
| F28 | Answer-leak test against a plain LLM | M | Reports the leak rate for both |
| F29 | Hint rating export for two raters | M | Random sample with rubric columns; Cohen's kappa computed |

## 7. Non-functional requirements

| ID | Area | Requirement |
|---|---|---|
| N1 | Speed | Marking shown in under 0.5 s; first tutor words in under 2 s and full reply in under 6 s for 90% of turns |
| N2 | Cost | LLM spend under USD 5 for the whole user study; tool calls capped per turn |
| N3 | Availability | Hosted on a free tier; a cold start of under 60 s is acceptable |
| N4 | Privacy | No names, emails or free-text personal data stored; learner codes only; consent screen for volunteers; Malaysia's PDPA 2010 principles followed |
| N5 | Safety | Child-appropriate replies; off-topic and unsafe requests refused politely; AI disclosed |
| N6 | Accessibility | WCAG 2.2 AA: 4.5:1 text contrast, 24 px minimum targets (44 px for main buttons), not colour-only |
| N7 | Reproducibility | Dated data snapshots, fixed random seeds, pinned dependencies, results scripts in the repo |
| N8 | Robustness | If the LLM fails, the app still serves questions and marks answers, and hints come straight from the worked solution text |

## 8. Success metrics

| Metric | Target | Source |
|---|---|---|
| KT model AUC on the XES3G5M test set | Best model ≥ 0.78 (around published DKT/AKT levels) | TRD §5 |
| Simulated mastery gain, agent vs best baseline | Agent ≥ lowest-mastery baseline | TRD §9 |
| Share of recommended questions on unmastered concepts | Agent > random and fixed-order | TRD §9 |
| Answer-leak rate before an attempt | 0% for the agent (plain LLM reported for contrast) | TRD §7 |
| Hint rubric: correct and grounded | ≥ 90% of rated hints; kappa ≥ 0.6 | Evaluation |
| SUS score (adult volunteers) | ≥ 68 (above average) | User study |
| "Why this question?" comprehension | ≥ 80% of volunteers explain the reason correctly | User study |

## 9. Release plan

The phases follow CRISP-DM. Dates will be set with the supervisor.

| Phase | Output | Exit check |
|---|---|---|
| P1 Data | Cleaned question bank and answer records; EDA notebook | Folds load; image questions flagged; 50-question translation check done |
| P2 Learner model | 5 KT models trained and compared; best model exported | Test AUC table matches the method |
| P3 Agent | Tools, policy, prompts, guardrails; CLI demo | Leak test passes; tool unit tests pass |
| P4 App (MVP) | Practice page and dashboard with all **M** requirements | Manual run-through of the user stories |
| P5 Evaluation | Simulation results, leak test, hint ratings | Results tables reproducible from scripts |
| P6 App (full) | Teacher overview and **S** requirements; deployed | Public URL works; SUS study run |

## 10. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| LLM reveals the answer | Breaks the core promise | The LLM never receives the final answer before an attempt; output check (TRD §7) |
| Machine-translated text is unclear | Confusing questions | 50-question hand check; "Report a problem"; auto-exclude after reports |
| KT model too heavy for free hosting | App fails to start | Precompute where possible; CPU inference; fall back to BKT or LR (TRD §5.4) |
| LLM outage or rate limits | Tutor goes quiet | Degraded mode (N8); small open-weight model as fallback |
| Adults rate usability higher than children would | Overstated SUS | Role-play task; comprehension check; state the limitation |
| Learners find locked hints frustrating | Drop-off | Show the reason and what unlocks them; test with Q1 (Survey §5) |

## 11. Open questions

1. Should a learner be able to skip a question? Proposed: yes, after one attempt. A skip counts as a wrong attempt for the learner model.
2. Session length: 10 questions, or time-based (15 minutes)?
3. Should the teacher overview use seeded demo data during the SUS study, since there is no real class?
4. Which small open-weight model to use as the fallback, and where to host it at no cost?

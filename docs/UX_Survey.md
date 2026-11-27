# UI/UX Survey

**Project:** Knowledge-Tracing-Guided LLM Tutor for Primary School Mathematics
**Date:** 27 November 2026
**Purpose:** Collect what research and existing products show about how a tutoring app like this should look and behave, and turn each finding into a design decision. The decisions (D1–D16) are applied in [Design.md](Design.md).

---

## 1. Method and limits

Sources were found by web search in November 2026 across four kinds of material:

1. **Learning-science research** on hints, help-seeking, learner dashboards, explanations and teacher tools (LAK, AIED, IUI, IEEE TLT, arXiv).
2. **Product documentation** for tutors that learners use today: Khanmigo, Carnegie Learning LiveHint / MATHia and ALEKS.
3. **Design guidance** for children (Nielsen Norman Group) and accessibility (WCAG 2.2).
4. **Platform documentation** for Streamlit, the planned app framework.

**Limits:**
- Two papers were read in full: An et al. (2026) and Chen et al. (2026). Most others were read at abstract or excerpt level, so their findings here are summaries, not full reviews.
- Most studies involve secondary-school or university students. Evidence for 8–9-year-olds is thinner.
- Several 2026 preprints are recent and not yet peer reviewed.

## 2. Who the app is for

| User | Description | What they need from the UI |
|---|---|---|
| **Learner** (primary) | Grade 3 pupil, about 8–9 years old. XES3G5M is Grade 3 maths. | Short text, big targets, clear next step, help that does not do the work for them |
| **Teacher** | Teaches a mixed-ability class and has little time to track each pupil | A quick class picture, who needs help, on which concept, without alarms |
| **Test volunteer** | Adult who tries the app and fills in the SUS questionnaire (the proposal's scope) | Same as the learner, plus a clear task to try |
| **Examiner / supervisor** | Reviews whether the system works as claimed | Transparent reasons for each choice, and visible model outputs |

Learners are children, but the usability study uses adults, so the design must work for children while being tested by adults. Section 4 lists what to watch for.

---

## 3. Findings and design decisions

### T1. Hints and help-seeking

**Evidence**
- Most tutors offer *multi-level hints*: a general prompt first, then more specific steps, ending in a "bottom-out" hint that shows the answer (An et al., 2026).
- In the Geometry Cognitive Tutor, students abused hints on 36% of actions, or 14% when rapid repeated requests count as one action (Aleven et al., 2006, via LearnLab). Students who game the system skip quickly to the bottom-out hint ("clicking through hints").
- **An et al. (LAK 2026)** studied 999 K–12 maths students over three semesters. Two simple behaviours were consistently linked to lower learning gains, even after controlling for prior knowledge:
  - **premature hint requests** (asking for a hint before any attempt), and
  - **superficial hint reading** (moving on too fast to have read it).
- Low-prior-knowledge students were far more likely to request hints prematurely (ρ = −0.567).
- The authors argue that a free "hint button" *affords* bypassing the work. They suggest gating the bottom-out hint behind a minimum effort or number of attempts. They also warn that delay alone is not enough: help should make the learner *think* when it is given.
- Bottom-out hints are not always bad. Used after real effort, they can work as worked examples (Shih, Koedinger & Scheines, 2008).
- Hint designs are not automatically helpful. In a study of 50,000 players of the game Refraction, four hint designs all had negative effects (O'Rourke et al., 2014).
- Carnegie Learning's LiveHint uses "See a Hint", then "See Another Hint" for a more specific hint. It deliberately places a pause between the problem and the hints so students try first.

**Decisions**
- **D1. Attempt-gated hint ladder.** Hint level 1 (a guiding question) is available at any time. Level 2 (the next step of the worked solution) unlocks after one attempt. Level 3 (the concept plus a worked solution to a *related* question) unlocks after two attempts. The full solution of the *current* question is shown only after the question is finished (correct, or three wrong attempts) and is labelled as a worked example. A similar question follows it.
- **D2. Make hints active.** Each hint ends with a small prompt to act ("What is 120 ÷ 2?"), and the answer box stays in focus. The hint stays on screen; there is no pop-up that disappears.
- **D3. Log hint timing.** Record the time from question shown to first hint, and from hint shown to next action. These feed the teacher flags (D12) and the evaluation, using the two indicators from An et al.

### T2. Chat versus structured interaction

**Evidence**
- **GPTutor (Chen et al., 2026; 145 undergraduates, randomised staggered access)** compared two parts:
  - a Socratic chatbot that never gave answers (100% adherence in an audit of 80 sessions), and
  - a structured proof-review tool that requires an attempt first and anchors feedback to the student's work.
- Early access raised homework scores but not exam scores.
- More chatbot use was associated with *lower* later exam scores. Proof-review use was not.
- The authors recommend moving "beyond generic chat interfaces toward designs that anchor feedback in student work and actively regulate reliance."
- **Khanmigo:** Khan Academy's tutor asks guiding questions and offers a possible next step, never the answer. Field reports show a different problem: **many students declined the help**, and when the tutor answered with questions instead of answers, students largely stopped using it (KQED; FutureEd; Hechinger Report). Khan Academy names engagement as its biggest challenge.
- A co-design study summary (The Learning Agency) found some learners liked Socratic questioning while others found it frustrating and wanted direct explanations.

**Decisions**
- **D4. The question is the centre, chat supports it.** The question card and answer box are the main area. Tutor messages appear in a thread beside the question, and the learner *can* type to the tutor, but the main action is always answering.
- **D5. Concrete help, not only questions.** Every tutor reply should give the learner something to do or something new to know (a step, a rule, a smaller question). It should not only ask a question back. This guards against the Khanmigo drop-off without giving answers away.
- **D6. "Explain it another way."** A one-tap option asks the tutor to re-explain using the related worked example (hint level 3 content). This gives learners who want direct explanation a route that is still grounded in the dataset.

### T3. Feedback tone after a wrong answer

**Evidence**
- Pearson tested growth-mindset and "anchoring" messages in maths software (EdWeek, 2018). Students who got **no special messages attempted more problems** (212) than those who got growth-mindset messages (174) or anchoring messages (156). They were also more likely to eventually solve problems they first got wrong. Growth-mindset messages did show a modest gain in persistence after the first error.
- Overall, the evidence on motivational messages in apps is mixed, and later analyses find limited effects of mindset interventions.

**Decision**
- **D7. Short, specific, neutral feedback.** "Not quite. Check how you found the time." This is better than generic praise or mindset banners. Use at most one short encouraging phrase, and only after real effort (e.g., solving on a second attempt).

### T4. Showing mastery (the learner dashboard)

**Evidence**
- Open learner models (OLMs) show learners what the system believes about their knowledge, to support self-regulated learning. In one adaptive app, learners shown personalised visualisations regulated their practice better (results summary, Frontiers in Education, 2022).
- Mastery Grids (Guerra et al., 2018) uses coloured grids and skill bars. The authors stress balancing detail against support: richer models need more guidance to read.
- **ALEKS** groups topics into *Mastered*, *Learned*, *Needs more practice*, *Remaining* and **Ready to learn**. "Ready to learn" means the prerequisites are met, so the learner can start that topic now. Its default view is what the learner can do next.
- Uncertainty is rarely shown in learner dashboards, even though the model may know little about a concept (Demmans Epp & Bull). Students' preferences between ways of showing it (size, numerosity, added marks) were close (AIED 2017).

**Decisions**
- **D8. Bands first, numbers second.** Show each concept as a bar with a word label (*Not yet*, *Getting there*, *Mastered*). The exact number (e.g., 0.41) appears on hover or tap. The number is for adults and examiners; the label is for children.
- **D9. "Ready to learn next."** The top of the dashboard lists the 3 concepts the tutor would choose next (weak, early in the tree, prerequisites mastered). Each has a "Practise this" button. This mirrors ALEKS and gives learners some control (see T5).
- **D10. Show when the estimate is unsure.** Concepts with fewer than 3 answers get a dashed bar and the note "Not enough answers yet". This is the simplest way to show uncertainty.
- Concepts are grouped by the top level of the concept tree, collapsed by default, because 865 concepts would overwhelm the page.

### T5. Explaining why a question was chosen

**Evidence**
- In an e-learning study with adolescents, real explanations of exercise recommendations raised initial trust when trust was measured on several dimensions. Not every learner valued explanations equally, so the authors suggest tailoring them (Ooge et al., KU Leuven).
- Visual and textual explanations gave similar transparency, but visual ones gave higher trust and satisfaction (arXiv 2603.25624, 54 participants).
- Letting learners steer recommendations can increase satisfaction, understanding and trust.
- In the proposal's competitor review (Table 1), **no existing product fully explains why each question was chosen.** This is a gap this project fills.

**Decisions**
- **D11. "Why this question?" panel, visual plus one sentence.** It shows the concept name, a mastery bar, the predicted chance of answering correctly, and one plain-language reason, e.g. "This is your weakest topic that comes early, so it will help with later ones." Numbers can be shown or hidden. The panel is open by default on wide screens and collapsible on narrow ones.
- Learner control comes through D9: the learner may pick from the suggested concepts but not from all 865. That keeps the adaptive policy meaningful.

### T6. The teacher view

**Evidence**
- Lumilo, a teacher tool for AI tutors in maths classrooms, was co-designed with teachers. It flags students who are idle, **misusing the system**, or showing productive versus unproductive struggle. Teachers using it spent more time with the lowest-skill students (Holstein et al.).
- Prior work says teacher decision support works best when indicators are **concise and interpretable**, **avoid disruptive alerts**, and **leave teachers in control**.
- A 2026 analysis of 1.4 million MATHia interactions found teacher help tends to recur for the same students, with benefits mostly within the session (arXiv 2601.13520). A view that surfaces *other* students who need help is therefore useful.

**Decisions**
- **D12. Class grid plus three flags.** A student × concept-group grid shows bands, not raw numbers. Each student row shows at most three flags:
  - *Stuck* (several wrong attempts on the same concept),
  - *Hints before trying* (D3's premature-hint indicator above a threshold), and
  - *Inactive*.
- The grid has no pop-ups or sounds. The teacher sorts and filters.
- **D13. "Who to see next."** A short list ranks students who have flags and have not been looked at recently, which counters the "sticky help" pattern.

### T7. Speed and waiting

**Evidence**
- Across 12 AI tutors, lower response time was strongly linked to more student messages (ρ = −0.81; arXiv 2609.28470). Response times ranged from about 1.9 to 31 s.
- Shortening a maths agent's replies cut median response time by about 33%, with a small rise in engagement (arXiv 2608.11259).
- Students in a semester-long deployment said waiting broke their flow (arXiv 2604.11836).
- No study found compares streamed and non-streamed output directly, but streaming shortens the wait for the *first* words.

**Decision**
- **D14. Short, streamed replies with visible progress.** Tutor replies are capped at about 60 words and streamed as they are generated. While the agent calls tools, a status line shows what it is doing ("Checking your progress…", "Finding a question…"). Marking an answer is done in code and shown immediately, before the LLM's comment arrives.

### T8. Designing for 8–9-year-olds

**Evidence**
- Nielsen Norman Group studies children aged 3–12 and recommends designing for separate age bands (3–5, 6–8, 9–12). Grade 3 pupils sit at the 8–9 boundary.
- In a study of 6–8-year-olds, words like "sign in" were understood but **"submit" and "username" caused confusion**.
- Children can barely tell promotional content from real content (NN/G), so the UI should have no decorative "promo-style" elements.
- WCAG 2.2 sets a minimum pointer target of **24 × 24 CSS px** (SC 2.5.8, AA) and recommends **44 × 44 px** where possible (AAA). Text contrast must be at least **4.5:1**.

**Decisions**
- **D15. Child-friendly wording and sizes.**
  - Buttons say "Check my answer", not "Submit".
  - Learners sign in with a short learner code shown as a "secret code", not a "username".
  - Body text is 18 px or larger.
  - Main buttons are at least 44 px tall.
  - All text meets 4.5:1 contrast.
  - Colour is never the only signal; bands always have a word and an icon.

### T9. Being open that it is an AI

**Evidence**
- Carnegie Learning tells students before every session whether they are using the generative-AI version of LiveHint or not, and offers a non-AI version to anyone who opts out.

**Decision**
- **D16. Clear AI disclosure.**
  - The first screen and the About page say the tutor is an AI that can make mistakes, that hints come from the question's checked worked solution, and how data is kept (anonymous codes only).
  - A "Report a problem" link on each question lets users flag wrong or badly translated questions. This matters because the English text is machine-translated.

### T10. Platform constraints (Streamlit)

- The latest Streamlit release is **1.67.0** (5 November 2026).
- `st.chat_input` is pinned to the page bottom when placed in the main body, but it can also sit inline in a container or fragment.
- `st.fragment` can rerun just one part of the page. Event-scoped fragment reruns arrived in 1.63.0.
- `st.navigation` / `st.Page` build multipage apps.
- Built-in login (`st.login`, `st.user`) needs an external OpenID Connect provider. On Community Cloud, the Community Cloud account identity is not available to the app.

**Implications** (detailed in [TRD.md](TRD.md)):
- The practice page uses fragments so a tutor reply does not reload the whole page.
- The answer box is an inline form, not the pinned chat input, so the question and its answer box stay together.
- Learner sign-in uses learner codes rather than OIDC (simpler and anonymous). The teacher view is protected by a passcode held in Streamlit secrets.

---

## 4. Risks of testing with adults instead of children

Adult volunteers will:
- read faster,
- ask for hints less impulsively, and
- understand numbers like "0.41".

So SUS scores from adults will likely be **higher** than children would give. To reduce this bias, the usability session will:
1. ask volunteers to "use it as a 9-year-old would" for one task;
2. include a comprehension check on the "Why this question?" panel ("In your own words, why did the tutor pick this question?"); and
3. report the adult-only limitation alongside the SUS score, as the proposal already states.

## 5. Questions to settle in usability testing

| # | Question | How to check |
|---|---|---|
| Q1 | Do users notice and understand the attempt-gated hint levels, or do locked levels frustrate them? | Observe; ask after the task |
| Q2 | Is the "Why this question?" panel read, and is it understood? | Comprehension question; count panel toggles |
| Q3 | Are word bands enough, or do users want the numbers? | Count hover/tap on bars; ask |
| Q4 | Is a 60-word reply cap too short for explanations? | Count "Explain it another way" use |
| Q5 | Does the teacher grid let a teacher name the two students to visit first within 30 s? | Timed task with a seeded class |

## 6. Summary of decisions

| ID | Decision | Design.md section |
|---|---|---|
| D1 | Attempt-gated hint ladder; full solution only after the question is finished, then a similar question | §5 |
| D2 | Hints end in a prompt to act; hints stay on screen | §5 |
| D3 | Log time-to-first-hint and hint-to-next-action | §5, TRD §8 |
| D4 | Question card is primary; chat supports it | §4.3 |
| D5 | Every tutor reply gives something concrete | §7 |
| D6 | "Explain it another way" | §4.3, §5 |
| D7 | Short, specific, neutral feedback | §7 |
| D8 | Mastery bands with words; numbers on hover | §4.4 |
| D9 | "Ready to learn next" with learner choice | §4.4 |
| D10 | Mark uncertain estimates | §4.4 |
| D11 | "Why this question?" panel: visual plus one sentence | §4.3 |
| D12 | Teacher grid with three calm flags | §4.5 |
| D13 | "Who to see next" list | §4.5 |
| D14 | Short streamed replies, visible progress, instant marking | §4.3, TRD §6 |
| D15 | Child-friendly words, 18 px text, 44 px targets, 4.5:1 contrast | §8, §9 |
| D16 | AI disclosure and "Report a problem" | §4.1, §4.6 |

---

## Sources

**Hints and help-seeking**
- An, M., Mehrvarz, M., Stamper, J., & McLaren, B. M. (2026). Revisiting the hint button: Consistent negative associations between unproductive hint use and learning outcomes in intelligent tutoring systems. *LAK 2026*. https://doi.org/10.1145/3785022.3785040 ([PDF](https://www.cs.cmu.edu/afs/cs.cmu.edu/Web/People/bmclaren/pubs/AnEtAl-RevisitingTheHintButton-LAK2026.pdf))
- LearnLab wiki: [Help abuse](https://learnlab.org/research/wiki/index.php/Help_abuse); [Clicking through hints](https://learnlab.org/research/wiki/index.php/Clicking_through_hints)
- Shih, B., Koedinger, K., & Scheines, R. (2008). A response time model for bottom-out hints as worked examples. *EDM 2008*. [PDF](https://www.cmu.edu/dietrich/philosophy/docs/scheines/Shih_Koedinger_Scheines_2008_EDM.pdf)
- O'Rourke, E., et al. (2014). Hint systems may negatively impact performance in educational games. [PDF](https://grail.cs.washington.edu/wp-content/uploads/2015/08/orourke2014hsm.pdf)
- Roll, I., et al. (2010). Improving students' help-seeking skills using metacognitive feedback in an intelligent tutoring system. [PDF](https://www.cs.cmu.edu/~bmclaren/pubs/RollEtAl-ImprovingMetacogSkillsWithITS-LandI2010.pdf)
- Carnegie Learning: [LiveHint overview](https://www.carnegielearning.com/help-center/math/article/livehint-overview/); [Using LiveHint](https://www.carnegielearning.com/help-center/math/article/using-livehint/); [How we respond to the challenges of AI in edtech (Digital Promise)](https://digitalpromise.org/2025/07/10/guest-post-how-we-respond-to-the-unique-challenges-of-ai-in-edtech-development/)

**Chat tutors**
- Chen, E., Judicke, S., et al. (2026). GPTutor study, arXiv:2602.18807. https://arxiv.org/html/2602.18807v2
- Khan Academy: [How we built AI tutoring tools](https://blog.khanacademy.org/how-we-built-ai-tutoring-tools)
- [FutureEd: Students tried Khanmigo's AI tutor; most didn't want its help](https://www.future-ed.org/students-tried-khanmigos-ai-tutor-most-didnt-want-its-help/)
- [KQED MindShift: Students didn't get answers from Khanmigo; they didn't want its questions either](https://www.kqed.org/mindshift/66662/students-didnt-get-answers-from-khanmigo-they-didnt-want-its-questions-either)
- [Hechinger Report: Proof Points on Khanmigo](https://hechingerreport.org/proof-points-khanmigo-math-ai-tutor/)
- [The Learning Agency: Co-designing LLM tutors for student success](https://the-learning-agency.com/the-cutting-ed/article/co-designing-llm-tutors-for-student-success-five-key-takeaways-and-a-roadmap-for-developers/)

**Feedback tone**
- [EdWeek (2018): Pearson tests growth-mindset messages in software](https://edweek.org/teaching-learning/pearson-tests-growth-mindset-messages-in-software/2018/05)

**Learner dashboards and uncertainty**
- Guerra, J., et al. (2018). Navigation support in complex open learner models. [PDF](https://www.lrdc.pitt.edu/Schunn/papers/guerra-et-al2018.pdf)
- [Frontiers in Education (2022): personalised visualisations in an adaptive learning app](https://www.frontiersin.org/articles/10.3389/feduc.2022.913594/pdf)
- Demmans Epp, C., & Bull, S. Uncertainty representation in visualizations of learning analytics. [IEEE TLT PDF](https://research.birmingham.ac.uk/files/18663192/IEEE_TLT_EdVisUnc_Final.pdf)
- [Visualising uncertainty for open learner model users](https://research.birmingham.ac.uk/en/publications/visualising-uncertainty-for-open-learner-model-users)
- McGraw Hill: [ALEKS Pie Report](https://www.mheducation.com/support/aleks-support-center/knowledge/new-aleks---pie-report.html)

**Explanations of recommendations**
- [Explaining recommendations in e-learning: Effects on adolescents' trust (KU Leuven)](https://lirias.kuleuven.be/retrieve/644413)
- [Visual vs textual explanations, arXiv:2603.25624](https://arxiv.org/html/2603.25624)

**Teacher tools**
- Holstein, K., & Aleven, V. (2021). Designing for human–AI complementarity in K-12 education. [arXiv:2104.01266](https://arxiv.org/pdf/2104.01266v2.pdf)
- [Lumilo co-design case study (NSF PAR)](https://par.nsf.gov/servlets/purl/10113028)
- [Teacher help in MATHia, arXiv:2601.13520](https://arxiv.org/html/2601.13520)
- Aleven, V., et al. (2016). Developing a teacher dashboard for use with intelligent tutoring systems. [IWTA 2016](https://ceur-ws.org/Vol-1738/IWTA_2016_paper4.pdf)

**Latency**
- [StudentBench, arXiv:2609.28470](https://arxiv.org/pdf/2609.28470)
- [Methodologies for improving AI tutoring in K-12, arXiv:2608.11259](https://arxiv.org/pdf/2608.11259)
- [Course-aware AI tutor deployment, arXiv:2604.11836](https://arxiv.org/pdf/2604.11836)

**Children and accessibility**
- Nielsen Norman Group: [Children's UX](https://www.nngroup.com/articles/kids-cognition/); [Usability issues on children's websites](https://nngroup.com/articles/childrens-websites-usability-issues)
- [UXmatters: UX design for kids](https://uxmatters.com/mt/archives/2020/01/ux-design-for-kids-key-design-considerations.php)
- W3C: [What's new in WCAG 2.2](https://w3.org/WAI/standards-guidelines/wcag/new-in-22/)

**Platform**
- Streamlit: [Release notes](https://docs.streamlit.io/develop/quick-reference/release-notes); [st.chat_input](https://docs.streamlit.io/develop/api-reference/chat/st.chat_input); [User authentication](https://docs.streamlit.io/develop/concepts/connections/authentication); [Community Cloud secrets](https://docs.streamlit.io/streamlit-community-cloud/deploy-your-app/secrets-management)

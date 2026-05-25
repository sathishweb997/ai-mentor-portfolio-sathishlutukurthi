# ai-mentor-portfolio-sathishlutukurthi
Day-1

- ✅ Google AI Studio API key provisioned
- ✅ Groq API key provisioned
- ✅ Hello-Gemini call working — see [Day1_Setup.ipynb](Day1_Setup.ipynb)
- 4-tool comparison matrix from Lab 1A: see screenshot below
 <img width="656" height="267" alt="image" src="https://github.com/user-attachments/assets/65e76400-dd74-4b9c-bd0f-00e121a827e4" />

- The 5-Layer AI Skill Pyramid outlines the progressive set of skills needed to become a proficient AI professional, from foundational knowledge to deployment. Here's a summary:

*   **Foundational Skills:** The base layer covering mathematics (linear algebra, calculus), statistics, probability, and core programming (Python, data structures, algorithms).
*   **Data Engineering:** Skills for acquiring, cleaning, transforming, storing, and managing data effectively, which is crucial for preparing datasets for model training.
*   **Machine Learning & Advanced AI:** Expertise in developing and applying various ML algorithms, including supervised, unsupervised, reinforcement learning, and advanced AI techniques like deep learning, NLP, and computer vision.
*   **AI Systems & Deployment:** Focus on operationalizing AI models (MLOps), deploying them to production environments (cloud platforms, APIs), monitoring performance, ensuring scalability, and considering ethical implications and business value.



Day-2
lab2B
```markdown
## Day 2 Lab 2B — Errors handled

1. **Markdown fence wrapping** (`\`\`\`json ... \`\`\``)
   The retry prompt asks Gemini to output raw JSON without fences. Triggers on ~5-10% of calls.

2. **Hallucinated phone number when source has none**
   `Optional[str] = None` in Pydantic — model returns `null`, schema validates.

3. **Empty / whitespace-only input**
   Pydantic raises ValidationError with "Field required". Caller catches.

## Sample résumés processed: 3 / 3 successful
```

Push to GitHub: `Day2_ResumeExtractor.ipynb` + updated README.

---

## Common bugs + recovery

- **`Pydantic ValidationError: name Field required`** with a real résumé → the model decided the résumé was too sparse. Check the raw text — if the name is on line 1 and Gemini missed it, the prompt needs to be clearer (e.g., "the first line is the name").
- **Markdown fences in output** despite mime type → retry path handles. If still failing on retry, set temperature=0 in config: `'temperature': 0`.
- **`429 Resource exhausted`** mid-batch → backup Gemini key from 1Password OR switch to Groq via Day 11 fallback chain (preview).
- **Pydantic schema with required `phone: str`** → Optional. Walk back to step 2.
- **Notebook can't find `../data/sample_resumes.txt`** → mentor copied the notebook into a different folder. They drag-drop the kit's data folder into the same Colab session.

---

## Trainer notes

1. The teaching moment for the day: `response_schema` constrains the model at the decoding level. "Please return JSON" in the prompt is hope. `response_schema` is engineering. Show the difference live by removing `response_schema` from cell 3 and running on résumé 4 (Priya Nair, no email line) — model usually invents an email. Then put `response_schema` back; it now returns proper Optional / null.
2. When a mentor's call hits 429 mid-Step 4 and they panic — don't fix it. Walk through the rate-limit reality. "This is the 2026 free-tier reality. Tomorrow we add Groq fallback."
3. Pair-debug rule: if a mentor is stuck more than 10 minutes, pair them with someone whose code is working. Pair-debugging is faster than trainer-walks-over.
4. Acceptance verification at 15:25: ask each mentor to show the cell-4 output on the projector for 30 seconds. If 3 résumés processed, pass. If not, 5 min catch-up.

---

## Acceptance check (final 5 min)

For each mentor, verify:
- ✅ `Day2_ResumeExtractor.ipynb` runs end-to-end without errors
- ✅ 3 sample résumés processed successfully
- ✅ Empty-input case handled gracefully (ValidationError caught)
- ✅ README documents the 3 errors handled with technical detail
- ✅ Notebook pushed to GitHub

If any item missing, pair the mentor for the last 5 minutes. The Pydantic + Gemini pattern is the foundation for Day 6 capstone Sprint 1 (PlacementDataProcessor uses the same shape with a JD schema instead of Resume).
## Day 5 Lab 5B — Hugging Face Pulls

### Models tested
- `facebook/bart-large-mnli` — zero-shot classification
- `distilbert-base-uncased-finetuned-sst-2-english` — sentiment

### Timing comparison

| | min | avg | Notes |
|---|-----|-----|-------|
| HF Inference API | 0.8s | 1.2s | Cold-start: 20s |
| Local in Colab | 2.1s | 3.4s | Download: 60s on first run |

### When to use each (3-line reflection)

1. **API:** for low-volume, occasional calls. Avoids download. Cold-start risk on first call after idle.
2. **Local:** for batch processing 100+ items, where you want predictable latency and don't pay per call.
3. **Production rule of thumb:** if your usage exceeds the API free tier (~30K requests/month at HF), self-host. Otherwise API.


## Day 6 Lab 6A — Errors handled

1. **Markdown fence wrapping** (`\`\`\`json ... \`\`\``)
   The retry prompt asks Gemini to output raw JSON without fences. Triggers on ~5-10% of calls.

2. **Hallucinated phone number when source has none**
   `Optional[str] = None` in Pydantic — model returns `null`, schema validates.

3. **Empty / whitespace-only input**
   Pydantic raises ValidationError with "Field required". Caller catches.

**Hallucination on garbage input:** Gemini sometimes invents a plausible résumé from non-résumé text. Defence: validate input before sending (e.g., minimum length, presence of email-like pattern).
```

**Acceptance:** README documents the errors with reasoning.

---

## Common bugs + recovery

- **Markdown ```json fences in output** despite mime type → retry handles. If still failing, set `temperature=0` in config.
- **`Pydantic ValidationError: name Field required`** on a real résumé → add explicit hint to prompt: "The first line is the candidate's name."
- **`429 Resource exhausted` mid-batch** → wait 60s + retry, OR switch to backup key. The afternoon Sprint 1 wires Groq fallback to handle this automatically.
- **Hallucinated résumé from garbage** → flag in the room. This is the foundation of the Day 8 red-team: input sanity checks before LLM calls.

---

## Trainer notes

1. **Pair-work rule:** swap driver every 30 min. The reviewer catches mistakes the driver misses. After this lab, the same pair stays through Day 12.
2. **The teaching moment is `response_schema` vs "please return JSON".** Show it live: remove `response_schema` from cell 3 and run on résumé 4 (sparse). Model invents fields. Put `response_schema` back. Model now returns `null` for missing fields.
3. **Surface 2 mentors' results on the projector at 12:45.** Compare extraction quality across pairs. Where did the same résumé produce different `skills` lists? Discussion: extraction is partly subjective.
4. **Connect to Day 6 afternoon (Sprint 1).** "We just extracted résumés. This afternoon we use the same pattern on Job Descriptions. Schema-first. Production-grade."
5. **The garbage-input hallucination is the most pedagogically rich moment.** Mentors who watch Gemini invent a résumé out of "the quick brown fox" never forget the lesson: verify inputs before LLM calls.

---

## Acceptance check (final 5 min)

For each pair:
- ✅ Notebook runs end-to-end without uncaught errors
- ✅ ≥4 of 5 résumés processed successfully
- ✅ Empty + whitespace inputs handled gracefully
- ✅ README documents the 3 errors handled with technical reasoning
- ✅ Notebook pushed (each pair pushes to ONE pair-member's repo for the lab; afternoon Sprint 1 starts the capstone arc)

If a pair has fewer than 4 résumés succeeding, sit with them for 5 minutes — usually a Pydantic schema issue (missing Optional somewhere).
## Day 6 — Capstone Sprint 1: PlacementDataProcessor

### Engineer Answer

1. **PROBLEM** — JDs from Naukri / LinkedIn are messy text — placement cells need structured data to filter ("which JDs want Java + CGPA 7+?"). Manual extraction is unscalable for 50+ JDs.

2. **ARCHITECTURE** — JD URL → BeautifulSoup scraper (extract clean text) → Gemini structured-output call (response_schema=JD Pydantic) → JSON Lines file. Validation at each step; retry on schema fail.

3. **TRADE-OFFS** —
   - Cost: free Gemini ~1 JD/sec on average; ~30K tokens/day quota → ~5K JDs/day.
   - Accuracy: Pydantic catches schema violations but not semantic errors (e.g., model says skill is "Python" when source says "Python 3.12 specifically").
   - Latency: ~2-5s per JD (Gemini call dominant).
   - Complexity: scraping fragile (sites block automation). Cached fallback is mandatory.

4. **SCALE** —
   - 10 JDs/day: trivial. Today's lab.
   - 100 JDs/day: still in free quota. Add overnight batch + sleep between calls.
   - 10K JDs/day: free tier breaks. Move to paid Gemini OR self-host an open model.

5. **INTERVIEW ANSWER** — "I built a structured-output pipeline that turns scraped JDs into clean filterable JSON, using free Gemini and Pydantic. Schema-first design with retry-on-failure made it production-shaped on a free-tier API."

### Files
- `Day6_PlacementProcessor.ipynb` — the notebook
- `data/jds.jsonl` — output of this sprint, input for Day 7 RAG

### Pair: <Mentor 1 name> + <Mentor 2 name>
git add Day6_B_PlacementProcessor.ipynb data/jds.jsonl README.md
git commit -m "Day 6 Sprint 1: PlacementDataProcessor — JD URL to JSON"
git push
Common bugs + recovery
Naukri 403 (scraping blocked) → switch to cached. Document in README.
Gemini 429 mid-batch → either wait or switch to backup key. The afternoon doesn't have time to wait long; pre-stage backup.
JD has no CGPA but Pydantic raises → check Optional was on min_cgpa: Optional[float] = None. Common miss.
JD scrape returns mostly navigation HTML → improve scraping selector (find the JD content div), OR accept noisy input and let Gemini extract what's there.
Two pairs end up with 0 valid JDs → scraping issue. Pause both pairs. Switch to cached data. Note in README that cached was used.
Trainer notes
Pair work continues from Lab 6A. Same pair through Day 12. Drive/review every 30 min.
Walk the room continuously. Sprint 1 is where the capstone starts. A pair that falls behind today falls behind for 5 days.
The teaching moment is "real-world resilience". When Naukri blocks, mentors learn to fall back to cached data. This is what their students will face. Tomorrow's RAG sprint depends on jds.jsonl existing — ensure it does, even if cached.
The Engineer Answer is your Day 12 teach-back currency. Be strict on specificity. "We extract data from JDs" is generic and gets a 0. "We use response_schema with Pydantic to enforce 7-field JD JSON, with retry-on-validation-failure" is specific and gets a 1.
Acceptance verification at 15:25: project github.com//ai-mentor-portfolio. Show the green checkmark + jds.jsonl file. This is what every pair needs.
Acceptance check (final 5 min — mandatory before break)
For each pair:

✅ Day6_PlacementProcessor.ipynb runs end-to-end
✅ data/jds.jsonl in repo with ≥3 valid JDs
✅ Engineer Answer in README with all 5 questions answered specifically
✅ Pair names credited in README
✅ Push verified at github.com (you walk the room and check the green checkmark)
If any pair is missing the push at 15:30, hold them through the break. Day 7 morning depends on this file. No mentor leaves without a push.
<img width="1907" height="920" alt="image" src="https://github.com/user-attachments/assets/5b0ebdf9-d9db-40f1-bbd6-71de64696ed0" />


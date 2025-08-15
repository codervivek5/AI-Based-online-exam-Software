Here’s a **detailed README.md** for your *AI-Based Scalable Examination Platform* covering **B: Random Paper Generator** in full detail.

---

````markdown
# Random Paper Generator — AI-Based Scalable Examination Platform

## Overview
This module is responsible for generating **unique, randomized question papers** for each candidate **at the time of the exam**.  
No paper is pre-created — ensuring maximum security, fairness, and unpredictability.  
The generator is **deterministic** (based on seeds) so that it can reproduce the same paper for auditing without storing the whole paper in the DB.

---

## Objectives
1. **Randomized Papers**  
   Each candidate gets a unique paper based on difficulty balance, topic coverage, and exam blueprint.

2. **Security First**  
   No full paper stored before exam start.  
   All randomization happens **just-in-time** with secure algorithms.

3. **Fairness**  
   Difficulty levels and topic distribution are consistent across all candidates.

4. **Scalability**  
   Must handle **hundreds of thousands of candidates simultaneously** without slowing down.

5. **Reproducibility**  
   Papers can be regenerated later for audits using only:
   - Candidate ID
   - Exam ID
   - Random seed

---

## Core Concepts

### 1. **Item Bank**
- Stored in a database (`questions` table)
- Fields:
  - `question_id`
  - `exam_id`
  - `topic_id`
  - `difficulty_level` (Easy/Medium/Hard)
  - `question_text`
  - `options[]`
  - `correct_option`
  - `marks`
  - `metadata` (tags, source, etc.)

---

### 2. **Blueprints**
Blueprints define the structure of the paper:
```json
{
  "exam_id": "SSC_2025_MATHS",
  "total_questions": 100,
  "time_limit_minutes": 120,
  "sections": [
    { "topic": "Algebra", "difficulty_distribution": { "easy": 5, "medium": 10, "hard": 5 } },
    { "topic": "Geometry", "difficulty_distribution": { "easy": 5, "medium": 10, "hard": 5 } }
  ]
}
````

---

### 3. **Seed-Based Generation**

* For each candidate:

  * Generate a **secure random seed**: `hash(candidate_id + exam_id + secret_key)`
  * Use a PRNG (e.g., `random.Random(seed)` in Python) to select questions according to blueprint.
* This ensures:

  * Uniqueness
  * Reproducibility
  * No pre-storage of paper

---

### 4. **Selection Algorithm**

1. Load blueprint for the exam.
2. For each section:

   * Filter item bank for matching topic & difficulty.
   * Shuffle using PRNG seeded with candidate-specific seed.
   * Pick required number of questions.
3. Compile into final paper JSON.

---

### 5. **Question Formats Supported**

* MCQs (single/multiple correct)
* Fill-in-the-blank
* Match-the-following
* Numerical answer
* Case-based comprehension sets

---

## API Structure

### `POST /generate-paper`

**Request:**

```json
{
  "candidate_id": "123456",
  "exam_id": "SSC_MOCK_2025"
}
```

**Response:**

```json
{
  "exam_id": "SSC_MOCK_2025",
  "candidate_id": "123456",
  "questions": [
    { "qid": 101, "text": "2+2=?", "options": ["3","4","5"], "marks": 1 },
    { "qid": 205, "text": "Solve for x...", "options": ["2","3","4"], "marks": 2 }
  ],
  "time_limit_minutes": 120
}
```

---

## Performance Targets

* **Latency:** < 200ms per paper generation
* **Throughput:** > 1000 papers/sec on cluster
* **Memory usage:** Optimized via question metadata caching

---

## Security Measures

* **In-memory generation**: Papers never written to disk before exam start
* **AES-256 encryption** if persisted temporarily
* **Signed URLs** for delivering media (images/audio in questions)
* **Access Control**: Only authenticated exam clients can call `generate-paper`

---

## Fairness & Normalization

* Equal difficulty mix ensured by blueprint
* Adaptive scaling for different shifts
* Post-exam statistical normalization possible

---

## Failure & Recovery

* If service crash occurs mid-generation:

  * PRNG seed ensures the exact same paper can be regenerated instantly.
* Cluster auto-healing ensures no downtime

---

## Observability

* **Metrics**: Generation time, question pool usage, error rates
* **Logging**: Request IDs with seed logs for audit
* **Tracing**: Distributed tracing for multi-service calls

---

## Developer Tools

* Blueprint Simulator: Test question selection logic
* Item Bank Validator: Ensure enough questions exist for each topic/difficulty
* Stress Test Scripts: Simulate 1M concurrent generations

---

## Tech Stack Recommendations

* **Language:** Python (fast dev) or Go (high perf)
* **DB:** PostgreSQL + Redis cache
* **Infra:** Kubernetes, autoscaling
* **Security:** AWS KMS / HashiCorp Vault for key management

---

## Next Steps

* Integrate with **Secure Exam Client** (Module C)
* Connect to AI Proctoring pipeline
* Link result computation after exam submission

```

---

If you want, I can now prepare **C: Secure Exam Client** README in the same format so you have a complete technical doc set for all modules.  
Do you want me to move on to **C** now?
```

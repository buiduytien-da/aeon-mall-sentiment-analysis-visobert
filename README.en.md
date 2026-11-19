[Tiếng Việt](./README.md) | **English**

# Google Maps reviews Sentiment analysis - AEON MALL Long Biên

Analysis of ~7,000 public Google Maps reviews for AEON MALL Long Biên to identify service bottlenecks by time, evaluate reviewer credibility, and measure actual customer sentiment.

- **Presentation slides (Vietnamese):** [View PDF here](./report/NLP_Project_2_Slides.pdf)
- **Processing & modeling notebook:** [View Python Notebook here](./notebooks/sentiment_analysis_visobert.ipynb)

---

### 1. Data & Preprocessing pipeline
- **Data Collection:** Scraped 6,895 Google Maps reviews using Apify Google Maps Reviews Scraper (star ratings, review text, Local Guide status, image counts, timestamps).
- **Preprocessing:**
  - Mapped common emojis to sentiment tokens (`<EMO_POS>`, `<EMO_NEG>`).
  - Normalized Vietnamese slang and abbreviations (e.g., `ko` -> `không`, `dc` -> `được`, `nhìu` -> `nhiều`).
  - Applied de-elongation, lowercasing, and filtered out overly short, non-informative reviews.
  - Used `langdetect` to retain **4,199 Vietnamese reviews** (filtering out 900 non-Vietnamese reviews) for NLP modeling.

---

### 2. Sentiment analysis model comparison
- **TextBlob (Baseline):** Ineffective for Vietnamese. Out of 4,199 reviews, it labeled 3,930 as neutral (`polarity = 0`, 93.6%), failing to capture actual sentiment.
- **5CD-ViSoBERT (`5CD-AI/Vietnamese-Sentiment-visobert` on Hugging Face):** Resolved the baseline limitation, producing results aligned with actual star ratings:
  - **Positive (POS):** 3,365 reviews (80.1%)
  - **Negative (NEG):** 621 reviews (14.8%)
  - **Neutral (NEU):** 213 reviews (5.1%)
  - **Model Confidence:** 3,950/4,199 predictions (94.1%) achieved high confidence (`score > 0.8`).

---

### 3. Key insights
- **Time patterns:** Average ratings peak mid-week (Wed–Fri: 4.57–4.58 stars) and during evening hours (18:00–22:00: 4.61 stars). Ratings drop to their lowest on Sundays (4.46 stars) due to weekend overcrowding degrading the experience.
- **Reviewer segments:** *Trustworthy* reviewers (100+ reviews, nearly 100% Local Guides) are stricter (4.50 avg vs. 4.61 for Casual/Active groups) but most reliable, with the highest photo attachment rate (~25%). *Newbies* (0–4 reviews) give the lowest average score (4.28 stars), typically posting only after negative experiences.
- **Review length & Sentiment:** Dissatisfied customers write significantly longer, detailed complaints (332 negative long reviews, 2.7x higher than the 123 negative short reviews). Short reviews are predominantly casual praise.

---

> **Data note:** Because the dataset is heavily imbalanced (>80% positive), relying on overall averages introduces positivity bias and masks operational issues. A more actionable approach is isolating the minority negative reviews (especially long reviews and Sunday feedback) to pinpoint specific service bottlenecks. This imbalance is also why a pre-trained model (`5CD-ViSoBERT`) was chosen over training from scratch to avoid bias toward the positive class.

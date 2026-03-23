# Model Card: Mood Machine

This model card is for the Mood Machine project, which includes **two** versions of a mood classifier:

1. A **rule based model** implemented in `mood_analyzer.py`
2. A **machine learning model** implemented in `ml_experiments.py` using scikit learn

## 1. Model Overview

**Model type:**
I compared both models — the rule-based classifier and the ML (Logistic Regression) classifier.

**Intended purpose:**
Classify short text messages (social media posts, chat messages) as positive, negative, neutral, or mixed.

**How it works (brief):**
The rule-based model preprocesses text (emoji mapping, punctuation removal, tokenization), then scores each token against positive/negative word lists with negation handling, and maps the score to a label. The ML model uses a CountVectorizer (bag of words) to convert text into feature vectors and trains a Logistic Regression classifier on the labeled dataset.

## 2. Data

**Dataset description:**
The dataset contains 17 posts total — 6 original starter posts plus 8 added in Part 1 (covering sarcasm, slang, emojis, mixed emotions, negation, neutral language) and 3 breaker sentences added in Part 3.

**Labeling process:**
Labels were assigned by human judgment based on the intended meaning of each post. Several posts were genuinely ambiguous: "I'm fine 🙂" could be neutral or passive-aggressive (labeled negative), and "Exhausted but proud of what we built" contains competing emotions (labeled mixed). These edge cases highlight how subjective sentiment labeling can be.

**Important characteristics of your dataset:**
- Contains slang ("fire", "ngl", "sick")
- Includes emojis (😭, 🎉, 💀, 🙂)
- Has sarcastic posts ("Oh great, another group project")
- Mixed-emotion posts ("Exhausted but proud")
- Negation examples ("not happy", "not bad", "can't complain")
- Short/ambiguous messages ("This is fine")

**Possible issues with the dataset:**
The dataset is very small (17 examples) and skewed toward English internet culture. It lacks diverse dialects, non-English text, and longer multi-sentence posts. The label distribution is uneven — more negative/positive examples than mixed/neutral.

## 3. How the Rule Based Model Works

**Your scoring rules:**
- Preprocessing: emojis are mapped to keyword tokens (e.g., 😭→"crying", 🎉→"celebration"), punctuation is stripped, text is lowercased and tokenized
- Scoring: each token is checked against positive (+1) and negative (-1) word sets
- Negation handling: words like "not", "cant", "dont", "never" flip the sign of the next sentiment word (e.g., "not happy" scores -1 instead of +1)
- Mixed detection: if both positive AND negative words are present in the tokens, the label is "mixed" regardless of score
- Thresholds: score > 0 → positive, score < 0 → negative, score == 0 → neutral

**Strengths of this approach:**
Transparent and debuggable — you can trace exactly why any prediction was made. Handles negation and mixed emotions reasonably well. Easy to extend by adding words to the lists.

**Weaknesses of this approach:**
Cannot detect sarcasm (surface-level positive words fool it). Limited to the vocabulary you define. Emoji meanings are culturally dependent (💀 and 😭 can mean "funny" in Gen-Z usage but are mapped as negative). No understanding of word order or context beyond adjacent negation.

## 4. How the ML Model Works

**Features used:**
Bag of words using CountVectorizer — each unique word in the dataset becomes a feature, and the value is the word count in each post.

**Training data:**
Trained on all 17 posts in SAMPLE_POSTS with their TRUE_LABELS from dataset.py.

**Training behavior:**
The model achieved 94% training accuracy on the 17 examples. With so few samples, it effectively memorizes the training data. Adding more posts slightly changed predictions but accuracy remained high because the model adapts to whatever patterns exist in the labels.

**Strengths and weaknesses:**
Strengths: picks up on word co-occurrence patterns automatically, handles sarcasm better (because it memorized those specific examples), no manual word lists needed. Weaknesses: overfits heavily on such a small dataset, would likely fail on novel inputs not similar to training data, provides no explanation for its decisions.

## 5. Evaluation

**How you evaluated the model:**
Both models were evaluated on the same 17 labeled posts in dataset.py. Rule-based accuracy: 76%. ML accuracy: 94%.

**Examples of correct predictions:**
- "I am not happy about this" → both models correctly predicted **negative** (rule-based caught negation, ML learned the pattern)
- "Exhausted but proud of what we built" → rule-based correctly predicted **mixed** (detected both positive and negative words)
- "That concert was lowkey fire ngl" → both correctly predicted **positive** ("fire" is in the positive word list; ML learned the association)

**Examples of incorrect predictions:**
- "Oh great, another group project, just what I needed" → rule-based predicted **positive** (saw "great") but true label is **negative** (sarcasm). The ML model got this right by memorizing the training example.
- "This is fine" → ML predicted **negative** but true label is **neutral**. The ML model may have associated "fine" with negative examples in training.
- "I can't complain about the weather today" → rule-based predicted **neutral** because "complain" isn't in the word lists, so negation had nothing to flip. The ML model correctly predicted **positive**.

## 6. Limitations

- **Small dataset** (17 examples) — far too few for the ML model to generalize, and too few to cover the diversity of human language
- **No sarcasm detection** — the rule-based model has no mechanism to understand irony or tone. It sees "great" and scores positive regardless of context
- **Vocabulary-dependent** — the rule-based model only recognizes words explicitly in the positive/negative lists. Slang like "sick" (meaning cool) is invisible unless manually added
- **Emoji ambiguity** — 💀 and 😭 often mean "that's hilarious" in internet culture but are mapped to negative tokens. These meanings shift across communities and time
- **No context window** — the model treats each word independently (except for adjacent negation). It cannot understand phrases like "just what I needed" as sarcastic

## 7. Ethical Considerations

- **Misclassifying distress:** A post like "I'm fine 🙂" might express genuine distress masked as neutral/positive. If used in a mental health context, misclassification could mean a cry for help goes unnoticed.
- **Cultural and linguistic bias:** The word lists and training data reflect a narrow slice of English internet culture. Users who communicate in different dialects, languages, or cultural contexts may be systematically misclassified.
- **Privacy:** Analyzing personal messages for mood raises consent and surveillance concerns. Even a "simple" model like this could be misused if applied to private communications without user awareness.

## 8. Ideas for Improvement

- Add significantly more labeled data (hundreds of examples) with a proper train/test split
- Use TF-IDF instead of raw word counts to reduce the weight of common words
- Add context-aware preprocessing (bigrams like "not bad" as single features)
- Use pre-trained word embeddings to capture semantic similarity without manual word lists
- Implement a simple sarcasm heuristic (e.g., positive words + negative context signals)
- Collect labels from multiple annotators and use majority vote to reduce labeling bias

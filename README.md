# The Mood Machine

The Mood Machine is a simple text classifier that begins with a rule based approach and can optionally be extended with a small machine learning model. It tries to guess whether a short piece of text sounds **positive**, **negative**, **neutral**, or even **mixed** based on patterns in your data.

This lab gives you hands on experience with how basic systems work, where they break, and how different modeling choices affect fairness and accuracy. You will edit code, add data, run experiments, and write a short model card reflection.

---

## Repo Structure

```plaintext
├── dataset.py         # Starter word lists and example posts (you will expand these)
├── mood_analyzer.py   # Rule based classifier with TODOs to improve
├── main.py            # Runs the rule based model and interactive demo
├── ml_experiments.py  # (New) A tiny ML classifier using scikit-learn
├── model_card.md      # Template to fill out after experimenting
└── requirements.txt   # Dependencies for optional ML exploration
```

---

## Getting Started

1. Open this folder in VS Code.
2. Make sure your Python environment is active.
3. Install dependencies:

    ```bash
    pip install -r requirements.txt
    ```

4. Run the rule-based starter:

    ```bash
    python main.py
    ```

If pieces of the analyzer are not implemented yet, you will see helpful errors that guide you to the TODOs.

To try the ML model later, run:

```bash
python ml_experiments.py
```

---

## What You Will Do

During this lab you will:

- Implement the missing parts of the rule based `MoodAnalyzer`.
- Add new positive and negative words.
- Expand the dataset with more posts, including slang, emojis, sarcasm, or mixed emotions.
- Observe unusual or incorrect predictions and think about why they happen.
- Train a tiny machine learning model and compare its behavior to your rule based system.
- Complete the model card with your findings about data, behavior, limitations, and improvements.
- The goal is to help you reason about how models behave, how data shapes them, and why even small design choices matter.

---

## Tips

- Start with preprocessing before updating scoring rules.
- When debugging, print tokens, scores, or intermediate choices.
- Ask an AI assistant to help create edge case posts or unusual wording.
- Try examples that mislead or confuse your model. Failure cases teach you the most.

---

## TF Summary

The core concept students need to understand is that rule-based classifiers depend entirely on the vocabulary and logic you define — they have no understanding of meaning, context, or tone, which is why sarcasm and slang break them so easily. Students are most likely to struggle with implementing negation handling correctly (knowing when to reset the negate flag after a non-sentiment word) and understanding why "mixed" detection requires checking for both positive and negative word presence rather than just looking at the final score. AI assistants are helpful for brainstorming diverse test posts and explaining error traces, but they tend to over-engineer solutions by suggesting complex NLP pipelines when the activity calls for small, deliberate changes — students should be guided to keep fixes targeted. When a student's model misclassifies a sentence, a good guiding question is: "What happens when you print the tokens for that sentence? Which specific token is causing the wrong score?" This leads them to debug their own logic rather than asking for a ready-made fix. Comparing the rule-based model (76% accuracy) to the ML model (94%) reveals that memorization on a tiny dataset is not the same as understanding — the ML model gets sarcasm right only because it saw that exact example during training.

# TakeMeter: Planning Document
## Community: r/Minecraft

---

## 1. Community

**Chosen community:** r/Minecraft (reddit.com/r/Minecraft)

r/Minecraft is one of the largest subreddits (~10 million members), with a constant stream of posts spanning builds, technical questions, bugs, memes, and game discussion. Discourse quality varies enormously: a post might be a carefully constructed mega-build showcase, a genuine question about redstone mechanics, a hot debate about a new update, or just a meme about creepers. This variety makes it well-suited for a classification task — the community has implicit, shared norms about what kind of post belongs in each mode, and regular members immediately recognize the difference between someone asking for help, someone showing off, someone arguing about game design, and someone posting for laughs.

**Why this community fits the task:** The four discourse modes I'm measuring are genuinely distinct, common enough to yield 200+ examples without hunting, and meaningful to people in the community (the subreddit itself has historically had flair tags that map roughly onto these categories). The labels are grounded in function — what the post is *doing* — rather than quality judgments, which makes annotation more reliable.

---

## 2. Label Taxonomy

### `request`
**Definition:** The post is primarily asking for information, help, advice, or a solution — the author needs something from the community and is directing the post as a question or appeal.

**Clear examples:**
- *"My villagers keep despawning overnight even though I have name tags on them — what am I doing wrong?"*
- *"What's the most efficient early-game iron farm that works in 1.21? I don't want anything too complex."*

**Uncertain case:** *"Has anyone else noticed the new copper oxidation is way faster than before?"* — This looks like a question but it might be leading into discussion rather than genuinely seeking an answer. → **Decision rule:** If the post is structured as a question but the author already has an implicit answer they're asserting, classify by what the *body* of the post does. A rhetorical question with a complaint or observation in the body → `analysis/discussion`. A genuine request for community input with no embedded answer → `request`.

---

### `showcase`
**Definition:** The post is primarily presenting something the author made, found, or discovered in-game — the goal is to share an accomplishment, creation, or surprising result with the community.

**Clear examples:**
- *"Spent 3 months on this medieval city — finally finished the keep. [image attached]"*
- *"I found a naturally generated structure that looks exactly like a portal frame. Seed: 8274910."*

**Uncertain case:** *"Here's my starter base — I know it's nothing special but I'm proud of it."* — This is a showcase, but with self-deprecating framing that might look like it's inviting feedback (closer to `request`). → **Decision rule:** If the primary content is a creation or discovery being displayed — regardless of tone — it's `showcase`. Only reassign to `request` if the author explicitly asks for critique, help, or suggestions in the post body.

---

### `analysis/discussion`
**Definition:** The post makes an argument, shares a reasoned opinion, or invites substantive discussion about the game — the author is reasoning about something (a mechanic, update, design choice, strategy) rather than asking for help or showing something.

**Clear examples:**
- *"Mojang's decision to gate the mace behind the trial chambers is bad game design — it punishes casual players who don't want to engage with combat dungeons just to access a weapon tier."*
- *"I think copper is the most underutilized block in the game. Here's why the oxidation mechanic has no gameplay incentive attached to it."*

**Uncertain case:** *"The new 1.21 update feels kind of empty — is it just me?"* — The post starts with an opinion but ends as a question. → **Decision rule:** If the dominant function of the post is to assert or argue a position, classify as `analysis/discussion` even if it ends with "thoughts?" or "am I alone in this?". Only use `request` if the author is genuinely uncertain and the question is the point, not the punchline.

---

### `reaction/humor`
**Definition:** The post is primarily a meme, joke, celebration, or emotional reaction — the intent is entertainment or shared feeling, not information exchange or argument.

**Clear examples:**
- *"Me at 11pm: just one more block. Me at 3am: [image of enormous unplanned cave system]"*
- *"Just died to a creeper and lost my full netherite set. Uninstalling. [flair: Not serious]"*

**Uncertain case:** *"This creeper blew up my base and I'm actually going to cry [image]"* — The emotional reaction is real, but the image of the damage could be read as a showcase of the destruction. → **Decision rule:** If the emotional/comedic framing is the *reason the post exists* — the image is evidence for the joke or reaction, not the point in itself — classify as `reaction/humor`. If the image could stand alone as interesting content and the text is secondary, classify as `showcase`.

---

## 3. Hard Edge Cases

### Edge case 1: Showcase with embedded question
> *"Finally built my first iron farm! Does this layout look efficient or should I change anything?"*

Could be `showcase` (displaying a build) or `request` (asking for optimization advice).

**Decision rule:** The post leads with the display and the question is secondary (a request for validation or minor feedback). → `showcase`. If the question were the title and the image were evidence of a specific problem, → `request`.

### Edge case 2: Analysis disguised as humor
> *"Mojang adding 10 new mob variants: 😍 Mojang fixing hostile mob AI: 💀 [meme format]"*

The meme format signals `reaction/humor`, but the content is a pointed critique of development priorities.

**Decision rule:** If the post is expressing a genuine argument in meme format, and the argument is specific enough that you could restate it in prose, → `analysis/discussion`. If the post is purely about the shared feeling (the laugh, the relatable moment) with no underlying claim to extract, → `reaction/humor`.

### Edge case 3: Reaction to a real event vs. general humor
> *"They actually added the firefly back after the backlash. We won. 🎉"*

This is an emotional reaction, but it's a reaction to a real update event with implicit community context — some might argue it's discussion-adjacent.

**Decision rule:** Reactions to real-world game events with no argument or information content → `reaction/humor`. If the post unpacks *why* the event happened or what it means for the game, → `analysis/discussion`.

---

## 4. Data Collection Plan

**Source:** r/Minecraft public posts — top posts from the past month (sorted by "Top > This Month") plus "New" posts for diversity. Collected manually via Reddit's web interface.

**Target distribution:**

| Label | Target count | Notes |
|---|---|---|
| `request` | 55 | Abundant in "New" feed |
| `showcase` | 65 | Most upvoted posts tend to be showcases |
| `analysis/discussion` | 50 | Less common; may need to look at weekly discussion threads |
| `reaction/humor` | 40 | Common but easy to over-collect relative to others |
| **Total** | **≥ 210** | Buffer for discards |

**If a label is underrepresented:** For `analysis/discussion` specifically, I'll search the subreddit using terms like "change my mind," "unpopular opinion," "I think Mojang should," and "let's talk about" to surface more examples. I'll also look at the weekly "Unpopular Opinion" and "Discussion" megathreads pinned by the subreddit mods.

**Imbalance threshold:** If any single label exceeds 70% after initial collection, I'll stop collecting that label and actively source more examples from the underrepresented ones before annotating further.

---

## 5. Evaluation Metrics

**Metrics I'll use:**

- **Overall accuracy** — required baseline, but not sufficient on its own since the label distribution is uneven.
- **Per-class F1** — the primary metric. F1 balances precision and recall and is robust to class imbalance. I'll report F1 for each of the four labels.
- **Confusion matrix** — to identify which label pairs the model consistently confuses. I expect `request` / `analysis/discussion` and `showcase` / `reaction/humor` to be the hardest boundaries.
- **Macro-averaged F1** — unweighted average across classes, so a model that learns only the majority class doesn't look deceptively good.

**Why accuracy alone isn't enough:** If `showcase` ends up as 35% of the dataset, a model that predicts `showcase` for everything achieves 35% accuracy — which sounds low but hides the fact that it's learned nothing about the other three classes. Per-class F1 and the confusion matrix surface this failure.

**I will not use:** Weighted F1 as the primary metric, since it inflates scores for majority classes.

---

## 6. Definition of Success

**Minimum threshold for "useful":**
- Overall accuracy ≥ 70% on the test set
- No single class with F1 < 0.50 (a model that completely fails to learn one label is not deployable)
- Fine-tuned model meaningfully beats the zero-shot Groq baseline (≥ 5 percentage points in overall accuracy)

**"Good enough for deployment" threshold:**
- Overall accuracy ≥ 78%
- All per-class F1 ≥ 0.65
- The `request` / `analysis/discussion` confusion (the hardest boundary) produces fewer than 20% errors in each direction

**What would signal the labels need rework:** If the fine-tuned model performs below 60% overall after training on 200 examples, or if `reaction/humor` and `showcase` have near-zero F1, I'd revisit the label boundary definitions before concluding the model is the problem.

---

## 7. AI Tool Plan

### Label stress-testing
Before annotating 200 examples, I'll give Claude the four label definitions above and ask it to generate 10–15 posts that sit at the boundary between two specific label pairs: (1) `showcase` vs. `reaction/humor`, and (2) `request` vs. `analysis/discussion`. If Claude produces posts I can't cleanly classify using my current decision rules, that's a signal to tighten the definitions before I've committed to 200 annotations.

I'll specifically prompt: *"Generate 5 Minecraft subreddit posts that could plausibly be labeled either `showcase` or `reaction/humor` by a reasonable annotator. Then generate 5 more that sit between `request` and `analysis/discussion`."*

### Annotation assistance
I'll use Claude to pre-label a batch of ~80 examples (roughly 40% of the dataset) before reviewing each myself. I'll provide the full label definitions and decision rules from this document and ask for one label per post with a one-sentence rationale. I will read every pre-label before accepting it — disagreements get flagged and added to my edge case log. Any example where Claude's label and my label differ goes into the "difficult cases" documentation. Pre-labeled examples will be disclosed in the AI usage section of the README.

### Failure analysis
After fine-tuning, I'll paste all misclassified test examples into Claude and ask: "Here are posts my classifier got wrong. Identify any systematic patterns such as shared length, topic, structural features, or label pairs that appear in the errors more than once." I'll then manually verify each claimed pattern against the raw examples. Patterns I can't verify by re-reading are discarded from the analysis write-up.


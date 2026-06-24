# Project Plan: Community Post Classifier

## 1. Community Selection
 **Chosen Community:** r/soccer (Reddit) / Online Soccer Fan Communities.
**Target Audience/Niche:** Passionate global football fans, primarily tracking international tournaments such as world cup, club rivalries from seasonal league like La Liga and Premier League, and live match day threads.
**Why it fits classification:** Soccer discourse is highly volatile, shifting rapidly between objective tactical breakdowns and pure knee-jerk emotional outbursts. During a major tournament like the World Cup, the exact same match event triggers incredibly varied text patterns: some users post detailed data/formation breakdowns, while others post reactionary rants or completely unsubstantiated "hot takes" about players and managers. This massive variance in data makes it an ideal environment for a text classification task.


## 2. Classification Labels


### Label A: Analysis
**Definition:** The post makes a structured argument backed by specific tactical observations, team formations, or verifiable match data/statistics.
**Example 1:** "Arsenal completely choked out the midfield space in the second half by shifting to a mid-block 4-4-2, preventing any central progressive passes from Modric."
**Example 2:** "Our expected goals (xG) dropped from 2.1 to 0.4 over the last three matches because we aren't generating overlapping runs on the left wing anymore."

### Label B: Hot take
**Definition:** A bold, highly confident, or controversial football opinion stated as a fact without providing concrete supporting evidence or analytical context.
**Example 1:** "This manager is completely out of his depth and will be sacked by Tuesday; the whole squad has completely given up on him."
**Example 2:** "He is the most overrated striker in modern football history and wouldn't even make the bench in a second-division side."

### Label C: Reaction
**Definition:** An immediate, raw emotional response expressing joy, anger, or despair about a live event, featuring little to no intellectual argument.
**Example 1:** "OH MY GOD WHAT A GOAL!!! I CANNOT BELIEVE HE JUST SCORED THAT IN THE 94TH MINUTE!!!"
**Example 2:** "Absolute absolute disgrace of a refereeing performance. I am completely sick to my stomach watching this match."




## 3. Hard Edge Cases

**Ambiguous Scenario (Analysis vs. Hot Take):** A post that makes an incredibly aggressive, biased "Hot Take" claim but inserts a single, cherry-picked match statistic to try and look legitimate (e.g., "Haaland is a complete fraud—he only had 12 touches of the ball the entire game today").
**Annotation Strategy / Resolution Rule:** Look closely at the core intent of the text. If the statistic or evidence is used decoratively to mask an emotional insult, classify it as a "Hot Take". If the post uses the data point to build a broader, logical argument explaining "why" the system failed, classify it as "Analysis".
**Example 1** It was flimsy, but the fight against "Messi is the GOAT" we put up in the mid-late 2010s was really fun. Barcelona no longer making deep UCL runs, Sanchez, Vidal and company beating his ass into faux retirement only to come back and be shut down in the next World Cup. Amplifying "UEFAlona" shouts to discredit the past. Good times.
**Example 2**Trionda is Jabulani 2.0 (too smooth), its why a lot of weird goals have been going in this tournament e.g., Messi v Zidane, Kim v Mexico, etc.
**Example 3**Belgium haven’t looked particularly exciting so far.

Spain’s first game was all the worst parts of Spain in the tiki-taka era but they looked infinitely better against SA.

## 4. Data Collection Plan
**Data Sources:** Reddit (specifically the r/soccer subreddit, focusing on live match threads and post-match discussion boards during the ongoing World Cup tournament cycle).
**Target Count per Label:** A target of 70 annotated text examples per label across the 3 categories (Tactical Analysis, Hot Take, Pure Emotional Reaction), aiming for a balanced dataset of 210 total posts.
**Underrepresentation Mitigation Plan:** Live match threads naturally skew heavily toward "Pure Emotional Reaction" posts. If "Analysis" or "Hot Take" is heavily underrepresented after reviewing 200 initial items, I will pivot my collection strategy to source text from dedicated r/soccer "Tactical Discussion" flaired posts, "Post-Match Analysis" megathreads, or top-level comments with high upvote counts which tend to contain lengthier, more deliberate arguments.
## 5. Evaluation Metrics
<!-- Which metrics will you use to evaluate your model and why are those the right ones for this specific task? (Accuracy alone is not enough — explain what else you need and why.) -->
**Primary Metric:** Macro-Averaged F1-Score.The Macro-Averaged F1-Score serves as our primary evaluation metric because it provides an unweighted, balanced assessment of model performance across all target classes, regardless of their underlying frequency in the dataset. 
**Secondary Metric(s) (Precision, Recall, F1-Score):** Per-class Precision, Per-class Recall, and a Confusion Matrix.
**Justification for choice:** Accuracy alone is misleading because sports threads can experience sudden, massive shifts in post types (e.g., an unexpected red card causes a massive spike in "Pure Emotional Reaction" posts, which artificially inflates accuracy if the model simply guesses that label). Macro-averaged F1-Score treats all three categories equally, ensuring the model performs well on rare "Tactical Analysis" posts as well as common rants. 

## 6. Definition of Success
<!-- What performance would make this classifier genuinely useful? What would you accept as "good enough" for deployment in a real community tool? -->
**Deployment Threshold:** A Macro F1-Score of 0.75 or higher on the validation dataset, with a specific minimum Precision threshold of 0.80 for the "Tactical Analysis" label.
**Real-World Utility Metric:** For a real-world community dashboard filtering application, this classifier is successful if a user can toggle on a "Tactics Only" feed and experience a feed where fewer than 1 in 5 posts are actually unfiltered emotional rants or baseless hot takes. This ensures the automated moderation or filtering tool provides genuine value to a user looking for substantive discourse during a hectic tournament cycle like the World Cup.

## 7. AI Tool Plan
**Label Stress-testing:**
**Example 1:**"Haaland is a complete fraud who disappears when it matters most—he only had 11 touches of the ball and 0 shots inside the box during the entire semifinal today.
**"Why it's borderline:** It uses exact, verifiable match statistics (11 touches, 0 shots), but the overarching intent is an aggressive, biased put-down ("complete fraud").
**Example 2"**: Our entire defensive system is completely finished. Anytime we encounter a team playing a high-pressing 4-3-3, our center-backs panic, drop too deep, and completely fall apart.
**"Why it's borderline:** It outlines a specific tactical scenario (defending against a high-pressing 4-3-3 with deep-dropping center-backs), but it provides zero evidence that this actually happened today, relying instead on a dramatic, sweeping conclusion ("completely finished").

**Example 3**:"Look at the data: ever since the manager benched our creative playmaker in the 60th minute, our passing accuracy in the final third dropped by 40% and we didn't create a single big chance. He single-handedly threw the match away.
**"Why it's borderline:** It quotes a specific, measurable tactical shift (40% drop in passing accuracy) tied to an in-game timestamp. However, it assigns absolute, emotional blame to a single tactical decision while ignoring other match variables.
**Example 4"The stats don't lie:** Bruno is a toxic presence on the pitch who destroys our team chemistry. Every time he starts tracking back less, our defensive transition speed drops drastically and we concede on the counter.
**"Why it's borderline:** It references a real tactical concept (defensive transition speed vs. counter-attacks) and links it to a player's positioning. However, it incorporates highly subjective, unquantifiable emotional terms like "toxic presence" and "team chemistry."

**Example 5** "This 18-year-old midfielder has a higher progressive pass completion rate this tournament than Modric did at the same age. If the manager keeps restricting his positioning to a defensive anchor role, he is actively ruining the greatest talent this country has ever produced.
**"Why it's borderline:** It highlights a legitimate, technical performance metric (progressive pass completion rate). However, it uses that single data point to make an incredibly bold, unsubstantiated future prediction and hurls an accusation at the manager.




**Annotation Assistance:**

**Pre-Labeling Tool:** Google Gemini (with secondary assistance from Claude Code for workflow integration).
**Workflow Split:** 
I will utilize Gemini to pre-label an initial batch of  raw Reddit posts based on the definitions set in Section 2.
I will manually source, read, and completely hand-annotate the remaining examples from scratch to ensure a pure baseline.
**Review & Verification Process:** 
  For the batch pre-labeled by Gemini, I will perform a 100% human-in-the-loop review. I will read through every single machine-assigned label, cross-reference it with the hard edge case rules in Section 3, and manually correct any misclassifications (especially instances where Gemini confuses a stat-heavy *Hot Take* with genuine *Analysis*)

**Failure Analysis:**

**AI Error Analysis:** Once the model is evaluated, a full list of all wrong predictions will be given directly to the AI tool. The AI will be asked to read through these mistakes and identify any common patterns or trends causing the model to mess up.
**Human Verification:** To ensure the AI's conclusions are actually correct, I will personally look at the misclassified posts myself. I will manually review and verify the answers to double-check that the patterns the AI spotted are real and accurate before writing my final project summary.
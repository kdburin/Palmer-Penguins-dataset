# Penguin Species Separation and Prediction

## Project Overview

This project studies the **Palmer Penguins** dataset to answer a biological question before moving into prediction:

**Which variables help separate Adelie, Chinstrap, and Gentoo penguins, and how do those variables carry species information?**

The goal of this work was not only to build a model that predicts penguin species well, but also to understand **why** the prediction works. Instead of treating the dataset like a black box, this project looks at the structure of the variables, how species are distributed across natural groups in the data, and how much uncertainty about species is removed when a variable or combination of variables is known.

---

## Why this project is more than just prediction

A machine learning model can give high accuracy without explaining anything about the data. This project was built to go further than that.

Rather than only asking:

**“Can I predict penguin species?”**

this analysis also asks:

- Which variables separate species best?
- Which species does each variable help identify?
- Which variables are strong on their own, and which help more when combined?
- Which variables carry real structure, rather than random noise?
- Why does the final predictive model perform well?

So the project has **two parts**:

1. **Understanding the data**
2. **Using that understanding for prediction**

---

## Main variables studied

The main biological variables in the dataset were:

- `bill_length_mm`
- `bill_depth_mm`
- `flipper_length_mm`
- `body_mass_g`
- `island`

I also studied several interaction terms such as:

- `flipper_length × body_mass`
- `bill_length × body_mass`
- `bill_length × flipper_length`

---

## Analysis process

### 1. Studying one variable at a time

For each variable, I first studied its distribution using an **ECDF** and a **dendrogram** built from **hierarchical clustering**.

The reason for doing this was to find the **natural clusters** in that variable. Instead of forcing every variable into the same number of bins, I let each variable form clusters according to its own shape in the data.

This made the analysis closer to the actual structure of the variable.

---

### 2. Building species-by-bin contingency tables

After choosing the clusters for a variable, I used them as bins and built a **species × bin contingency table**.

This table showed:

- how many Adelie penguins fell into each bin
- how many Chinstrap penguins fell into each bin
- how many Gentoo penguins fell into each bin

This was one of the most important parts of the analysis, because it showed the actual separation pattern.

For example:

- some variables placed **Gentoo** mostly into one side of the bins
- some variables helped separate **Adelie** at the lower end
- some variables left **Chinstrap** overlapping with the others

So the contingency table was the main tool for seeing **how species were distributed across each variable**.

---

### 3. Null vs. alternative multinomial models

After building the contingency tables, I created two probability models:

- a **null model**
- an **alternative model**

The null model assumed that **species do not affect how penguins fall into bins**.  
The alternative model assumed that **different species fall into bins in different ways**.

So in simple terms:

- **Null:** species and bins are unrelated
- **Alternative:** species and bins are related

I converted the contingency tables into **multinomial probability vectors** and used them as the basis for simulation.

---

### 4. Simulating entropy distributions

Using the multinomial vectors, I simulated many observations under both the null and alternative models.

For each simulation, I computed **entropy**, which measures uncertainty.

This gave me:

- a **null entropy distribution**
- an **observed / alternative entropy distribution**

These plots helped answer an important question:

**Is the species pattern in the bins strong, or could it happen by chance?**

If the observed entropy distribution was much lower than the null one, it meant the variable created real structure and reduced uncertainty about species.

So the entropy analysis validated what I saw in the contingency tables.

In short:

- the **contingency tables** showed the pattern
- the **entropy simulations** showed that the pattern was not random

---

### 5. Separation score

To summarize how useful a variable was, I used a **separation score**:

\[
S(A) = 1 - \frac{H(Y \mid A)}{H(Y)}
\]

where:

- \(Y\) is the species label
- \(A\) is the variable through its bins
- \(H(Y)\) is the original uncertainty about species
- \(H(Y \mid A)\) is the remaining uncertainty after knowing the variable

This score tells me:

**How much species uncertainty is removed by knowing the variable?**

A larger separation score means the variable removes more uncertainty and therefore separates species better.

This gave me a way to compare variables and combinations of variables.

---

### 6. Pair plots and combinations of variables

After studying variables one by one, I looked at **pairs of variables** with pair plots.

This was different from the 1D entropy analysis because it showed **2D separation** instead of one variable at a time.

The pair plots showed that some pairs helped especially with separating **Gentoo**, while other pairs helped more with separating **all three species together**.

This showed that species identity is not explained by one measurement alone, but often by a pattern across multiple measurements.

---

### 7. Multi-variable clustering

I then combined multiple variables together and applied clustering again.

This let me compare:

- all four numerical variables together
- all four numerical variables plus island
- all four numerical variables plus selected interactions

These results showed that the species structure became much cleaner when variables were combined, which means the data contain **joint biological structure** that is not visible from only one variable at a time.

---

## Main findings

The analysis showed that the variables do not all help in the same way.

### Single-variable findings

- **Flipper length** was one of the strongest single-variable separators overall.
- **Bill depth** and **body mass** were especially useful for identifying **Gentoo**.
- **Bill length** was useful mainly because it helped separate **Adelie** at the lower end of the scale.
- **Island** was very informative for **Chinstrap** and **Gentoo**, because each of those species was strongly tied to one island, while Adelie was spread across all three.

### Multi-variable findings

- Species separation became much stronger when variables were combined.
- The four numerical variables together gave a very strong baseline structure.
- Some interaction terms helped slightly, especially when combined with the baseline variables.
- The best overall predictive variable set included both strong global separators and variables that still carried useful partial species information.

### Biological interpretation

The main biological message is that penguin species are not separated by one variable alone.  
Instead, species identity is explained by a **pattern across body measurements**.

Also, different variables help with different species:

- **Gentoo** is often the easiest to separate
- **Chinstrap** is often the hardest because it overlaps more with the others
- **Adelie** can be separated well in certain variable ranges, especially lower bill-length regions

So the data are not random. They contain real biological structure.

---

## Prediction

After understanding the variable structure, I built a final predictive model.

The final model included:

- bill length
- bill depth
- flipper length
- body mass
- selected interaction terms
- island

I kept island in the predictive model even though it was not the strongest global separator, because prediction and separation are not exactly the same task.

A variable does not need to be the best overall separator to still help prediction.  
It only needs to provide useful signal in some part of the classification process.

Using cross-validation, the final predictive model achieved about **98% accuracy**.

---

## Final takeaway

This project was not only about getting a good prediction score.

It was about understanding:

- which variables carry species information
- how they carry that information
- which species each variable helps separate
- why combining variables improves performance
- why the final predictive model works

So this project connects **data analysis**, **biological interpretation**, and **machine learning**.

Instead of only saying:

**“The model predicts well.”**

this project also explains:

**“Here is the structure in the data that makes the prediction possible.”**

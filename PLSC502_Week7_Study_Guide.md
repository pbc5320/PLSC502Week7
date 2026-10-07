# PLSC 502 Week 7 Study Guide

Oct 7, 2026 · Pamela Carey

## 1. The big picture

Week 7 answers one question: how do we know how much to trust a number computed from one sample? The answer is the sampling distribution, and every other topic this week either feeds into it or helps you describe it. The source materials are the Week 7 slide deck (Jared Edgerton), the Python and R code tutorials, and the ten-problem practice quiz. (The deck's first goals slide is labeled "Week 6 Goals"; it is the Week 7 deck.)

The week has two halves that connect:

1. **Inference machinery.** Population, sample, statistic, sampling distribution, the Central Limit Theorem (CLT), and the properties we use to judge an estimator: bias, variance, consistency, efficiency, and MSE.
2. **A catalog of data-generating processes (DGPs).** Each political outcome has a natural probability family. Binary events are Bernoulli or Binomial, counts are Poisson, durations are Exponential or Weibull, equal-chance draws are Uniform, and the catch-all is the Normal.

Why they connect: to simulate or reason about a sampling distribution you must first say what DGP produced the data. The code tutorial does exactly this, drawing from each family and then studying how statistics behave across repeated samples.

| Outcome type | Political example | Natural family | Parameter(s) |
| --- | --- | --- | --- |
| Binary event | War onset, vote choice, bill passes | Bernoulli | p |
| Number of successes in n trials | Votes for a candidate | Binomial | n, p |
| Event count | Protests, coups, executive orders | Poisson | λ |
| Time until event (constant risk) | Cabinet survival, ceasefire length | Exponential | λ |
| Time until event (changing risk) | Leader tenure | Weibull | k, λ |
| Equal chance | Lottery, random audit | Uniform | a, b |
| Continuous, symmetric, catch-all | Ideology scores, measurement error | Normal | μ, σ² |

**Study order that works:** sampling distribution → CLT → estimator properties → the DGP catalog → linear-model extensions → run the code → do the practice problems.

## 1A. Lecture notes: what the professor said (study this first)

These notes follow the lecture in order, using his explanations and examples. Where the lecture adds something the slides lack, it is marked **(lecture only)**. Timestamps are approximate and come from the transcript.

### Learning goals he stated (0:11)

By the end of the week you should be able to: distinguish population, sample and statistic; explain what a sampling distribution is and why it governs uncertainty; state and use the CLT; evaluate estimators by bias, variance, consistency, efficiency and mean squared error; and recognize the common data-generating processes used in social science.

### Population, sample, statistic (0:49)

- **Population:** the full set of units (all eligible voters in a state).
- **Sample:** a subset that we actually observe.
- **Statistic:** any numeric summary computed from the sample (e.g. mean preference for an opponent).
- **Inferential goal:** say something about a population parameter (μ) using a statistic (x̄). In many cases the target is the population mean.

### Sampling distribution (1:20)

- It is the distribution you would see if you repeatedly drew new samples and recomputed the statistic each time.
- It quantifies how much the statistic would vary across hypothetical repeated samples.
- "This is the engine behind standard errors, confidence intervals, and tests."
- **Key intuition:** even if the population is fixed, your sample varies, so your statistic does too.
- **Histogram example (1:45):** each bar is the frequency of sample means from repeatedly drawing samples (e.g. of 50) from the same population. It is centered on the true mean (zero in his example), so **the average of the sample means is unbiased**. The spread shows sampling variability: the tighter the spread, the more precise the estimator. As n increases the spread narrows because the standard error of the mean shrinks.
- The red line over the histogram is the normal approximation. Even if the population is not normal, the CLT says the sampling distribution of the mean tends to look normal as n grows.
- **Takeaway:** the sample mean is not a fixed number; it has a distribution, and that distribution lets us calculate probabilities, standard errors and confidence intervals. "It's the foundation of inferential statistics."

### The Central Limit Theorem (3:30)

- **Statement:** given a sufficiently large sample size, the sampling distribution of the sample mean approaches a normal distribution, regardless of the original population distribution.
- It does not matter whether the population is skewed, uniform, or heavy-tailed; once n is large enough, the distribution of the mean looks normal.
- **Assumption 1, independent:** one observation does not influence another, so there is no systematic dependence.
- **Assumption 2, identically distributed:** all data points come from the same underlying population. Together these are the classic IID assumptions.
- **Why it is powerful:** individual data can be messy, with skew and outliers, but when you average across observations the randomness smooths out and the average behaves in a predictable, bell-shaped way. This is a bridge "from the mess of reality to the clean mathematics of inference."

**His simulation (4:49), true population mean = 3:**

| Sample size | What you see |
| --- | --- |
| n = 5 | Rough and spread out; means range roughly from 1 to 6; not well centered on 3; normal approximation fits poorly |
| n = 50 | Much tighter, centered near 3, starting to look bell-shaped; normal approximation already does a good job |
| n = 1,000 (transcript is garbled here) | Almost indistinguishable from the normal curve; extremely narrow; centered precisely on 3 |

### Properties of estimators (6:27)

He gives four properties, and the quiz may ask you to define and distinguish them.

- **Bias:** the difference between the expected value of the estimator and the true parameter. If the average of the estimator across repeated samples equals the true parameter, the estimator is **unbiased**. The sample mean is an unbiased estimator of the population mean. **"Unbiased does not mean perfect. It just means that on average, the estimator hits the target."**
- **Variance:** how much the estimator fluctuates from sample to sample. An unbiased estimator that varies wildly may not be useful. **Dart analogy:** a thrower who always aims at the bullseye but sprays darts all over the board is unbiased with high variance.
- **Consistency:** as the sample size increases, the estimator converges in probability to the true parameter. The sample mean is consistent because, as n goes to infinity, the **law of large numbers** makes x̄ converge to the population mean. Consistency is about long-run performance: with more data, does the estimator improve?
- **Efficiency:** among all unbiased estimators, the efficient one has the lowest variance. If two unbiased estimators target the same mean, the one with the tighter sampling distribution is more efficient. We prefer efficient estimators because they give more precise results.
- **Big picture:** good estimators balance bias and variance and improve as we collect more data, ideally in the most precise way possible.

**His four pictures (9:03), true mean = 5:**

| Picture | What it shows |
| --- | --- |
| Biased estimator (top left) | Whole distribution centered around 6 instead of 5. Even averaging over many samples, you systematically miss the target. |
| High-variance estimator (top right) | Average close to 5, but a huge spread. Unbiased but unreliable. |
| Consistent estimator (bottom left) | Tightly clustered around the true mean; with larger samples it would shrink further. |
| Efficient estimator (bottom right) | Centered correctly with the smallest variance among competing unbiased estimators: the tightest distribution. |

### Mean squared error (10:38)

- MSE combines bias and variance into one measure of total error. It has two components: the **systematic error** (squared bias) and the **random error** (variance).
- Think of it as "are we systematically off target, and how much do we wiggle around the target?"
- It lets you compare estimators even when one has lower bias but higher variance and another the reverse.
- **The trade-off:** sometimes accepting a little bias can dramatically reduce variance, lowering MSE overall.
- Figure: the blue dashed line is the true value, the red line is the mean of the estimates. The gap between them is the bias; the width of the histogram around the red line is the variance.

```latex
MSE(\hat{\theta}) = Bias(\hat{\theta})^2 + Var(\hat{\theta})
```

### Models and DGPs (11:49)

- **Statistical models** are the mathematical frameworks we use to capture the process generating observed data. In political science, sociology and economics, they let us formalize and test theories, not just describe data: "Models give us that bridge."
- **Parametric families of DGPs** are sets of probability distributions defined by parameters such as means, variances and rates. Examples: a Bernoulli with probability p for voter turnout, a Poisson with rate λ for protest counts.
- Specifying a family lets you define hypotheses clearly and use inference to test them. Instead of saying "policies sometimes diffuse across states," model adoption as a probability distribution, estimate its parameters, and test whether covariates shift those parameters.

### Bernoulli (13:02)

- The building block for other distributions. A Bernoulli trial is a random experiment with two outcomes: success or failure, yes or no, 1 or 0.
- One parameter, **p**, the probability of success. Examples: whether a voter turns out, whether a country adopts a policy.
- His chart uses p = 0.6: success has probability 0.6 and failure 0.4.
- It is the foundation of the binomial, geometric and other families.

### Binomial (14:00)

- Counts the **number of successes across n independent Bernoulli trials**, each with success probability p. Part of its PMF comes from the Bernoulli.
- Examples: votes a candidate receives out of n voters; policies adopted after several attempts.
- His chart: n = 10, p = 0.5. The most likely outcome is about 5 successes, and the distribution is symmetric because p = 0.5. If p = 0.6 the peak shifts right (toward 6); if p = 0.4 it shifts left (toward 4).

### Logit and probit (15:19)

- The common ways to link covariates to binary outcomes.
- **Logit:** the probability of success is the logistic function of Xβ, which maps predictors smoothly between 0 and 1.
- **Probit:** uses the standard normal cumulative distribution function, Φ(Xβ).
- Extensions: **ordered** logit/probit for ordinal outcomes (strongly disagree to strongly agree); **multinomial** logit/probit for more than two categories (choice among candidates).

### Poisson (16:08)

- Models the **number of events in a fixed interval of time or space**. One parameter, λ, the average number of events, which is also its variance (mean = variance = λ).
- The PMF gives the probability of exactly k events and depends only on λ.
- **Two assumptions:** (1) independence, so each event occurs independently of the others; (2) a constant rate, so λ stays constant over the interval or region. If either is violated, for example if events cluster, the Poisson may not fit well.
- Examples: protests in a country per month, bills passed per year, international conflicts per decade. **(lecture only)** He warns that none of these examples are actually fully independent of each other.
- His chart: λ = 3. Most likely outcomes are near 2, 3 or 4, but 0, 1 and larger counts remain possible. With small λ the distribution is **right-skewed**; as λ grows it looks more symmetric.

### Exponential (17:52)

- Models **time until an event occurs**, with one parameter λ, the event rate.
- **Memoryless:** the probability of the event happening in the future does not depend on how much time has already passed. If a policy has not been adopted yet, its chance of adoption next month is the same whether it has been pending one month or one year.
- Examples: time until regime change, time until a new policy is adopted.
- Plot: a steep decline at first (most events happen early), with a long tail for longer durations.

### Exponential hazard rate (18:53)

- **Hazard rate:** the instantaneous probability that the event occurs at time t, given that it has not occurred yet.
- For the exponential, h(t) = λ: **constant**. The risk of ending is the same no matter how long something has lasted.
- That is why the exponential is often the **baseline hazard** in survival models. Example: baseline risk of regime collapse assumed constant over time.

### Weibull (19:44)

- A flexible generalization of the exponential that lets the hazard change over time. Two parameters: **shape k** (controls hazard behavior) and **scale λ** (influences spread and central tendency).
- **k > 1:** hazard increases over time; the longer we wait, the higher the chance of the event. Examples: mechanical failure, leadership fatigue.
- **k < 1:** hazard decreases over time; the event is most likely early, and the risk falls the longer something survives. Example: a fragile regime that stabilizes after surviving its first year.
- **k = 1:** hazard is constant and the Weibull collapses into the exponential.
- His graph: blue line k = 2 (rising hazard), green line k = 0.5 (falling hazard).
- **Takeaway:** the Weibull extends the exponential by adding flexibility, so we can model increasing, decreasing or constant risk.

### Uniform (21:29)

- One of the simplest continuous distributions: every outcome within \[a, b\] is equally likely; density is constant between a and b and zero elsewhere.
- A baseline or **"no preference" model**, useful when assuming complete equality of likelihood across outcomes. **(lecture only)** He notes it is frequently used in Bayesian statistics.
- Mean = the midpoint (a + b)/2; variance depends on the width of the interval, (b − a)²/12; the CDF rises in a straight line from a to b.
- A natural reference or null distribution to compare against more complicated models. Examples: a lottery, random selection, simulation, neutral hypothesis testing.
- His plot: Uniform(0, 1), with PDF equal to 0 outside the interval.

### Normal (23:35)

- The "workhorse of statistics": symmetric, bell-shaped, defined by mean μ and variance σ².
- Used so often for two reasons: **mathematical convenience** and the fact that it **emerges naturally from the CLT**.
- Many real variables approximate normality: measurement error, ideology scores, and income approximately, **though income is often skewed**.

### Z-scores and the standard normal (24:30)

- A z-score standardizes so you can compare across different normal distributions: how many standard deviations an observation is from the mean. Positive means above average, negative below; the farther from zero, the more unusual.
- **Example:** score 85, mean 75, SD 5, so Z = (85 − 75)/5 = 2, two standard deviations above the mean, a significantly above-average performance.
- **Standard normal:** mean 0, variance 1, obtained by standardizing (normalization). It is the foundation for p-values, critical values and confidence intervals: "the universal reference curve."

### Z-scores to p-values and critical values **(lecture only)** (25:50)

The slides do not cover this, but he teaches it in the lecture, so expect it.

- A z-score places an observation on the standard normal curve. The **p-value is the area under the curve in the tail(s)** beyond that z.
- **One-tailed test:** p = 1 − Φ(|z|) (upper-tail area).
- **Two-tailed test:** p = 2 × (1 − Φ(|z|)), counting both tails.
- Decide one- or two-tailed **before looking at the data**. In most social science research the default is two-tailed, even when theory points in one direction.
- **Critical value:** the z threshold that carves off an alpha proportion of the distribution as the rejection region. For a two-tailed test at α = 0.05, alpha is split (0.025 per tail) and the critical value is about **1.96**. For a one-tailed test the whole 0.05 goes in one tail and the critical value is about **1.645**.
- If your computed z falls beyond the critical value, **reject the null** at that alpha level; if it lands in the non-rejection region, fail to reject. The only difference between one- and two-tailed tests is how alpha is allocated across tails.
- **His R example:** with z = 2, one-tailed p = 1 − pnorm(2) ≈ 0.023 (he says "0.02"); two-tailed p = 2 × that ≈ 0.045. Critical values: 1.645 (one-sided) and 1.96 (two-sided, using 0.05/2 = 0.025).
- His plot: a standard normal with two red rejection regions at ±1.96 totaling 5% of the area, 2.5% per tail.

```latex
p_{\text{one-tail}}=1-\Phi(|z|),\qquad p_{\text{two-tail}}=2\,[1-\Phi(|z|)]
```

### Normal errors and the bivariate normal (28:52)

- In many models, especially linear regression, we assume the errors are normal. That makes estimates approximately normal, which justifies confidence intervals and tests.
- When two variables are jointly normal, the relationship is summarized by **two means, two variances and a correlation**; the intimidating PDF simply encodes that correlation.
- Examples: education and income, ideology and donations (continuous pairs where a linear association is plausible).
- Contour plot: each ellipse is a region of equal joint density. **Ellipses tilted upward reflect positive correlation.**
- Wrap-up: the normal is symmetric and bell-shaped; z-scores standardize; the standard normal gives z-scores, p-values and critical values; many models assume normal errors; bivariate/multivariate normals capture correlation in a tractable way.

### Closing concepts, to be covered in depth later (31:11)

- **Interaction terms:** let the effect of one variable depend on the value of another, allowing conditional rather than purely additive relationships. In his plot of education × sex on income, the slope is positive for women (more education, more income) and negative for men, in a hypothetical scenario.
- **Exponential effects:** relationships that are multiplicative rather than linear. Exponential transformations capture growth or decay and are useful when effects accelerate or tip off quickly: population growth, compound interest, cumulative effects on policy adoption. Y rises slowly at first, then faster and faster.
- **Saturated models:** include all possible main effects and interactions and fit the data perfectly; residual variance is minimized, often zero. **(lecture only)** He adds that they are a **poor choice for generalization** because they memorize the sample rather than capturing underlying patterns. They are still useful as baselines: they show the maximum possible fit, and comparing simpler models to the saturated one shows how much explanatory power is gained or lost.

### Memorize: the lecture's one-liners

- Even if the population is fixed, the sample varies, so the statistic does too.
- The sampling distribution is the foundation of standard errors, confidence intervals and tests.
- CLT needs independent and identically distributed observations.
- Unbiased does not mean perfect; it means correct on average.
- Consistency is about long-run performance; bias and variance describe repeated samples at a fixed n.
- Efficient means the lowest variance among unbiased estimators.
- MSE = systematic error (bias²) + random error (variance).
- Poisson: mean = variance = λ; assumes independence and a constant rate.
- Exponential: constant hazard, memoryless. Weibull: k > 1 rising, k < 1 falling, k = 1 exponential.
- Uniform: the "no preference" baseline.
- Two-tailed critical value 1.96 and one-tailed 1.645 at α = 0.05.

## 2. Sampling and the sampling distribution

A statistic computed from one sample is one draw from a distribution of possible values; that distribution is the sampling distribution.

**Three definitions**

- **Population:** the entire set of possible observations or units of analysis (all eligible voters in a state).
- **Sample:** a subset of the population selected for analysis (a random group of those voters).
- **Statistic:** a number computed from the sample (the sample mean, the sample variance).

**Sampling distribution:** the probability distribution of a statistic across repeated samples drawn from the same population. It shows how the statistic varies from sample to sample, and so how accurately it estimates the population parameter.

**The key mental move.** You only ever see one dataset, which gives one sample mean. The sampling distribution is a thought experiment (or, in code, a simulation) of what you would get if you could redraw the sample many times. The code tutorial puts it this way: "One dataset gives ONE sample mean. The sampling distribution is what you would get across many repeated samples from the same DGP."

**Standard error.** The standard deviation of a sampling distribution is called the standard error. For the sample mean:

```latex
SE(\bar{X}) = \frac{\sigma}{\sqrt{n}}
```

Two consequences: the standard error shrinks as n grows, but only at the rate of the square root (quadrupling the sample size halves the SE), and a noisier population (bigger σ) means a noisier estimate.

**Worked check from the tutorial.** Cabinet durations are Exponential with mean 24 months. For an Exponential, the standard deviation equals the mean, so σ = 24. With samples of n = 50, theory gives SE = 24 / √50 ≈ 3.39 months. The simulation (5,000 samples of 50) returns an empirical standard deviation of the sample means very close to that number.

**Do not confuse three different distributions**

| Distribution | What it describes | Its spread is called |
| --- | --- | --- |
| Population distribution | Individual units in the population | σ (standard deviation) |
| Sample distribution | The n values you actually observed | s (sample standard deviation) |
| Sampling distribution | A statistic across repeated samples | Standard error |

## 3. The Central Limit Theorem

The CLT says that averages of independent draws become approximately normal as n grows, whatever the shape of the underlying data. The slide states it as: given a sufficiently large sample size, the sampling distribution of the sample mean approaches a normal distribution, regardless of the original population distribution.

In symbols, for independent, identically distributed draws with mean μ and finite variance σ²:

```latex
\bar{X}_n \;\approx\; \mathcal{N}\!\left(\mu,\ \frac{\sigma^2}{n}\right)
\qquad\text{equivalently}\qquad
\frac{\bar{X}_n-\mu}{\sigma/\sqrt{n}} \to \mathcal{N}(0,1)
```

**Two required conditions** (the practice quiz asks for these explicitly)

1. **Independent** observations.
2. **Identically distributed** observations (all drawn from the same population).

The full theorem also requires a finite variance, which the slides leave implicit.

**What the CLT does and does not say**

- It is about the distribution of the **sample mean**, not the raw data. The raw data stay skewed; only the averages become bell-shaped.
- As n grows, two things happen to the sampling distribution: it becomes more normal, and it becomes narrower (SE = σ/√n).
- "Sufficiently large" depends on the population. A nearly symmetric population needs a small n; a heavily skewed one such as conflict durations needs more.

**Why it matters.** The CLT lets us use normal-based inference, meaning hypothesis tests and confidence intervals, for a mean without knowing the population's distribution. The slide's example: comparing two groups with a difference-in-means test works because the sampling distribution of the difference approximates a normal for large enough samples.

**See it in the tutorial.** Part 3 draws Exponential data (heavily right-skewed, mean 24) and records 3,000 sample means at n = 2, 10, 50 and 200. The histograms start skewed at n = 2 and look normal by n = 50 or 200, while the center stays at 24 and the spread keeps shrinking. The lecture's demo (true mean 3) shows the same pattern at n = 5, 50 and a much larger n; see Section 1A.

## 4. Properties of estimators and the MSE decomposition

An estimator is good if its sampling distribution is centered on the truth (low bias), tightly spread (low variance), and improves with more data (consistency). Let θ be the true parameter and θ̂ the estimator.

| Property | Definition | Formula |
| --- | --- | --- |
| Bias | Gap between the estimator's expected value and the truth | Bias(θ̂) = E\[θ̂\] − θ |
| Variance | Spread of the estimator across repeated samples | Var(θ̂) = E\[(θ̂ − E\[θ̂\])²\] |
| Consistency | Converges in probability to the true parameter as n grows | θ̂ₙ → θ as n → ∞ |
| Efficiency | Lowest variance among all unbiased estimators | Smallest Var(θ̂) in the unbiased class |

**Unbiased** means bias = 0: on average, across samples, the estimator hits the truth. Unbiased does not mean any single estimate is correct.

**Bias is a small-sample concern; consistency is a large-sample one.** The tutorial's closing comment says exactly this. An estimator can be biased in small samples yet consistent, because its bias vanishes as n grows.

**Mean squared error** combines accuracy and precision in one number:

```latex
MSE(\hat{\theta}) = E[(\hat{\theta}-\theta)^2] = \text{Bias}(\hat{\theta})^2 + \text{Var}(\hat{\theta})
```

This shows the bias-variance trade-off: a slightly biased estimator with much lower variance can beat an unbiased one on MSE. It is the tool for choosing between estimators.

**Deriving it**. Add and subtract E\[θ̂\]:

```latex
E[(\hat{\theta}-\theta)^2] = E\big[(\hat{\theta}-E[\hat{\theta}])^2\big] + 2\,(E[\hat{\theta}]-\theta)\,E\big[\hat{\theta}-E[\hat{\theta}]\big] + (E[\hat{\theta}]-\theta)^2
```

The middle term is zero because E\[θ̂ − E\[θ̂\]\] = 0. What remains is Var(θ̂) + Bias(θ̂)².

**Worked example: two variance estimators (tutorial Part 4).** Data are Normal with σ = 2, so the true variance is 4, with samples of n = 10.

- Dividing by n (the maximum-likelihood estimator) is biased downward. Across 10,000 samples its average falls below 4 (theory: 4 × 9/10 = 3.6).
- Dividing by n − 1 (R's `var()`, NumPy's `ddof=1`) is unbiased, averaging about 4.
- Both are consistent: at n = 10, 100, 1,000 and 10,000 their values converge to 4 and to each other.

This is why software divides by n − 1: one degree of freedom is spent estimating the mean, which makes the divide-by-n version too small on average.

**Slide intuition.** The deck's visuals (true mean of 5) show each property as a target-shooting picture: bias is the center of the cloud missing the truth, variance is the width of the cloud, and consistency is the cloud tightening around the truth as n rises.

## 5. DGP families for binary and count outcomes

A statistical model is a mathematical description of the process that generated the data. A parametric family is a group of distributions described by a finite set of parameters, so choosing one lets you state a hypothesis precisely and do inference. The skill this week is matching the outcome to its family.

### Bernoulli: one yes/no outcome

Used for a single binary outcome (voted or not, policy adopted or not). One parameter, p = probability of success.

```latex
P(X=x) = p^x (1-p)^{1-x},\quad x\in\{0,1\}
\qquad E[X]=p,\quad Var(X)=p(1-p)
```

The variance p(1 − p) is largest at p = 0.5 (value 0.25). Intuition: the outcome is least predictable when success and failure are equally likely, and fully predictable at p = 0 or 1.

### Binomial: count of successes in n trials

Counts successes in a fixed number n of independent Bernoulli trials, each with probability p.

```latex
P(X=k) = \binom{n}{k} p^k (1-p)^{n-k},\quad k=0,1,\dots,n
\qquad E[X]=np,\quad Var(X)=np(1-p)
```

n is the number of trials; p is the success probability on each. A Bernoulli is the special case n = 1. Examples: votes for a candidate out of n voters, successful implementations out of n attempts.

### Adding covariates: logit and probit

When the binary outcome depends on predictors, model the probability of success as a function of Xβ.

```latex
\text{Logit: } P(Y=1\mid X)=\frac{1}{1+e^{-X\beta}}
\qquad
\text{Probit: } P(Y=1\mid X)=\Phi(X\beta)
```

Here Φ is the standard normal CDF. Both squeeze Xβ into the 0–1 range. Extensions from the deck: **ordered** logit/probit for ordinal outcomes (survey agree/disagree scales) and **multinomial** logit/probit for unordered categories (choice among candidates).

### Poisson: counts of independent events

Counts events in a fixed interval of time or space. One parameter, λ, the average rate.

```latex
P(X=k)=\frac{e^{-\lambda}\lambda^k}{k!},\quad k=0,1,2,\dots
\qquad E[X]=Var(X)=\lambda
```

Assumptions: events occur independently, and the rate is constant over the interval. Examples: protests per month, bills passed per year, conflicts per decade, coups per decade. The mean equals the variance; real count data that are more spread out than that (overdispersion) are a warning sign for the Poisson assumption.

| Family | Support | Parameters | Mean | Variance |
| --- | --- | --- | --- | --- |
| Bernoulli | 0, 1 | p | p | p(1 − p) |
| Binomial | 0, 1, …, n | n, p | np | np(1 − p) |
| Poisson | 0, 1, 2, … | λ | λ | λ |

## 6. DGP families for durations

Duration outcomes (time until something happens) are modeled by how the instantaneous risk of the event, the hazard, behaves over time. A constant hazard gives the Exponential; a hazard that rises or falls gives the Weibull.

### Exponential: constant risk

Models the time until an event. One parameter, the hazard rate λ.

```latex
f(t\mid\lambda)=\lambda e^{-\lambda t},\ t\ge 0
\qquad E[T]=\frac{1}{\lambda},\quad Var(T)=\frac{1}{\lambda^2}
\qquad P(T>t)=e^{-\lambda t}
```

The survival formula P(T > t) = e^(−λt) is not on the slides but is what the practice quiz needs (Problem 6).

**Memoryless property.** The probability the event occurs in the next interval does not depend on how long you have already waited. A cabinet that has survived 3 years has the same chance of surviving 2 more years as a brand-new cabinet has of surviving 2.

**Hazard rate.** The instantaneous probability of the event at time t, given it has not yet happened. For the Exponential it is constant:

```latex
h(t)=\lambda\quad\text{for all } t\ge 0
```

Constant hazard means no duration dependence, and the Exponential is the standard baseline hazard in survival analysis. In the tutorial, `rexp(n, rate = 1/24)` in R and `rng.exponential(24)` in Python (scale = mean) both give cabinets lasting 24 months on average. Watch the parameterization: R takes the rate, NumPy takes the scale (1/rate).

### Weibull: risk that changes over time

Generalizes the Exponential by letting the hazard vary. Shape parameter k controls the hazard's direction; scale parameter λ sets the spread and central tendency.

```latex
f(t\mid k,\lambda)=\frac{k}{\lambda}\left(\frac{t}{\lambda}\right)^{k-1} e^{-(t/\lambda)^k}
\qquad
h(t\mid k,\lambda)=\frac{k}{\lambda}\left(\frac{t}{\lambda}\right)^{k-1}
```

| Shape k | Hazard over time | Political reading |
| --- | --- | --- |
| k < 1 | Decreasing | Risk is highest early, e.g. a new government is most fragile at the start |
| k = 1 | Constant (reduces to Exponential) | No duration dependence |
| k > 1 | Increasing | Risk builds with time in office |

The slide's example is leader tenure, where hazard depends on political context and time already served. The two parameterizations of λ differ: in the Exponential λ is a rate, in the Weibull it is a scale. Note this when reading formulas.

## 7. Uniform and Normal distributions

### Uniform: every outcome equally likely

Every value in the interval \[a, b\] has the same density, and values outside have none. It is the "no-preference" baseline and the null distribution to compare richer models against.

```latex
f(x\mid a,b)=\frac{1}{b-a},\ a\le x\le b
\qquad F(x)=\frac{x-a}{b-a}
\qquad E[X]=\frac{a+b}{2},\quad Var(X)=\frac{(b-a)^2}{12}
```

Examples: lotteries, random audits (each entity equally likely to be picked), and the base from which software generates pseudorandom numbers. In the tutorial, `audit_draw` is Uniform(0, 1).

### Normal: when all else fails

Symmetric, bell-shaped, fully defined by its mean μ and variance σ². It is mathematically convenient and appears constantly because the CLT makes averages normal.

```latex
f(x\mid\mu,\sigma^2)=\frac{1}{\sqrt{2\pi\sigma^2}}\exp\!\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)
```

Examples: voter ideology scores, measurement error, income (approximately).

### Z-scores and the standard normal

A z-score says how many standard deviations an observation sits from its mean, which lets you compare across different normal distributions.

```latex
Z=\frac{X-\mu}{\sigma}
\qquad\text{Standard normal: } f(z)=\frac{1}{\sqrt{2\pi}}e^{-z^2/2},\ \mu=0,\ \sigma^2=1
```

Positive z is above average, negative below, and larger absolute value means further out. **Slide example:** a score of 85 on a test with μ = 75 and σ = 5 gives Z = (85 − 75)/5 = 2, two standard deviations above the mean. The standard normal is the benchmark for p-values and confidence intervals.

Useful anchors: about 68% of a normal lies within 1 SD of the mean, 95% within 2 (more precisely 1.96), and 99.7% within 3.

### Bivariate normal

The joint distribution of two normal variables, defined by two means, two variances, and a correlation ρ.

```latex
f(x,y)=\frac{1}{2\pi\sigma_x\sigma_y\sqrt{1-\rho^2}}\exp\!\left(-\frac{1}{2(1-\rho^2)}\left[\frac{(x-\mu_x)^2}{\sigma_x^2}-\frac{2\rho(x-\mu_x)(y-\mu_y)}{\sigma_x\sigma_y}+\frac{(y-\mu_y)^2}{\sigma_y^2}\right]\right)
```

It is drawn as a contour plot: when ρ = 0 the contours are circles (or axis-aligned ellipses), and the larger |ρ| is, the more the ellipse tilts and narrows along the diagonal. Examples: education and income, voter ideology and campaign donations.

**Why normality matters for regression.** Linear regression assumes normally distributed errors, which is what makes confidence intervals and hypothesis tests on coefficients exact in small samples.

## 8. Linear model extensions

The last slides show how a linear model can be bent to fit richer theories while staying linear in its parameters. Each extension has a theoretical reason to use it.

**The basic specification.** Every model has a systematic component (the part explained by predictors) and a stochastic component (the random error):

```latex
Y_i = \underbrace{\beta_0+\beta_1X_{1i}+\beta_2X_{2i}}_{\text{systematic}} + \underbrace{\varepsilon_i}_{\text{stochastic}},\qquad \varepsilon_i\sim\mathcal{N}(0,\sigma^2)
```

Equivalently, Yᵢ \~ N(μᵢ, σ²) with μᵢ = β₀ + β₁X₁ᵢ + β₂X₂ᵢ. This is the normal-DGP view of regression from Section 7.

| Extension | Idea | Slide example | Specification sketch |
| --- | --- | --- | --- |
| Interaction | The effect of one variable depends on the level of another; relationships are conditional, not just additive | Effect of education on income differs by gender (Education × Gender) | Y = β₀ + β₁Edu + β₂Gender + β₃(Edu × Gender) + ε |
| Exponential effects | Multiplicative, nonlinear relationships; effects that grow or decay rapidly | Population growth, compound interest | Transform a variable (e.g. exponential or log form) so growth is captured |
| Saturated model | All main effects and all interactions among the predictors; fits the observed cells perfectly | Two binary variables: both main effects plus their interaction | Y = β₀ + β₁A + β₂B + β₃(A × B) + ε |

**Reading an interaction.** In the education-by-gender model, the effect of one more year of education is β₁ for the reference group and β₁ + β₃ for the other group. β₃ is the difference in slopes; β₂ alone is the gender gap only where Education = 0. Never interpret a main effect in isolation once an interaction is present.

**Saturated models.** With two binary predictors there are four cells (00, 01, 10, 11), and four parameters let the model reproduce each cell's mean exactly, so residual variance is minimized and sometimes zero. That is why it serves as a baseline: simpler models are judged by how much fit they give up relative to it.

**Exponential effects.** The lecture frames these as multiplicative growth or decay, useful when effects accelerate or tip off quickly (population growth, compound interest, cumulative effects on policy adoption): Y rises slowly at first, then faster and faster. He says both this and interactions return later in the course, so know the intuition, not the algebra. Also from the lecture: a saturated model memorizes the sample, so it generalizes poorly and is used as a baseline.

## 9. Code tutorial walkthrough

The R and Python files are the same five parts, using only simulated data (seed 123, n = 10,000). Running them yourself is the fastest way to make Sections 2 to 6 concrete.

| Part | What the code does | Concept it demonstrates | Result to expect |
| --- | --- | --- | --- |
| 1. DGP families | Draws Bernoulli (p = 0.08), Poisson (λ = 3.5), Exponential (mean 24), Uniform(0, 1), Normal(0, 1); plots four histograms | Matching a family to a political outcome | war\_onset mean ≈ 0.08; cabinet\_months mean ≈ 24; Poisson and Exponential visibly skewed, Uniform flat, Normal bell-shaped |
| 2. Sampling distribution | 5,000 means of samples of 50 Exponential(24) draws | The sampling distribution and standard error | Histogram centered at 24; empirical SD close to 24/√50 ≈ 3.39 |
| 3. CLT | Sample means of Exponential draws at n = 2, 10, 50, 200 (3,000 each) | Skewed data, increasingly normal means | Skewed at n = 2, bell-shaped by n = 50, narrower at n = 200 |
| 4. Bias and consistency | Variance of N(0, 2²) samples of 10, divide-by-n vs divide-by-(n − 1), 10,000 reps; then n up to 10,000 | Bias vs consistency | Divide-by-n averages below 4 (about 3.6); divide-by-(n − 1) averages about 4; both converge to 4 as n grows |
| 5. Regression slope | 3,000 datasets of 100 points with y = 1 + 0.5x + noise; fit OLS each time | The same logic applies to any estimator | Slopes centered at the true 0.5, roughly bell-shaped |

**Code idioms worth knowing**

- **Repeated sampling.** R: `replicate(5000, mean(rexp(50, rate = 1/24)))`. Python: draw a 5,000 × 50 array and take `.mean(axis=1)`. Both give 5,000 sample means.
- **Empirical vs theoretical SE.** `sd(sample_means)` in R and `sample_means.std(ddof=1)` in Python should land near σ/√n.
- **Parameterization trap.** Exponential: R uses `rate`, NumPy uses `scale` = 1/rate.
- **Variance estimators.** R's `var()` and NumPy's `ddof=1` both divide by n − 1. The tutorial's `var_n` function computes the divide-by-n version by hand.
- **Slope extraction.** R: `coef(lm(y ~ x))[2]`. Python: `np.polyfit(x, y, 1)[0]`.

**What each simulation teaches in one line.** Part 2: one sample is one draw from a distribution. Part 3: averaging tames skew. Part 4: bias is a small-sample problem, consistency a large-sample one. Part 5: coefficients, not just means, have sampling distributions.

## 10. Practice problems with worked solutions

The quiz is ungraded, and its answer key was posted separately, which I did not have. These solutions are my own; attempt each problem first, then compare with the key.

**1. Poisson, λ = 4, P(X = 2).**

```latex
P(X=2)=\frac{e^{-4}\,4^2}{2!}=8e^{-4}\approx 8(0.018)=0.144
```

**2. Bernoulli, p = 0.3.** (a) Mean = p = 0.3. Variance = p(1 − p) = 0.3 × 0.7 = 0.21. (b) Variance is maximized at p = 0.5 (value 0.25). Intuition: outcomes are most uncertain when success and failure are equally likely; at the extremes the outcome is nearly certain.

**3. Binomial PMF.** P(X = k) = C(n, k) p^k (1 − p)^(n−k) for k = 0, 1, …, n. The parameter n is the number of independent trials; p is the probability of success on each trial. A Bernoulli is a Binomial with n = 1, a single trial.

**4. Linear model for incumbent vote share.**

```latex
VoteShare_i=\underbrace{\beta_0+\beta_1\,Spending_i+\beta_2\,Incumbent_i}_{\text{systematic component}}+\underbrace{\varepsilon_i}_{\text{stochastic component}},\qquad \varepsilon_i\sim\mathcal{N}(0,\sigma^2)
```

The conventional assumption is that errors are normally distributed with mean zero and constant variance, independent across observations. Incumbent is a 0/1 indicator.

**5. Civil-war onsets, λ = 1 per year.** (a) P(X ≥ 1) = 1 − P(X = 0) = 1 − e^(−1) ≈ 1 − 0.368 = 0.632. (b) "At least one" would otherwise require summing infinitely many terms (X = 1, 2, 3, …). The complement is a single term, P(X = 0) = e^(−λ).

**6. Cabinet survival, Exponential with λ = 0.5 per year.** (a) Mean = 1/λ = 2 years. (b) P(X > 2) = e^(−0.5 × 2) = e^(−1) ≈ 0.368. (c) Memoryless: a cabinet that has already survived 3 years has exactly the same chance of lasting any further period as a new cabinet. Having survived so far carries no information about the remaining time.

**7. Skewed durations, repeated samples of n = 200.** (a) The sample means are approximately normally distributed, by the Central Limit Theorem. (b) As n grows the spread of the sample means shrinks (SE = σ/√n). The skewness of the underlying data does not change; it is a property of the population. Only the sampling distribution of the mean becomes more symmetric.

**8. X \~ Uniform(2, 8).** (a) f(x) = 1/(8 − 2) = 1/6 ≈ 0.167 for 2 ≤ x ≤ 8, and 0 elsewhere. (b) Mean = (2 + 8)/2 = 5. Variance = (8 − 2)²/12 = 36/12 = 3.

**9. Matching DGPs.**

| Outcome | Family | Reason |
| --- | --- | --- |
| (a) How long a ceasefire lasts | Exponential (or Weibull if the risk changes with time) | It is a time-until-event duration; Exponential assumes a constant hazard |
| (b) Whether a bill passes | Bernoulli | A single yes/no outcome with some probability of success |
| (c) Executive orders signed per month | Poisson | A count of events in a fixed interval at a roughly constant rate |

**10. The CLT, stated carefully.** For independent and identically distributed draws with mean μ and finite variance σ², the sample mean is approximately Normal with mean μ and variance σ²/n once n is large, regardless of the population's shape. The two key conditions are that the draws are independent and identically distributed.

## 11. Quick-reference cheat sheet

| Family | Use for | Parameters | Mean | Variance | Key fact |
| --- | --- | --- | --- | --- | --- |
| Bernoulli | One yes/no outcome | p | p | p(1 − p) | Variance max at p = 0.5 |
| Binomial | Successes in n trials | n, p | np | np(1 − p) | Bernoulli is n = 1 |
| Poisson | Event counts | λ | λ | λ | Mean equals variance; P(X ≥ 1) = 1 − e^(−λ) |
| Exponential | Time to event, constant hazard | λ (rate) | 1/λ | 1/λ² | Memoryless; P(T > t) = e^(−λt) |
| Weibull | Time to event, changing hazard | k, λ | depends | depends | k < 1 falling, k = 1 constant, k > 1 rising hazard |
| Uniform | Equal chance on \[a, b\] | a, b | (a + b)/2 | (b − a)²/12 | Density 1/(b − a) |
| Normal | Catch-all, CLT limit | μ, σ² | μ | σ² | Z = (X − μ)/σ |

**Definitions to know verbatim**

- **Population:** the entire set of possible observations or units of analysis.
- **Sample:** a subset of the population selected for analysis.
- **Statistic:** a numeric measure computed from sample data.
- **Sampling distribution:** the probability distribution of a statistic computed from repeated samples from a population.
- **Standard error:** the standard deviation of a sampling distribution; for the mean, σ/√n.
- **CLT:** given a sufficiently large n, the sampling distribution of the sample mean approaches a normal distribution regardless of the population distribution. Requires independent, identically distributed observations.
- **Bias:** E\[θ̂\] − θ. **Variance:** E\[(θ̂ − E\[θ̂\])²\]. **Consistency:** converges in probability to the true parameter as n grows. **Efficiency:** lowest variance among unbiased estimators.
- **MSE = Bias² + Variance.**
- **Hazard rate:** the instantaneous probability of the event at time t, given it has not yet occurred.
- **Memoryless:** the chance of the event does not depend on time already elapsed.

**Fast number checks**

- Poisson P(X = k) = e^(−λ) λ^k / k!
- "At least one" = 1 − P(none).
- Exponential mean = 1/rate; software may want the rate or the scale.
- Divide by n − 1 for an unbiased variance estimate.

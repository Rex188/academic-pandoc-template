# Introduction

Musical performance is visceral. Every gesture links physical effort to sonic response. In digital musical instrument design, however, engineers frequently dismiss bodily motor variability as disposable sensor noise, scrubbing out micro-gestural nuances with aggressive low-pass filters. Modern synthesizers often introduce synthetic variation through pseudo-random number generators (PRNG) or chaotic maps. Human performers meanwhile introduce their own biological fluctuations that follow neither rule. Do these bodily motor variations reflect purely random drift, or do they contain structured, state-dependent dynamical organization? Answering this question matters: gestural synthesizers cannot respond to expressive nuance if they misdiagnose neuromuscular coordination as thermal noise.

Physiological time series—including postural sway, cardiac intervals, and rhythmic finger tapping—frequently exhibit $1/f^\beta$ power-law spectral scaling ($\beta \approx 1$) [@gilden1995; @hausdorff1995; @hausdorff2009]. Such scaling confirms that biological movement possesses long-range temporal memory rather than uncorrelated white noise. Inspired by this literature, we initially formulated an intuitive hypothesis ($H_{\text{init}}$): *expressive bodily motor fluctuations across diverse percussive gestures can be comprehensively characterized by a single linear metric—the power-law spectral exponent $\beta$—with expert coordination reflecting self-organized pink noise ($\beta \approx 1$).*

Empirical experiments on our custom gesture-augmented instrument, Robin, quickly overturned this initial hypothesis. In rapid percussive rolls driven by rapid wrist articulation, the linear Fourier slope collapsed to $\beta \approx 0.16$, while lag-1 autocorrelation plummeted. Judged by a single linear metric, the performer's high-speed virtuosity looked indistinguishable from uncorrelated white noise. Simultaneously, testing against synthetic benchmarks demonstrated that nonlinear dynamical systems produce fractured sub-band slopes ($\Delta_\beta = 2.54$), rendering a single regression slope mathematically ill-defined.

These empirical anomalies exposed a fundamental reality: *linear spectral slopes fail to capture the true dynamical organization of expressive musical gesture.* By definition, Gaussian white noise passed through an appropriate linear filter precisely reproduces an empirical $1/f$ spectrum without possessing underlying state-dependent constraints [@taqqu1995]. Observing a $1/f$ slope cannot determine whether bodily variability reflects an unconstrained linear random walk or structured, state-dependent motor coordination.

To resolve this limitation, we discarded the single-axis assumption and constructed a decoupled **Two-Axis Framework**:
- **Axis 1 (Spectral Slope / Distribution)**: Uses Welch power spectral density estimation subject to a sub-band stability check ($\Delta_\beta < 0.20$) strictly as a descriptive baseline to characterize macroscopic energy decay.
- **Axis 2 (Physical Nonlinearity)**: Deploys an adversarial surrogate testing engine using Iterative Amplitude Adjusted Fourier Transform (IAAFT) phase-randomized surrogates and Sample Entropy (SampEn) as the discriminating statistic.

Certified against synthetic controls (PRNG noise and chaotic maps) and hardware decimation audits, our framework resolves the rapid roll paradox. Where linear metrics misclassify high-speed rolls as white noise, Axis 2 unmasks their structured nonlinear temporal regularity ($p = 0.0198$).

Our findings advance gestural interaction along three dimensions. First, we demonstrate empirically that linear spectral slopes fail to characterize expressive gesture, collapsing under rhythmic rolls to white-noise appearance ($\beta \approx 0.16$) and failing on multi-scale synthetic attractors ($\Delta_\beta = 2.54$). Second, across all physical axes and performance regimes, Robin accelerometer streams decisively reject the linear stochastic surrogate null ($p = 0.0198$). Expressive gestures do not drift through unconstrained linear walks; they exhibit structured, state-dependent nonlinear temporal regularity. Third, we release a fully verified experimental pipeline and sensor benchmark for single-case gestural dynamics, anchoring gesture-aware synthesis in physical mechanics rather than speculative metaphysical claims.

# Related Work and Methodological Background

## 1/f Power-Law Scaling in Human Motor Control
Scale-free fluctuations pervade human motor control. Gilden et al. [@gilden1995] and Hausdorff et al. [@hausdorff1995; @hausdorff2009] demonstrated this in tapping intervals, postural sway, and stride timing. Power spectra in these physiological signals reliably scale as $1/f^\beta$ ($\beta \approx 1$). For decades, researchers treated this pink spectrum as evidence of self-organized criticality, distributed biological memory, or multi-scale neuromuscular coordination [@treffner1999; @west2010].

Yet second-order linear statistics—Fourier spectral slopes and autocorrelation functions—leave an unresolved gap. By definition, linear stochastic theory proves that Gaussian white noise passed through an infinite impulse response linear filter reproduces a $1/f$ power spectrum perfectly [@taqqu1995]. No underlying dynamical rules or nonlinear constraints are required. Spectral decay alone cannot differentiate a passive linear random walk from structured, state-dependent motor coordination.

## Nonlinear Dynamics in Biomechanical Signals
Seeking to move beyond linear correlation, biomechanical researchers turned to nonlinear dynamics, investigating deterministic chaos and invariant measures in gait and tremor [@dingwell2000; @abarbanel1996]. Standard tools include phase-space delay embedding, correlation dimension ($D_2$) [@grassberger1983], and the largest Lyapunov exponent ($\lambda_1$) [@rosenstein1993].

In practice, computing $\lambda_1$ or $D_2$ on empirical gestures encounters severe mathematical limits. Kantz and Schreiber [@kantz2004] demonstrated that estimating invariant measures reliably requires long, strictly stationary, weakly noise-corrupted sequences, typically exceeding $N > 10^4$ samples. On short records ($N \sim 10^3$) subject to anatomical damping, sensor noise, or digital quantization, dimension estimation algorithms routinely report spurious low-dimensional attractors for purely linear stochastic noise [@rapp1993; @theiler1991]. Single-index chaos metrics are fragile. Applying them directly to musical gestures yields unverified claims.

## Surrogate Data Testing and Sample Entropy
Surrogate testing bypasses this limitation. Pioneered by Theiler et al. [@theiler1992] and refined by Schreiber and Schmitz [@schreiber1996; @schreiber2000], surrogate analysis converts dynamical identification into statistical hypothesis testing without requiring asymptotic phase-space reconstruction. The Iterative Amplitude Adjusted Fourier Transform (IAAFT) generates synthetic surrogates under the null hypothesis that the observed series represents a linearly filtered Gaussian process distorted by an invertible static measurement function. By simultaneously matching the empirical power spectrum and the exact amplitude distribution, IAAFT destroys temporal phase alignment while preserving all linear properties.

Separating empirical data from this surrogate ensemble requires an appropriate discriminating statistic. Linear spectral metrics cannot serve this role: by construction, surrogates replicate the power spectrum. Instead, Sample Entropy (SampEn) [@richman2000]—a bias-reduced formulation of Approximate Entropy [@pincus1991]—quantifies the conditional probability that sub-sequences of length $m$ matching within tolerance $r$ remain similar at length $m+1$. SampEn demonstrates high stability on short, noisy records ($N \sim 10^3$) and resists baseline nonstationarity [@costa2002].

Surrogate testing has been utilized in cardiovascular (ECG) and neural (EEG) dynamics [@kugiumtzis2000], yet its systematic deployment in Digital Musical Instruments (DMI) and percussive kinematics remains unexplored. Furthermore, existing studies rarely evaluate surrogate pipelines against simultaneous negative (PRNG) and positive (chaotic attractor) baselines, nor do they account for sensor-level oversampling plateaus. We address this gap by combining an IAAFT-SampEn architecture with synthetic controls and physical decimation audits across diverse performance regimes.

# Methodology

## Overview of the Two-Axis Framework
To overcome the failure of single-index linear indicators, we formulated the decoupled **Two-Axis Framework** depicted in @fig-main-evidence. Rather than conflating spectral decay with motor organization, the pipeline processes inertial data along two orthogonal dimensions:

- **Axis 1 (Noise Color)** evaluates macroscopic spectral tilt $\beta$ using Welch power spectral density estimation, subject to a sub-band stability rule ($\Delta_\beta < 0.20$) to prevent spurious power-law claims.
- **Axis 2 (Nonlinear Determinism)** subjects the time series to an adversarial surrogate test, comparing its empirical Sample Entropy against an ensemble of 100 IAAFT phase-randomized surrogates.

![Master empirical evidence across synthetic controls and percussive performance regimes. Top row: Welch power spectral density curves with log-log regression fits. Bottom row: Empirical Sample Entropy values (red dashed lines) against 100-surrogate IAAFT null distributions (blue bars).](images/fig1_main_evidence.png){#fig-main-evidence width=100%}

## Acquisition Platform and Experimental Performance Regimes
Primary biomechanical data were acquired using **Robin**, a custom gesture-augmented digital instrument integrating eleven capacitive touch pads and an onboard three-axis accelerometer.

### Instrument Architecture and Quantization
Robin's onboard microcontroller digitizes physical acceleration and transmits standard 7-bit MIDI Continuous Controller (CC) messages ($0\sim 127$) over USB. Accelerometer streams were logged continuously at a uniform sampling rate of $f_s = 50\text{ Hz}$. This setup mirrors real-world performance conditions: finite quantization resolution, asynchronous serial transmission, and bounded sensor dynamics.

### Percussive Performance Regimes
Recordings capture three distinct percussive regimes performed by an expert percussionist:
- **Regime T1 (Impulsive Strikes, $N = 2250$, duration $45\text{ s}$)**: Discrete mallet strikes characterized by rapid impulsive acceleration transients followed by natural viscoelastic wrist damping.
- **Regime T2 (Locomotion Flow, $N = 2250$, duration $45\text{ s}$)**: Continuous gestural flow involving whole-body postural shifts, spatial orientation changes, and sustained instrument re-positioning.
- **Regime T3 (Rapid Percussive Rolls, $N = 2250$, duration $45\text{ s}$)**: Rapid alternating orchestral rolls dominated by high-frequency wrist oscillations ($8\sim 12\text{ Hz}$), operating at the physical limits of motor entrainment.

## Axis 1: Welch Power Spectral Density and Sub-Band Stability
Power Spectral Density (PSD) $S(f)$ is estimated via Welch's averaged periodogram with Hanning windowing and $50\%$ segment overlap. Over the empirical scaling range $[f_{\text{min}}, f_{\text{max}}] = [0.5\text{ Hz}, 20.0\text{ Hz}]$, the spectral exponent $\beta$ is computed via ordinary least squares regression:

$$\log S(f) = -\beta \log f + C$$

To verify that an estimated slope represents genuine scale-invariance rather than mixed-frequency dynamics, the frequency range is divided into equal log-scale sub-bands $[f_{\text{min}}, f_{\text{mid}}]$ and $[f_{\text{mid}}, f_{\text{max}}]$. Independent regressions yield low-band slope $\beta_{\text{lo}}$ and high-band slope $\beta_{\text{hi}}$. We enforce the **Sub-Band Stability Rule**:

$$\Delta_\beta = |\beta_{\text{lo}} - \beta_{\text{hi}}| < 0.20$$

When $\Delta_\beta \ge 0.20$, scale-invariance is rejected. The signal is designated as possessing multi-scale or resonant dynamics, preventing invalid power-law claims.

## Axis 2: Iterative Amplitude Adjusted Fourier Transform (IAAFT) Surrogates

### Null Hypothesis Formulation
Axis 2 tests the formal null hypothesis $H_0$: *The observed time series $z(t)$ is generated by a stationary linear Gaussian stochastic process, possibly distorted by an invertible, static, nonlinear measurement function $s(t) = g(z(t))$.*

### Iteration Mechanism
For an empirical series $z = \{z_1, \dots, z_N\}$, let $\{s_k\}$ denote its sorted values and $\{A_k = |\mathcal{F}(z)_k|\}$ its discrete Fourier amplitude spectrum. Surrogates are initialized by randomly shuffling $z$:

$$z^{(0)} = \pi(z)$$

Each iteration $i$ executes two alternating projections:
1. **Spectral Matching**: Enforce the empirical Fourier amplitude spectrum while preserving current phases $\phi^{(i)}$:
   $$y^{(i)} = \mathcal{F}^{-1}\left( A_k \exp\left(i \phi^{(i)}_k\right) \right)$$
2. **Amplitude Distribution Matching**: Rank-order substitute the sorted values $\{s_k\}$ into $y^{(i)}$:
   $$z^{(i+1)} = \text{rank\_match}\left(y^{(i)}, \{s_k\}\right)$$

Convergence is evaluated by monitoring maximum spectral discrepancy:

$$\epsilon_i = \max_k \left| |\mathcal{F}(z^{(i)})_k| - A_k \right|$$

Iterations terminate when $\epsilon_i < 10^{-4}$ or upon reaching 500 iterations. As illustrated in @fig-iaaft-pipeline, this procedure completely randomizes nonlinear phase coupling while preserving the exact linear power spectrum and amplitude histogram.

![Visual mechanics of the IAAFT surrogate generation algorithm. The empirical time series (top) is iteratively transformed to generate surrogate ensembles that match both the empirical power spectral density and marginal probability distribution (middle panels) while destroying phase coupling and temporal determinism (bottom).](images/fig2_iaaft_pipeline.png){#fig-iaaft-pipeline width=100%}

## Discriminating Statistic: Sample Entropy
Given an embedded sequence of vectors $\mathbf{x}_m(i) = [x_i, x_{i+1}, \dots, x_{i+m-1}]$ in embedding dimension $m$, the distance between vectors is defined under the Chebyshev norm:

$$d[\mathbf{x}_m(i), \mathbf{x}_m(j)] = \max_{k=0,\dots,m-1} |x_{i+k} - x_{j+k}|$$

Let $B_i^m(r)$ denote $(N - m)^{-1}$ times the number of vectors $\mathbf{x}_m(j)$ ($j \ne i$) such that $d[\mathbf{x}_m(i), \mathbf{x}_m(j)] \le r$. The average over all $i$ is:

$$B^m(r) = \frac{1}{N - m} \sum_{i=1}^{N - m} B_i^m(r)$$

Increasing the template length to $m+1$ yields $A^m(r)$. Sample Entropy is defined as:

$$\text{SampEn}(m, r, N) = -\ln \left( \frac{A^m(r)}{B^m(r)} \right)$$

Following established physiological benchmarks [@richman2000], we fix $m = 2$ and $r = 0.20 \times \sigma_x$, where $\sigma_x$ represents sequence standard deviation.

## Statistical Inference
For an empirical record, we generate an ensemble of $B = 100$ independent IAAFT surrogates, computing Sample Entropy for each. Let $k$ denote the number of surrogates whose entropy is less than or equal to empirical entropy:

$$k = \sum_{b=1}^B \mathbb{I}\left( \text{SampEn}_{\text{surr}}^{(b)} \le \text{SampEn}_{\text{real}} \right)$$

The exact non-parametric one-sided rank $p$-value is:

$$p = \frac{k + 1}{B + 1}$$

For $B = 100$, the minimum achievable rank floor is $p_{\text{min}} = 1/101 \approx 0.0099$ (or $2/101 \approx 0.0198$ for a two-sided test). We adopt a significance threshold of $\alpha = 0.05$. When $p < 0.05$, the linear Gaussian null hypothesis is rejected in favor of nonlinear temporal regularity.

# Experiments and Results

## Synthetic Control Benchmarking
To certify our surrogate testing pipeline against false discoveries, we evaluated two synthetic baselines across 10 independent random seeds ($N = 2048$, $N_{\text{surr}} = 100$):

1. **Negative Control (Uniform PRNG)**: A 32-bit Mersenne Twister uniform pseudo-random number generator, representing uncorrelated linear stochastic noise without temporal memory.
2. **Positive Control (Logistic Chaos)**: The classic 1D chaotic Logistic map $x_{n+1} = r x_n (1 - x_n)$ with parameter $r = 3.9$, possessing full deterministic phase coupling and a positive Lyapunov exponent ($\lambda_1 \approx 0.49$).

@tbl-synthetic-controls summarizes the benchmark validation. The PRNG sequence yielded an empirical Sample Entropy of $\text{SampEn} = 2.187 \pm 0.012$, which fell squarely within its surrogate ensemble ($\text{SampEn}_{\text{surr}} = 2.178 \pm 0.017$), producing an average rank $p$-value of $p = 0.6139$ with $0/10$ null rejections (Type-I false positive rate $= 0\%$).

Conversely, the Logistic map produced an empirical entropy of $\text{SampEn} = 0.518 \pm 0.008$, whereas its phase-randomized surrogates exhibited substantially higher entropy ($\text{SampEn}_{\text{surr}} = 1.569 \pm 0.022$). In $10/10$ independent seeds, every surrogate exhibited higher entropy than the empirical sequence ($k = 0$), yielding the theoretical minimum $p = 0.0198$. The surrogate engine demonstrates $100\%$ detection power on nonlinear deterministic dynamics.

| Benchmark System | Generator / Parameters | $N$ | SampEn (Observed) | SampEn (IAAFT Null) | Rank-$p$ | Empirical Decision |
|---|---|---|---|---|---|---|
| **Negative Control (PRNG)** | Mersenne Twister (Uniform) | 2048 | 2.187 | $2.178 \pm 0.017$ | 0.6139 | Null Accepted ($0/10$ false alarms) |
| **Positive Control (Chaos)** | Logistic Map ($r = 3.9$) | 2048 | 0.518 | $1.569 \pm 0.022$ | 0.0198* | Null Rejected ($10/10$ detections) |

: Synthetic Control Benchmark Validation Across 10 Independent Seeds ($p = 0.0198$ represents the exact two-sided rank floor for $B=100$). {#tbl-synthetic-controls}

Furthermore, while PRNG exhibited stable spectral slopes ($\beta = -0.06 \pm 0.04$, $\Delta_\beta < 0.10$), the Logistic map produced a massive sub-band discrepancy: $\beta_{\text{lo}} \approx -0.10$ in low frequencies and $\beta_{\text{hi}} \approx -2.65$ in high frequencies ($\Delta_\beta = 2.54$). A forced single regression yields an arbitrary slope of $\beta \approx -1.30$. This benchmark confirms that *single-index spectral slopes are mathematically ill-defined on nonlinear attractors*. Power-law slopes cannot serve as an unverified single axis for dynamical classification.

## Empirical Kinematics Across Performance Regimes
We evaluated the primary three-axis accelerometer streams logged from Robin across the three percussive performance regimes. As shown in @tbl-empirical-gestures, Axis 1 spectral characteristics diverge across physical regimes, whereas Axis 2 identifies invariant physical nonlinearity across all axes and conditions.

| Performance Regime | Axis | ACF(1) | Shannon (bits) | Axis 1: PSD $\beta$ | SampEn (Observed) | SampEn (IAAFT Null) | Decision ($p$-value) |
|---|---|---|---|---|---|---|---|
| **Robin T1: Impulsive Strike** | $X$ | 0.900 | 4.52 | $1.55 \pm 0.13$ | 0.497 | $1.279 \pm 0.021$ | Reject Null ($p = 0.0198^*$) |
| **Robin T1: Impulsive Strike** | $Y$ | 0.877 | 4.73 | $1.90 \pm 0.12$ | 0.528 | $1.238 \pm 0.031$ | Reject Null ($p = 0.0198^*$) |
| **Robin T1: Impulsive Strike** | $Z$ | 0.881 | 4.55 | $1.47 \pm 0.12$ | 0.499 | $1.318 \pm 0.021$ | Reject Null ($p = 0.0198^*$) |
| **Robin T2: Locomotion Flow** | $X$ | 0.986 | 4.77 | $2.13 \pm 0.07$ | 0.311 | $0.505 \pm 0.023$ | Reject Null ($p = 0.0198^*$) |
| **Robin T2: Locomotion Flow** | $Y$ | 0.949 | 4.38 | $2.25 \pm 0.09$ | 0.337 | $0.777 \pm 0.032$ | Reject Null ($p = 0.0198^*$) |
| **Robin T2: Locomotion Flow** | $Z$ | 0.948 | 4.41 | $1.79 \pm 0.09$ | 0.423 | $1.039 \pm 0.033$ | Reject Null ($p = 0.0198^*$) |
| **Robin T3: Rapid Roll** | $X$ | 0.659 | 3.69 | $0.16$ (Resonant) | 0.718 | $1.807 \pm 0.025$ | Reject Null ($p = 0.0198^*$) |
| **Robin T3: Rapid Roll** | $Y$ | 0.798 | 4.11 | $0.21$ (Resonant) | 0.700 | $1.468 \pm 0.040$ | Reject Null ($p = 0.0198^*$) |
| **Robin T3: Rapid Roll** | $Z$ | 0.688 | 3.42 | $0.51$ (Resonant) | 0.548 | $1.135 \pm 0.025$ | Reject Null ($p = 0.0198^*$) |

: Empirical Kinematic Dynamics and Surrogate Hypothesis Testing Across Robin Performance Regimes ($N = 2250$, duration $45\text{ s}$, $B = 100$ surrogates; $p = 0.0198$ represents the exact rank-test floor). {#tbl-empirical-gestures}

### Axis 1 Analysis (Spectral Signatures)
1. **Regime T1 (Impulsive Strikes)**: Exhibits colored noise with spectral slopes from $\beta = 1.47 \pm 0.12$ ($Z$-axis) to $\beta = 1.90 \pm 0.12$ ($Y$-axis). High lag-1 autocorrelation ($\text{ACF}(1) \approx 0.88$) and red-shifted spectra reflect the sharp rise and viscoelastic damping of limb impacts.
2. **Regime T2 (Locomotion Flow)**: Displays steep power-law decay, with $\beta$ reaching $2.13 \sim 2.25$ and lag-1 autocorrelation reaching $0.986$. Low-frequency gravitational orientation changes and bodily inertia dominate the energy profile.
3. **Regime T3 (Rapid Percussive Rolls)**: Breaks the colored noise pattern entirely: the apparent spectral slope collapses to $\beta = 0.16 \pm 0.12$ ($X$-axis) and $\beta = 0.21 \pm 0.11$ ($Y$-axis). Dominant oscillatory components associated with rapid percussive wrist articulation ($3\sim 6\text{ Hz}$) inject kinetic energy into the upper observable spectrum (strictly within the $8.33\text{ Hz}$ Nyquist bandwidth of native $16.67\text{ Hz}$ transmission), flattening the global spectral slope.

### Axis 2 Analysis: Invariant Physical Nonlinearity Across Performance Regimes
In contrast to divergent spectral slopes on Axis 1, **Axis 2 reveals invariant dynamical structure across all three regimes and spatial axes**:
- For every empirical series, empirical Sample Entropy is substantially lower than that of the phase-randomized surrogate ensemble (e.g., T1 $X$-axis: $\text{SampEn}_{\text{real}} = 0.497$ vs. $\text{SampEn}_{\text{surr}} = 1.279 \pm 0.021$).
- In all 9 tests (3 regimes $\times$ 3 axes), the real series ranked strictly lower than all 100 surrogates ($k = 0$), yielding identical floor $p$-values of $p = 0.0198 < 0.05$.
- Even when constrained to identical Fourier power spectra and marginal histograms, linear stochastic processes fail to replicate the low entropy of human motor variability. Bodily fluctuations during expressive performance reflect structured nonlinear temporal regularity rather than linear stochastic drift.

![Phase-space delay coordinate reconstruction ($\mathbf{v}(t) = [x(t), x(t+\tau)]$) comparing empirical kinematics against phase-randomized IAAFT surrogates across performance regimes. (a) Time series with highlighted 3–4 s gesture windows. (b) Empirical phase orbits display coherent, topologically continuous limit-cycle loops (T1 strike loop, T2 directional flow, T3 bounded oscillatory cycles) reflecting neuromuscular momentum and biomechanical coordination. (c) Corresponding IAAFT surrogate trajectories collapse into erratic, uncoupled zigzags violating physical momentum, demonstrating that bodily fluctuations possess irreducible nonlinear phase structure.](images/fig_phase_space_embedding.png){#fig-phase-space-embedding width=100%}

## The Trial 3 Paradox: Falsifying the Single-Axis Paradigm
Regime T3 exposed the core flaw of the single-axis paradigm. In high-speed orchestral rolls, the linear Fourier slope plunged to $\beta \approx 0.16$, accompanied by a modest lag-1 autocorrelation ($\text{ACF}(1) = 0.659$). Under a linear lens, one would conclude that the performer's movements had degenerated into uncorrelated white noise.

![Empirical kinematic breakdown in Regime T3 (rapid percussive rolls). Left: Continuous 3-axis accelerometer time series showing sustained high-frequency alternating wrist articulation. Right: Power spectral density revealing the concentrated rhythmic articulation peak at $3\sim 6\text{ Hz}$ that flattens the global linear slope to $\beta \approx 0.16$.](images/fig3_trial3_resonance.png){#fig-trial3-resonance width=100%}

Sub-band analysis contradicts this superficial conclusion. Splitting the frequency band reveals $\beta_{\text{lo}} \approx 0.16$ at low frequencies and $\beta_{\text{hi}} \approx 1.05$ at higher frequencies ($\Delta_{\beta} = 0.89$). The signal violates scale-invariance because it is not white noise at all. As shown in @fig-trial3-resonance, the performer articulated Robin as a handheld drumstick in rapid percussive rolls. Gripping the instrument couples its effective mass with the wrist-forearm kinematic chain, shifting the dominant oscillatory components associated with rapid rolling into the $3\sim 6\text{ Hz}$ range—well within the $8.33\text{ Hz}$ Nyquist window. This voluntary rhythmic oscillation injects narrowband power into the upper spectrum, flattening the linear Fourier decay.

Axis 2 captures what linear spectra obscure. Phase randomization destroys the precise temporal timing that sustains fast wrist alternation. Consequently, empirical entropy ($\text{SampEn} = 0.718$) drops far below the surrogate ensemble ($\text{SampEn}_{\text{surr}} = 1.807 \pm 0.025$, $p = 0.0198$). As visually demonstrated in @fig-phase-space-embedding, where linear Fourier indicators see random drift, nonlinear surrogate testing and phase-space reconstruction unmask structured nonlinear temporal regularity (which may reflect coordinated biomechanical constraints, such as phase-locked motor coordination).

## Robustness Verification: Eliminating Zero-Order Hold Artifacts
Because Robin transmits MIDI CC over USB event-synchronously at $\approx 16.7\text{ Hz}$, uniform oversampling at $50\text{ Hz}$ resulted in approximately $70.5\%$ of adjacent samples sharing identical integer values. In an adversarial test, we verified that a pure linear Gaussian $1/f$ noise process subject to $70\%$ zero-order hold (ZOH) plateaus was falsely rejected by surrogate testing ($p = 0.0198$). ZOH plateaus artificially depress empirical Sample Entropy while surrogates scatter identical values across the sequence.

To verify that our empirical findings are not an artifact of ZOH plateaus, all empirical datasets were subjected to a factor-of-3 decimation audit ($N_{\text{dec}} = 750$, $f_{\text{native}} \approx 16.67\text{ Hz}$), completely eliminating adjacent repeated values. As detailed in @tbl-decimation, the results confirm physical robustness.

| Condition / Axis | Raw ZOH Repeats (%) | Decimated $N$ | SampEn (Observed) | SampEn (Surr Mean $\pm$ SD) | Rank-$p$ | Empirical Verdict |
|---|---|---|---|---|---|---|
| **Linear $1/f$ Simulation** | 68.1% (ZOH) $\to$ 0.0% | 750 | 1.565 | $1.507 \pm 0.038$ | 0.2178 | Null Accepted (Robust to artifacts) |
| **Robin T1 Strike ($X$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 1.045 | $1.793 \pm 0.033$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T1 Strike ($Y$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 1.086 | $1.769 \pm 0.041$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T1 Strike ($Z$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 0.972 | $1.846 \pm 0.051$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T2 Flow ($X$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.690 | $0.868 \pm 0.046$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T2 Flow ($Y$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.682 | $1.242 \pm 0.041$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T2 Flow ($Z$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.889 | $1.550 \pm 0.048$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T3 Roll ($X$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.160 | $2.138 \pm 0.056$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T3 Roll ($Y$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.210 | $1.772 \pm 0.050$ | 0.0198* | Reject Null (Physical Nonlinearity) |
| **Robin T3 Roll ($Z$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.206 | $1.480 \pm 0.034$ | 0.0198* | Reject Null (Physical Nonlinearity) |

: Decimation Robustness Audit at Native Transmission Rate ($f_{\text{native}} \approx 16.67\text{ Hz}$, $N_{\text{dec}} = 750$, Zero Repeated Plateaus). {#tbl-decimation}

Decimation audits definitively rule out zero-order hold artifacts:
1. Under 3x decimation, synthetic linear noise accepts the null hypothesis ($p = 0.2178$), showing that decimation eliminates spurious plateau-induced significance.
2. Under identical decimation, all nine empirical channels across all three performance regimes continue to reject the linear null hypothesis decisively at $p = 0.0198 < 0.05$.

The observed nonlinear structure represents genuine continuous biomechanics rather than sensor transmission artifacts.

# Discussion

## Re-Evaluating Contingency in Digital Musical Interaction
Acoustic instruments push back. String tension, mallet bounce, and reed resistance physically shape how hands move. When digital interfaces severed this mechanical feedback, designers faced a choice: filter out bodily motor variability or treat it as creative randomness. Both choices miss the mark. Bodily motor fluctuations are neither Gaussian noise nor unconstrained walks; they embody structured nonlinear dynamics governed by physiological inertia and sensorimotor feedback.

Treating bodily fluctuations as noise strips digital instruments of expressive depth. Conversely, assuming linear $1/f$ drift introduces sluggish, ungrounded stochastic behavior. Nuance-aware digital instruments must interface directly with the structured, state-dependent geometry of human movement.

## Demarcating Musical Agency from Metaphysical Claims
Agency grows out of physical resistance. Popular commentary often frames human agency as an ethereal middle ground between ironclad determinism and pure randomness. We reject that framing. Rejecting a linear stochastic null does not prove metaphysical free will or deterministic chaos. Claiming so would turn an empirical kinematic finding into an unfalsifiable philosophical assertion.

Operationally, agency appears in how a musician negotiates mechanical constraints through phase-space coordination. The rejection of the linear null demonstrates that bodily fluctuations obey structured, state-dependent dynamics. Expressive nuance lives in that negotiation—not in unconstrained noise.

## Practical Implications for DMI Design and Translational Kinematics
These empirical insights directly inform digital instrument engineering and clinical kinematics:
- **Nuance-Preserving Gesture Filters**: Contemporary DMIs widely deploy linear low-pass filters to suppress sensor jitter, inadvertently destroying the high-frequency nonlinear phase-space orbits revealed in Regime T3. Instrument designers should replace linear filters with state-preserving nonlinear filters (such as predictive Kalman filters or manifold projection filters) that preserve phase-space kinematic orbits while suppressing true stochastic quantization noise.
- **Gesture-Responsive Synthesis Mappings**: Rather than mapping raw sensor inputs directly to acoustic parameters or adding artificial PRNG jitter, sound engines can couple directly to gestural phase-space geometry. For instance, mapping synthesized timbre to the instantaneous divergence between real-time gestural entropy and linear surrogate baselines creates instruments that respond dynamically to the performer's neuromuscular control state, rewarding physical expressive focus during rapid rolls.
- **Translational Biomarkers in Motor Disorders**: The Two-Axis Framework provides an objective tool for clinical motor analysis. In neurodegenerative conditions such as Parkinson's disease or essential tremor, motor deficits frequently disrupt high-frequency neuromuscular coordination. By separating macroscopic tremor power (Axis 1) from phase-space loss of temporal regularity (Axis 2), our framework establishes a quantitative foundation for non-invasive motor assessment.

## Methodological Boundaries, Single-Subject Scope, and Future Work
Honesty regarding epistemological boundaries is paramount:
1. **Inherent Limitations of Single-Subject ($N=1$) Design**: Because all empirical data in this study derives from a single performer on the Robin interface, we explicitly refrain from asserting universal numerical constants (e.g., that all performers exhibit $\beta = 0.16$ during rolls, or produce identical SampEn intervals). Furthermore, the performer's personal percussive experience and idiosyncratic motor habits cannot be fully disentangled from general physiological traits, nor can findings on a handheld baton be immediately extrapolated to continuous touch surfaces or free-air optical controllers.
2. **Existential Falsification and Construct Validity**: Nonetheless, an idiographic $N=1$ design possesses decisive epistemological power. This single-case observation is sufficient to falsify the strong universal form of the $1/f$ prior, but not to characterize the distribution of spectral exponents across performers, gestures, or instruments. As an empirical counterexample, observing the collapse of the spectral exponent in Trial 3 ($\beta \approx 0.16$) decisively demonstrates that expressive human motor variability does not universally conform to scale-invariant pink noise. Crucially, the Two-Axis Framework demonstrates high diagnostic construct validity, successfully decoupling macro-gesture classification from micro-structural nonlinear regularity across all spatial axes.
3. **Future Work Roadmap (Structured by Epistemic Purpose)**: This study establishes clear empirical and theoretical signposts for subsequent investigation across four core dimensions:
   - *Population (Generalizability)*: Conducting systematic cohort studies comparing percussionists, string players, and non-musicians to determine whether the collapse of $\beta$ during rolls generalizes across diverse motor training backgrounds.
   - *Interface (Modality Independence)*: Applying the Two-Axis diagnostic pipeline across distinct interaction paradigms (continuous ribbons, multidimensional touch surfaces, and camera-based vision systems) to determine whether the observed dynamical decoupling is gesture-specific or interface-independent.
   - *Mechanism (Physiological Ground Truth)*: Synchronizing 240 Hz optical motion capture with 1000 Hz surface electromyography (sEMG) to evaluate cross-axis phase synchronization, phase-locking values (PLV), and formal attractor dimensions ($D_2$) and Lyapunov spectra under long-duration stationary conditions.
   - *Application (Translational DMI Engineering)*: Implementing prototype nuance-preserving nonlinear filters inside digital synthesizer firmware to evaluate latency, tactile transparency, and performer agency through double-blind evaluation.

# Conclusion

This paper resolved the ambiguity surrounding bodily motor fluctuations in digital musical performance. Beginning from the intuitive hypothesis that musical movement conforms to $1/f$ pink noise, our empirical experiments with Robin revealed that linear spectral slopes fail to capture the true dynamical organization of expressive musical gesture, collapsing during rapid percussive rolls ($\beta \approx 0.16$) and failing on multi-scale attractors ($\Delta_\beta = 2.54$).

Falsifying this single-axis assumption prompted the formulation of a decoupled Two-Axis Framework, separating macroscopic spectral decay from adversarial surrogate testing using IAAFT, Sample Entropy, and phase-space delay reconstruction. Across all spatial axes and performance regimes, human motor fluctuations decisively rejected the linear stochastic null ($p = 0.0198$). Physical gestures embody structured, state-dependent nonlinear dynamics rather than linear stochastic drift. Native-rate decimation audits verified that these conclusions are robust against digital quantization artifacts.

By embracing empirical anomalies rather than concealing them, this research moves beyond romanticized notions of human motor freedom. We provide a rigorous mathematical and empirical foundation for understanding bodily nuance, establishing that expressive musical agency resides in the structured physical negotiation between the human performer and the instrument.

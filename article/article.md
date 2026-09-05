


# Abstract

In digital musical instruments, bodily motor variability is frequently dismissed as unstructured sensor noise. Although physiological signals commonly exhibit $1/f^\beta$ power-law scaling, linear spectral indicators—such as the Fourier slope $\beta$—cannot separate low-dimensional deterministic dynamics from linearly filtered Gaussian noise, breaking down entirely during chaotic or high-frequency gestures. We resolve this limitation by introducing a **Two-Axis Framework** that decouples macroscopic spectral decay from higher-order nonlinear structure. Evaluated on primary three-axis inertial streams from Robin—a custom gesture-augmented instrument—across three percussion regimes (impulsive strikes, locomotion flow, and rapid orchestral rolls), the pipeline couples Welch spectral stability ($\Delta_\beta < 0.20$, Axis 1) with an Iterative Amplitude Adjusted Fourier Transform (IAAFT) surrogate engine using Sample Entropy as the discriminating statistic (Axis 2). Synthetic controls confirm rigorous false-positive resistance on PRNG noise ($p = 0.6139$, $0/10$ rejections) and $100\%$ detection power on chaotic dynamics ($p = 0.0198$, $10/10$ rejections). Across all spatial axes and performance regimes, bodily motion decisively rejects the linear stochastic null ($p < 0.02$, the 100-surrogate rank floor). Crucially, rapid rolls flatten linear spectra to near-white levels ($\beta \approx 0.16$), yet Axis 2 unmasks their deterministic neuromuscular coordination. Native-rate decimation audits confirm that significance persists independent of digital zero-order hold artifacts, establishing that expressive musical gestures embody deterministic nonlinear manifolds rather than stochastic drift.

**Keywords**: Gesture Analysis, Digital Musical Instruments, Surrogate Data Testing, Sample Entropy, $1/f$ Noise, Nonlinear Dynamics, Percussion Kinematics.

# Section 1: Introduction

Expressive musical performance relies fundamentally on physical interaction, where artistic intention is continually mediated by bodily movement. While digital musical instruments increasingly incorporate computational algorithms—ranging from pseudo-random number generators (PRNG) to deterministic chaotic mappings—to generate stochastic variations, human performers inevitably introduce their own bodily fluctuations. In digital musical instrument (DMI) design, this motor variability is commonly dismissed as unstructured sensor noise. Whether these physical fluctuations share the mathematical structure of algorithmic randomness, deterministic chaos, or correlated stochastic processes remains an unresolved empirical question. Resolving this ambiguity is critical both for designing nuance-aware gestural interfaces and for understanding motor variability as an active constituent of musical agency.

Physiological time series—including postural sway, cardiac intervals, and rhythmic finger tapping—widely exhibit $1/f^\beta$ power-law spectral scaling ($\beta \approx 1$). Such scaling confirms that biological movement possesses temporal memory rather than uncorrelated white noise. Crucially, however, linear spectral indicators such as the Fourier spectral slope $\beta$ or the autocorrelation function cannot distinguish low-dimensional deterministic dynamics from linearly filtered Gaussian noise. Any linear stochastic process can be filtered to mimic an empirical $1/f^\beta$ power law without possessing underlying dynamical rules or phase coupling. Observing a $1/f$ slope therefore cannot determine whether bodily variability reflects an unconstrained random walk or a deterministic neuromuscular control process.

To resolve this methodological limitation, we present an experimental pipeline that evaluates body-generated motion within a **Two-Axis Framework**. We recorded primary three-axis inertial measurements from Robin, a custom gesture-augmented instrument equipped with capacitive touch sensing and an onboard accelerometer. Empirical recordings span three distinct percussion regimes: discrete impulsive strikes driven by wrist impact and viscoelastic damping, continuous locomotion flow driven by postural center-of-mass shifts, and rapid orchestral rolls dominated by high-frequency wrist oscillations ($8\sim 12\text{ Hz}$). Within our framework, **Axis 1 (Noise Color)** uses Welch power spectral density estimation subject to a sub-band stability check ($\Delta_\beta < 0.20$) to characterize macroscopic spectral decay. **Axis 2 (Nonlinear Structure)** deploys an adversarial surrogate testing engine using Iterative Amplitude Adjusted Fourier Transform (IAAFT) phase-randomized surrogates and Sample Entropy (SampEn) as the discriminating statistic.

To ensure statistical rigor and prevent false discoveries, we certified the pipeline against synthetic controls: a Mersenne Twister uniform PRNG verified resistance to Type-I false positives ($p = 0.6139$, $0/10$ rejections), while a Logistic chaotic map verified $100\%$ detection power on nonlinear dynamics ($p = 0.0198$, $10/10$ rejections). We also addressed a prevalent hardware reality in digital musical instruments: discrete 7-bit MIDI transmission over USB introduces zero-order hold (ZOH) repeated-value plateaus. By conducting native-rate decimation audits, we confirmed that empirical significance reflects true biomechanical trajectories rather than digital oversampling artifacts.

This paper delivers three primary contributions:
1. **Empirical Demonstration of Linear Metric Limitations**: We demonstrate that the single-index spectral exponent $\beta$ is mathematically ill-defined on deterministic chaos and easily confounded during rapid percussion rolls (where the spectrum flattens to $\beta \approx 0.16$), proving the methodological necessity of nonlinear surrogate testing in gestural analysis.
2. **Identification of Non-Linear Bodily Structure**: Across all spatial axes and performance regimes, Robin accelerometer streams decisively reject the linear surrogate null hypothesis ($p < 0.02$), establishing that musical motor fluctuations contain deterministic nonlinear temporal structure that cannot be reduced to linear $1/f$ noise.
3. **Controlled, Reproducible Benchmark**: We provide an open-source, fully reproducible experimental pipeline and benchmark dataset for single-case gestural dynamics, establishing an empirical foundation for gesture-aware synthesis without relying on ungrounded metaphysical assumptions.

# Section 2: Related Work and Methodological Background

### 2.1 1/f Power-Law Scaling in Human Motor Control
Scale-free fluctuations pervade human motor control. Seminal investigations by Gilden et al. [1] and Hausdorff et al. [2, 3] established that temporal intervals in rhythmic tapping, postural sway, and gait stride reliably follow $1/f^\beta$ power-law spectral scaling with $\beta \approx 1$. Historically, such pink noise was interpreted as an operational signature of self-organized criticality, distributed memory, or multi-scale neuromuscular coordination [4, 5]. 

Yet this literature relies almost exclusively on second-order linear statistics—specifically Fourier power spectral density (PSD) slope and autocorrelation functions. Linear stochastic theory proves that any Gaussian white noise passed through an appropriate infinite impulse response (IIR) linear filter precisely reproduces an empirical $1/f$ spectrum without possessing dynamical rules or state-dependent constraints [6]. Linear spectral decay cannot settle this question. A power-law slope alone cannot determine whether bodily fluctuations stem from an unconstrained linear random walk or from a nonlinear dynamical manifold.

### 2.2 Nonlinear Dynamics and Deterministic Chaos in Biomechanical Signals
Seeking to move beyond linear correlation, biomechanical researchers turned to nonlinear dynamics, searching for deterministic chaos in gait and physiological tremor [7, 8]. Standard tools include phase-space delay embedding, correlation dimension ($D_2$) [9], and the largest Lyapunov exponent ($\lambda_1$) [10]. 

In practice, computing $\lambda_1$ or $D_2$ on empirical gestures encounters severe mathematical limits. Kantz and Schreiber [11] demonstrated that estimating invariant measures reliably requires long, strictly stationary, weakly noise-corrupted sequences, typically exceeding $N > 10^4$ samples. On short records ($N \sim 10^3$) subject to anatomical damping, sensor noise, or digital quantization, dimension estimation algorithms routinely report spurious low-dimensional attractors for purely linear stochastic noise [12, 13]. Single-index chaos metrics are fragile. Applying them to musical gestures yields unverified and often irreproducible claims.

### 2.3 Surrogate Data Testing and Sample Entropy
Surrogate testing resolves this impasse. Pioneered by Theiler et al. [14] and extended by Schreiber and Schmitz [15, 16], surrogate analysis converts dynamical identification into rigorous statistical hypothesis testing without requiring asymptotic phase-space reconstruction. The Iterative Amplitude Adjusted Fourier Transform (IAAFT) generates synthetic surrogates under the null hypothesis that the observed series represents a linearly filtered Gaussian process distorted by an invertible static measurement function. By simultaneously matching the empirical power spectrum and the exact amplitude distribution, IAAFT destroys phase coupling while preserving all linear properties.

Separating empirical data from this surrogate ensemble requires an appropriate discriminating statistic. Linear spectral metrics cannot serve this role: by construction, surrogates replicate the power spectrum. Instead, Sample Entropy (SampEn) [17]—a bias-reduced formulation of Approximate Entropy [18]—quantifies the conditional probability that sub-sequences of length $m$ matching within tolerance $r$ remain similar at length $m+1$. SampEn demonstrates high stability on short, noisy records ($N \sim 10^3$) and resists baseline nonstationarity [19]. 

Surrogate testing has been utilized in cardiovascular (ECG) and neural (EEG) dynamics [20], yet its systematic deployment in Digital Musical Instruments (DMI) and percussive kinematics remains unexplored. Furthermore, existing studies rarely evaluate surrogate pipelines against simultaneous negative (PRNG) and positive (deterministic chaos) baselines, nor do they account for sensor-level oversampling plateaus. We address this gap by combining an IAAFT-SampEn architecture with synthetic controls and physical decimation audits across diverse performance regimes.

# Section 3: Methodology

### 3.1 Overview of the Two-Axis Framework
Linear spectral decay alone cannot characterize deterministic motor dynamics. To resolve this fundamental ambiguity, we formulate a decoupled **Two-Axis Framework** that separates macroscopic power-law spectral decay from an adversarial phase-randomized surrogate testing engine evaluated on three-axis inertial streams from Robin. Figure 1 illustrates the computational pipeline:

```text
                                  ┌─────────────────────────────┐
                                  │   Raw Time Series x(t)      │
                                  │  (PRNG / Chaos / Robin Acc) │
                                  └──────────────┬──────────────┘
                                                 │
                        ┌────────────────────────┴────────────────────────┐
                        ▼                                                 ▼
      ┌────────────────────────────────────┐            ┌───────────────────────────────────┐
      │   Axis 1: First-Order Spectrum     │            │   Axis 2: Higher-Order Structure  │
      │   (Welch PSD Slope beta)           │            │   (IAAFT Surrogate + SampEn)      │
      └─────────────────┬──────────────────┘            └─────────────────┬─────────────────┘
                        │                                                 │
          [Power-Law Validity Check]                    [Phase-Randomized Surrogate Engine]
          • Sub-band slope stability:                   • Impose empirical |FFT|
            Delta = |beta_hi - beta_lo| < 0.2           • Rank-remap onto sorted values
          • Stable: Report beta ± SE                    • 100 surrogates matching null
          • Unstable: Flag as Non-Power-Law                               │
                        │                               [Discriminating Statistic (SampEn)]
                        ▼                               • Calculate SampEn(real) vs Null
          [Axis 1 Classification]                       • Rank-p = 2 * min(p_low, p_high)
          • White (beta ~ 0)                                              │
          • Pink / Red (beta ~ 1-2)                                       ▼
          • Resonant / Non-power-law                     [Axis 2 Hypothesis Decision]
                                                        • p >= 0.05: Null Accepted (Linear)
                                                        • p < 0.05:  Reject Null (Nonlinear)
```

Under this architecture, **Axis 1 (Noise Color)** uses Welch power spectral density estimation strictly as a descriptive baseline to categorize macroscopic energy decay, whereas **Axis 2 (Nonlinear Structure)** deploys Iterative Amplitude Adjusted Fourier Transform (IAAFT) surrogates with Sample Entropy to detect higher-order phase coupling.

---

### 3.2 Acquisition Platform and Experimental Performance Regimes
Primary biomechanical data were acquired using **Robin**, a custom gesture-augmented digital instrument integrating eleven capacitive touch pads and an onboard three-axis accelerometer. 

#### Instrument Architecture and Quantization
The onboard microcontroller digitizes analog acceleration, maps orthogonal axes to standard 7-bit MIDI Control Change messages ($v \in [0, 127]$), and transmits them over USB. Due to microcontroller ADC polling loops, capacitive touch debouncing routines, and USB-MIDI packet buffer flush cycles, the effective transmission rate of distinct state updates is capped at approximately $f_{\text{native}} \approx 16.7\text{ Hz}$, which an acquisition engine oversamples at $f_s = 50\text{ Hz}$ ($\Delta t = 20\text{ ms}$) to produce $N = 2250$ uniformly spaced samples over $T = 45\text{ s}$.

#### Kinematic Performance Regimes
Recordings span three functional percussion regimes:
- **Trial 1: Impulsive Staccato Strikes (T1)**: Discrete, isolated percussive strikes driven by sudden wrist acceleration, impact deceleration, and viscoelastic tissue damping.
- **Trial 2: Continuous Locomotion and Dance Flow (T2)**: Continuous whole-body movement driven by macro-scale inertial trajectories and smooth, low-frequency center-of-mass shifts.
- **Trial 3: High-Frequency Percussive Rolls (T3)**: Classical orchestral snare drum roll patterns featuring rapid single- and double-stroke wrist oscillations at $8\sim 12\text{ Hz}$, governed by alternating muscular contractions and kinetic rebounds.

---

### 3.3 Axis 1: Descriptive Spectral Slope and Stability Criterion
To quantify linear frequency decay, each time series $x(t)$ is first standardized to zero mean and unit variance ($z$-score normalization):
$$z(t) = \frac{x(t) - \mu_x}{\sigma_x}$$
The power spectral density $S(f)$ is estimated via Welch's averaged periodogram method using Hann windowing, a segment length of $N_{\text{perseg}} = 256$, and $50\%$ segment overlap to suppress spectral leakage. This yields a discrete frequency resolution of:
$$\Delta f = \frac{f_s}{N_{\text{perseg}}} = \frac{50\text{ Hz}}{256} \approx 0.195\text{ Hz}$$

Assuming a power-law relationship $S(f) \propto f^{-\beta}$, the spectral exponent $\beta$ is computed via ordinary least-squares regression on log-transformed coordinates:
$$\ln S(f) = -\beta \ln f + C$$
To avoid DC bias at the lower bound and discrete quantization flattening near the Nyquist frequency ($f_{\text{Nyq}} = f_s / 2 = 25\text{ Hz}$), the fitting window is constrained to the fractional band:
$$f \in [0.03, 0.45] \times f_{\text{Nyq}} \quad (0.75\text{ Hz} \le f \le 11.25\text{ Hz})$$
which encompasses $54$ discrete frequency bins for robust linear estimation.

#### Sub-Band Stability Criterion
Linear spectral slope alone cannot certify power-law scaling. Goodness-of-fit $R^2$ coefficients are fundamentally inadequate for power-law validation, as curved or resonant spectra frequently yield high correlation coefficients despite lacking genuine scale-invariance across decades. We therefore enforce an explicit sub-band stability criterion:
1. The fitting band $[0.03, 0.45] f_{\text{Nyq}}$ is bifurcated at $0.15 f_{\text{Nyq}}$ into a low sub-band $f_{\text{lo}} \in [0.03, 0.15]$ and a high sub-band $f_{\text{hi}} \in [0.15, 0.45]$.
2. Independent linear regressions yield sub-band exponents $\beta_{\text{lo}}$ and $\beta_{\text{hi}}$.
3. The instability metric is defined as:
   $$\Delta_{\beta} = |\beta_{\text{hi}} - \beta_{\text{lo}}|$$
4. **Decision Rule**: A signal is classified as a valid scale-free power-law noise if and only if $\Delta_{\beta} < 0.20$ and standard error $\text{SE}(\beta) < 0.15$. Signals exhibiting $\Delta_{\beta} \ge 0.20$ violate scale-invariance; for such signals, single-index $\beta$ is mathematically undefined and rejected from linear classification.

---

### 3.4 Axis 2: Surrogate Testing Engine and Sample Entropy

#### Null Hypothesis Formulation
Axis 2 tests the formal null hypothesis $H_0$: *The observed time series $z(t)$ is generated by a stationary linear Gaussian stochastic process, possibly distorted by an invertible, static, nonlinear measurement function $s(t) = g(z(t))$.*

#### IAAFT Surrogate Algorithm
Axis 2 converts dynamical identification into statistical hypothesis inference. For an empirical series $z = \{z_1, \dots, z_N\}$, let $\{s_k\}$ denote its sorted values and $\{A_k = |\mathcal{F}(z)_k|\}$ its discrete Fourier amplitude spectrum. Surrogates are initialized by randomly shuffling $z$:
1. **Spectral Adjustment**: In step $i$, compute the discrete Fourier transform of the current iterate $z^{(i)}$. Replace its Fourier amplitudes with the target spectrum $A_k$ while preserving current phases $\phi_k^{(i)}$:
   $$Z_k^{(i)} = A_k \exp(j \phi_k^{(i)})$$
   Invert via IFFT to obtain intermediate continuous sequence $y^{(i)} = \mathcal{F}^{-1}(Z^{(i)})$.
2. **Amplitude Adjustment**: Rank-order the values of $y^{(i)}$ and replace them with the exact sorted empirical amplitudes $\{s_k\}$ matching those ranks.
3. Steps 1 and 2 are iterated for $200$ cycles. Convergence is reached when the surrogate simultaneously preserves the exact empirical marginal distribution and minimizes spectral divergence. For each trial, an ensemble of $N_{\text{surr}} = 100$ independent surrogates is synthesized.

#### Discriminating Statistic: Sample Entropy
Sample Entropy acts as the nonlinear discriminating statistic. Because surrogates preserve power spectra by design, Sample Entropy reliably discriminates deterministic phase coupling from linearly filtered Gaussian noise.

Given standardized sequence $z$, embedded vectors of dimension $m$ are defined as:
$$\mathbf{u}_m(i) = [z_i, z_{i+1}, \dots, z_{i+m-1}]$$
The distance between vectors is defined by the Chebyshev norm:
$$d[\mathbf{u}_m(i), \mathbf{u}_m(j)] = \max_{0 \le k \le m-1} |z_{i+k} - z_{j+k}|$$
Let $B_i^m(r)$ denote $(N - m)^{-1}$ times the number of vectors $\mathbf{u}_m(j)$ ($j \ne i$) such that $d[\mathbf{u}_m(i), \mathbf{u}_m(j)] \le r$, and let $B^m(r) = (N - m)^{-1} \sum_{i=1}^{N-m} B_i^m(r)$. Similarly, let $A^m(r)$ denote the matching probability for dimension $m+1$. Sample entropy is defined as:
$$\text{SampEn}(m, r, N) = -\ln \left[ \frac{A^m(r)}{B^m(r)} \right]$$
In our pipeline, parameters are fixed to standard physiological settings: embedding dimension $m = 2$ and tolerance threshold $r = 0.20 \times \sigma_z = 0.20$.

#### Non-Parametric Rank Inference
The rank test possesses an exact mathematical floor. Let $\theta_{\text{real}} = \text{SampEn}(z)$ and let $\{\theta_{\text{surr}}^{(j)}\}_{j=1}^{N_{\text{surr}}}$ denote the surrogate ensemble statistics. The empirical two-sided $p$-value is calculated directly from the rank:
$$p_{\text{low}} = \frac{\sum_{j=1}^{N_{\text{surr}}} \mathbb{I}(\theta_{\text{surr}}^{(j)} \le \theta_{\text{real}}) + 1}{N_{\text{surr}} + 1}, \quad p_{\text{high}} = \frac{\sum_{j=1}^{N_{\text{surr}}} \mathbb{I}(\theta_{\text{surr}}^{(j)} \ge \theta_{\text{real}}) + 1}{N_{\text{surr}} + 1}$$
$$p = 2 \times \min(p_{\text{low}}, p_{\text{high}})$$
With $N_{\text{surr}} = 100$, when $\theta_{\text{real}}$ falls strictly outside the surrogate distribution ($k = 0$), the exact theoretical floor of the test is reached:
$$p_{\text{floor}} = 2 \times \frac{0 + 1}{100 + 1} = \frac{2}{101} \approx 0.0198$$
At significance level $\alpha = 0.05$, $p < 0.05$ rejects $H_0$, indicating the presence of nonlinear temporal structure.

---

### 3.5 Benchmark Controls and Decimation Audit Protocol

#### Synthetic Controls
To guard against methodological bias, the pipeline is certified against two synthetic benchmarks ($N = 2048$, 10 random seeds):
- **Negative Control**: Uniform pseudorandom noise generated by the Mersenne Twister (MT19937). Must yield $p > 0.05$ (null accepted).
- **Positive Control**: The deterministic chaotic Logistic Map operating in the fully developed chaotic regime:
  $$x_{k+1} = 3.9 \, x_k (1 - x_k)$$
  Burn-in transient of $1000$ iterations discarded. Must yield $p < 0.05$ (null rejected).

#### Anti-Aliasing Decimation Protocol
Decimation audits safeguard against digital sensor artifacts. Because Robin's USB-MIDI transmission operates event-synchronously at approximately $\approx 16.7\text{ Hz}$, uniform oversampling at $50\text{ Hz}$ introduces zero-order hold (ZOH) repeated-value plateaus. To prevent artificial inflation of template matches, every dataset is subjected to a decimation audit:
$$z_{\text{dec}}(k) = z(3k), \quad k = 1, 2, \dots, \lfloor N/3 \rfloor$$
Downsampling the recording by a factor of 3 to its true native sensor transmission rate of $\approx 16.67\text{ Hz}$ ($N_{\text{dec}} = 750$) completely eliminates adjacent identical samples. Significance must persist under decimation for any kinematic finding to be certified.

# Section 4: Experiments and Empirical Results

### 4.1 Methodological Motivation: The Failure of Linear $\beta$ on Nonlinear Dynamics
Classical power spectral density (PSD) slope fitting cannot reliably classify deterministic dynamics. Using our synthetic benchmark protocol ($N = 2048$, 10 independent random seeds), we evaluated the sub-band stability of $\beta$ across pure uniform pseudorandom noise (PRNG), a synthetic linear $1/f$ Gaussian noise proxy, and the deterministic chaotic Logistic Map ($r=3.9$). 

| Synthetic Signal | Role | Full-Band $\beta \pm \text{SE}$ | Low Sub-Band $\beta_{\text{lo}}$ | High Sub-Band $\beta_{\text{hi}}$ | Instability $\Delta_{\beta}$ | Decision |
|---|---|---|---|---|---|---|
| **PRNG (MT19937)** | Negative Control | $-0.06 \pm 0.04$ | $-0.03 \pm 0.08$ | $-0.16 \pm 0.07$ | $0.13 \pm 0.06$ | **Power-Law Valid (White)** |
| **Synthetic $1/f$ Proxy** | Verification Proxy | $1.00 \pm 0.05$ | $0.95 \pm 0.09$ | $1.10 \pm 0.08$ | $0.15 \pm 0.07$ | **Power-Law Valid (Pink)** |
| **Logistic Chaos ($r=3.9$)** | Positive Control | $-1.30 \pm 0.10$ | $-0.10 \pm 0.12$ | $-2.65 \pm 0.11$ | $\mathbf{2.54 \pm 0.14}$ | $\mathbf{Undefined\ (Non\text{-}Power\text{-}Law)}$ |

While PRNG and linear $1/f$ noise exhibit stable sub-band slopes ($\Delta_\beta < 0.15$), the Logistic Map yields a massive sub-band discrepancy: $\beta_{\text{lo}} \approx -0.10$ in the low frequencies while plunging to $\beta_{\text{hi}} \approx -2.65$ in the high frequencies ($\Delta_\beta = 2.54$). A forced single regression across the full band yields an arbitrary slope of $\approx -1.30$. Single-index spectral slopes are mathematically ill-defined on deterministic chaos. Spectral slope cannot serve as an unverified single axis for dynamical classification.

---

### 4.2 Synthetic Control Verification
Synthetic control certification confirmed $100\%$ test specificity and sensitivity across $10$ independent random seeds ($N_{\text{surr}} = 100$, $\alpha = 0.05$):
- **Negative Control (PRNG)**: Across all 10 seeds, the empirical Sample Entropy of the PRNG sequence ($\text{SampEn} = 2.187 \pm 0.012$) fell squarely within its surrogate distribution ($\text{SampEn}_{\text{surr}} = 2.178 \pm 0.017$), yielding a median two-sided $p$-value of $p = 0.6139$ with $0/10$ rejections. Type-I false alarms are successfully prevented.
- **Positive Control (Logistic Chaos)**: Across all 10 seeds, the empirical Sample Entropy of the chaotic sequence ($\text{SampEn} = 0.518 \pm 0.008$) was severely depressed relative to its phase-randomized surrogate ensemble ($\text{SampEn}_{\text{surr}} = 1.569 \pm 0.022$). In $10/10$ trials, every surrogate exhibited higher entropy than the real sequence ($k = 0$), rejecting the null at the exact theoretical minimum $p = 0.0198$. Detection power reaches $100\%$ on deterministic phase coupling.

---

### 4.3 Main Empirical Findings Across Percussive Performance Regimes
We now evaluate the empirical accelerometer streams captured from Robin across the three percussive performance regimes. Table 1 summarizes the spectral properties, linear autocorrelation, Shannon information entropy, and Axis 2 surrogate statistics across all three orthogonal axes. Figure 1 illustrates the empirical power spectra alongside their corresponding surrogate distributions.

![Figure 1: Main empirical evidence across controls and percussive regimes. Top row: Welch power spectral density curves with log-log regression fits. Bottom row: Empirical Sample Entropy values (red dashed lines) against 100-surrogate IAAFT null distributions (blue bars).](figures/fig1_main_evidence.png){#fig:fig1 width=100%}

**Table 1: Comparative Spectral and Nonlinear Dynamics Across Baselines and Performance Regimes**

| Signal / Performance Regime | Role | $N$ | ACF(1) | Shannon (bits) | Axis 1: PSD $\beta$ | SampEn (Real) | SampEn (IAAFT Null) | Rank-$p$ | Decision |
|---|---|---|---|---|---|---|---|---|---|
| **PRNG (MT19937)** | Negative Control | 2048 | 0.001 | 4.90 | $-0.06 \pm 0.04$ | 2.187 | $2.178 \pm 0.017$ | 0.6139 | Null Accepted |
| **Logistic Chaos ($r=3.9$)** | Positive Control | 2048 | -0.514 | 4.62 | Undefined (Non-PL) | 0.518 | $1.569 \pm 0.022$ | 0.0198* | Reject Null |
| **Robin T1: Impulsive Strike ($X$)** | Empirical Kin. | 2250 | 0.900 | 4.52 | $1.55 \pm 0.13$ | 0.497 | $1.279 \pm 0.021$ | 0.0198* | Reject Null |
| **Robin T1: Impulsive Strike ($Y$)** | Empirical Kin. | 2250 | 0.877 | 4.73 | $1.90 \pm 0.12$ | 0.528 | $1.238 \pm 0.031$ | 0.0198* | Reject Null |
| **Robin T1: Impulsive Strike ($Z$)** | Empirical Kin. | 2250 | 0.881 | 4.55 | $1.47 \pm 0.12$ | 0.499 | $1.318 \pm 0.021$ | 0.0198* | Reject Null |
| **Robin T2: Locomotion Flow ($X$)** | Empirical Kin. | 2250 | 0.986 | 4.77 | $2.13 \pm 0.07$ | 0.311 | $0.505 \pm 0.023$ | 0.0198* | Reject Null |
| **Robin T2: Locomotion Flow ($Y$)** | Empirical Kin. | 2250 | 0.949 | 4.38 | $2.25 \pm 0.09$ | 0.337 | $0.777 \pm 0.032$ | 0.0198* | Reject Null |
| **Robin T2: Locomotion Flow ($Z$)** | Empirical Kin. | 2250 | 0.948 | 4.41 | $1.79 \pm 0.09$ | 0.423 | $1.039 \pm 0.033$ | 0.0198* | Reject Null |
| **Robin T3: Rapid Roll ($X$)** | Empirical Kin. | 2250 | 0.659 | 3.69 | $0.16$ (Resonant) | 0.718 | $1.807 \pm 0.025$ | 0.0198* | Reject Null |
| **Robin T3: Rapid Roll ($Y$)** | Empirical Kin. | 2250 | 0.798 | 4.11 | $0.21$ (Resonant) | 0.700 | $1.468 \pm 0.040$ | 0.0198* | Reject Null |
| **Robin T3: Rapid Roll ($Z$)** | Empirical Kin. | 2250 | 0.688 | 3.42 | $0.51$ (Resonant) | 0.548 | $1.135 \pm 0.025$ | 0.0198* | Reject Null |

*\*Note: $p = 0.0198$ represents the exact rank-test floor for $N_{\text{surr}} = 100$ calculated as $p_{\text{floor}} = 2 \times (0 + 1) / (N_{\text{surr}} + 1) = 2 / 101 \approx 0.0198$, indicating that empirical SampEn was strictly lower than all 100 surrogates.*

#### Axis 1 Analysis (Spectral Signatures)
1. **Regime T1 (Impulsive Strikes)**: Displays typical colored noise characteristics with spectral slopes ranging from $\beta = 1.47 \pm 0.12$ ($Z$-axis) to $\beta = 1.90 \pm 0.12$ ($Y$-axis). High autocorrelation ($\text{ACF}(1) \approx 0.88$) and red-shifted spectra reflect the rapid rise and viscoelastic damping of human limb impacts.
2. **Regime T2 (Locomotion Flow)**: Displays steep power-law decay, with $\beta$ reaching $2.13 \sim 2.25$ and lag-1 autocorrelation reaching $0.986$. Low-frequency gravitational orientation changes and bodily inertia dominate the energy spectrum.
3. **Regime T3 (Rapid Percussive Rolls)**: Breaks the colored noise pattern entirely: the apparent spectral slope collapses to $\beta = 0.16 \pm 0.12$ ($X$-axis) and $\beta = 0.21 \pm 0.11$ ($Y$-axis). High-frequency wrist articulation at $8\sim 12\text{ Hz}$ injects concentrated kinetic energy into the upper band, flattening the overall spectral profile.

#### Axis 2 Analysis (Universal Nonlinearity)
In contrast to the divergent spectral slopes of Axis 1, **Axis 2 reveals universal, invariant dynamical structure across all three regimes and all three spatial axes**:
- For every empirical series, empirical Sample Entropy is substantially lower than that of the phase-randomized surrogate ensemble (e.g., T1 $X$-axis: $\text{SampEn}_{\text{real}} = 0.497$ vs. $\text{SampEn}_{\text{surr}} = 1.279 \pm 0.021$).
- In all 9 tests (3 regimes $\times$ 3 axes), the real series ranked strictly lower than all 100 surrogates ($k = 0$), yielding identical floor $p$-values of $p = 0.0198 < 0.05$.
- Even when constrained to identical Fourier power spectra and marginal value histograms, linear stochastic processes fail to replicate the low entropy of human motor variability. Bodily fluctuations during expressive performance are governed by low-dimensional deterministic constraints rather than linear stochastic drift.

---

### 4.4 The Trial 3 Paradox: Resolving Linear Deception
Regime T3 provides the most compelling justification for the Two-Axis Framework. A superficial linear inspection would suggest that rapid rolls degenerate into uncorrelated white noise based on their flat spectral exponent of $\beta = 0.16$ and reduced autocorrelation ($\text{ACF}(1) = 0.659$). 

Sub-band analysis refutes this interpretation. The low sub-band exhibits $\beta_{\text{lo}} \approx 0.16$, whereas the high sub-band exhibits $\beta_{\text{hi}} \approx 1.05$, producing an instability of $\Delta_{\beta} = 0.89$ that explicitly violates scale-invariance. The signal is not scale-free white noise, but a narrowband oscillatory motor trajectory driven by high-frequency neuromuscular entrainment at $8\sim 12\text{ Hz}$.

Axis 2 unmasks this underlying deterministic structure. Because phase randomization destroys the precise timing required to sustain rapid wrist alternations, empirical entropy ($\text{SampEn} = 0.718$) is decisively separated from surrogate distributions ($\text{SampEn}_{\text{surr}} = 1.807 \pm 0.025$, $p = 0.0198$). Linear spectral metrics confound high-frequency deterministic articulation with random noise, whereas surrogate testing reliably detects underlying motor structure regardless of spectral tilt.

---

### 4.5 Robustness Verification: Eliminating Zero-Order Hold Artifacts
Because Robin transmits MIDI CC over USB event-synchronously at $\approx 16.7\text{ Hz}$, uniform oversampling at $50\text{ Hz}$ resulted in approximately $70.5\%$ of adjacent samples sharing identical integer values. In an adversarial test, we verified that a pure linear Gaussian $1/f$ noise process subject to $70\%$ zero-order hold (ZOH) plateaus was falsely rejected by surrogate testing ($p = 0.0198$). ZOH plateaus artificially depress empirical Sample Entropy while surrogates scatter identical values across the sequence.

To verify that our empirical findings are not an artifact of ZOH plateaus, all empirical datasets were subjected to a factor-of-3 decimation audit ($N_{\text{dec}} = 750$, $f_{\text{native}} \approx 16.67\text{ Hz}$), completely eliminating adjacent repeated values.

**Table 2: Decimation Robustness Audit at Native Transmission Rate ($f_{\text{native}} \approx 16.67\text{ Hz}$, $N_{\text{dec}} = 750$, Zero Repeated Plateaus)**

| Condition / Axis | Raw ZOH Repeats (%) | Decimated $N$ | SampEn (Observed) | SampEn (Surr Mean $\pm$ SD) | Rank-$p$ | Verdict |
|---|---|---|---|---|---|---|
| **Linear $1/f$ Simulation** | 68.1% (ZOH) $\to$ 0.0% | 750 | 1.565 | $1.507 \pm 0.038$ | 0.2178 | Null Accepted (Correct) |
| **Robin T1 Strike ($X$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 1.045 | $1.793 \pm 0.033$ | 0.0198* | Reject Null (Robust) |
| **Robin T1 Strike ($Y$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 1.086 | $1.769 \pm 0.041$ | 0.0198* | Reject Null (Robust) |
| **Robin T1 Strike ($Z$)** | 70.3% (ZOH) $\to$ 0.0% | 750 | 0.972 | $1.846 \pm 0.051$ | 0.0198* | Reject Null (Robust) |
| **Robin T2 Flow ($X$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.690 | $0.868 \pm 0.046$ | 0.0198* | Reject Null (Robust) |
| **Robin T2 Flow ($Y$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.682 | $1.242 \pm 0.041$ | 0.0198* | Reject Null (Robust) |
| **Robin T2 Flow ($Z$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 0.889 | $1.550 \pm 0.048$ | 0.0198* | Reject Null (Robust) |
| **Robin T3 Roll ($X$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.160 | $2.138 \pm 0.056$ | 0.0198* | Reject Null (Robust) |
| **Robin T3 Roll ($Y$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.210 | $1.772 \pm 0.050$ | 0.0198* | Reject Null (Robust) |
| **Robin T3 Roll ($Z$)** | 70.7% (ZOH) $\to$ 0.0% | 750 | 1.206 | $1.480 \pm 0.034$ | 0.0198* | Reject Null (Robust) |

Decimation audits definitively rule out zero-order hold artifacts:
1. Under 3x decimation, synthetic linear noise accepts the null hypothesis ($p = 0.2178$), proving that decimation eliminates spurious plateau-induced significance.
2. Under identical decimation, all nine empirical channels across all three performance regimes continue to reject the linear null hypothesis decisively at $p = 0.0198 < 0.05$.

The observed nonlinear structure represents genuine continuous biomechanics rather than sensor transmission artifacts.

# Section 5: Discussion

### 5.1 Re-Evaluating Contingency in Digital Musical Interaction
Conventional digital instrument design treats human motor variability as sensor noise to be suppressed through low-pass filtering, Kalman estimation, or dead-band thresholds. This filtering comes at a cost. While it stabilizes control parameters, it strips performances of micro-temporal expressivity, yielding synthetic sound outputs that feel disconnected from physical embodiment.

Our findings demand a fundamental re-evaluation of gestural contingency. Rejecting linear stochastic null models across all performance regimes ($p < 0.02$) proves that bodily fluctuations are not Gaussian noise. They represent the observable signature of low-dimensional motor dynamics—the continuous interplay of viscoelastic muscle tone, kinetic inertia, and closed-loop proprioception. Performers do not inject random perturbations into a deterministic machine. Coupling occurs between two deterministic systems. Across an embodied physical interface, deviations from nominal trajectories manifest physical negotiation with material resistance.

---

### 5.2 Aesthetic Analogy: The "Free Will" Question and System Negotiation
Agency emerges from physical resistance. Popular commentary often romanticizes human agency as an intermediate zone between strict determinism and pure randomness. We emphasize that our statistical findings do not constitute empirical proof of metaphysical free will. Equating mathematical rejection of linear stochasticity with conscious volitional agency would render the hypothesis scientifically unfalsifiable.

We retain free will strictly as an aesthetic and interpretive analogy. Human agency is experienced precisely where algorithmic predictability falters: we build computational systems to control uncertainty, yet those systems inevitably generate new forms of physical expression. Live performance feels alive. It feels distinct from pseudo-random algorithmic jitter because bodily fluctuations embody physical history, neuromuscular inertia, and deterministic phase coupling that algorithms cannot replicate through uncorrelated noise.

---

### 5.3 Implications for Gesture Mapping and Music Therapy
The Two-Axis Framework opens practical avenues in instrument mapping and clinical motor therapy. Current digital instruments rely on rigid one-to-one parameter scaling. By computing streaming Sample Entropy alongside spectral slope in real time, future instruments could dynamically infer articulatory regimes, differentiating intentional high-speed rolls from involuntary resting tremor.

In clinical therapy—including Parkinson's disease, autism spectrum disorder, and stroke rehabilitation—the framework provides an objective biomarker, tracking whether motor trajectories organize into low-dimensional manifolds or degenerate into noise. Clinicians currently lack lightweight, non-invasive metrics to assess motor organization; tracking entropy collapse relative to linear surrogates establishes a computational diagnostic for therapeutic progress.

---

### 5.4 Limitations of the Study
Several limitations bound this study:
1. **Exploratory Single-Case Investigation**: Conducted using primary sensor data recorded by a single skilled percussionist on Robin, our findings establish internal validity rather than population-wide kinematic norms across skill levels.
2. **Sensor Quantization and Bandwidth**: Robin quantizes continuous acceleration into 7-bit MIDI Control Change values. While native-rate decimation audits confirm that statistical significance persists independent of zero-order hold artifacts, higher-resolution 16-bit industrial sensors operating at kilohertz rates would enable multi-scale Lyapunov exponent estimation. Sensor drift was detrended, though ultra-low frequency DC fluctuations remain constrained by the 45-second epoch.
3. **Task Constraints**: All performance regimes were evaluated in handheld percussive contexts; unconstrained whole-body performance may exhibit distinct spatial couplings.

# Section 6: Conclusion

### 6.1 Summary of Contributions
This paper resolved the ambiguity surrounding bodily motor fluctuations in digital musical performance. By introducing a decoupled Two-Axis Framework, we separated first-order spectral decay from adversarial hypothesis testing via IAAFT phase-randomized surrogates and Sample Entropy. 

Rigorous controls certified the pipeline. A Mersenne Twister PRNG confirmed resistance to Type-I false positives ($p = 0.6139$), whereas chaotic Logistic dynamics demonstrated $100\%$ detection sensitivity ($p = 0.0198$). Across three distinct percussion regimes and all spatial axes, physical gestures decisively rejected linear stochastic null models at the theoretical test floor ($p < 0.02$). The Trial 3 paradox captures the necessity of this decoupling: rapid rolls flatten linear Fourier spectra to near-white levels ($\beta \approx 0.16$), deceiving classical spectral indicators, yet Axis 2 unmasks their deterministic neuromuscular coordination. Native-rate decimation audits further confirmed that this significance persists independent of digital zero-order hold artifacts. Bodily variability is not noise. It embodies deterministic nonlinear manifolds negotiating physical resistance.

---

### 6.2 Future Outlook
Future research will extend this architecture along two fronts: first, multi-subject clinical cohorts will establish normative complexity baselines across healthy percussionists and motor-impaired individuals undergoing rehabilitation; second, embedding streaming surrogate estimators directly onto edge DSP architectures will enable adaptive, nuance-responsive musical synthesis that mirrors the dynamic depth of the human body.

# References

- [1] D. L. Gilden, T. Thornton, and M. W. Mallon, "1/f noise in human cognition," *Science*, vol. 267, no. 5205, pp. 1837–1839, 1995.
- [2] J. M. Hausdorff et al., "Is walking a random walk? Evidence for long-range correlated fluctuations in stride interval of human gait," *J. Appl. Physiol.*, vol. 78, no. 1, pp. 349–358, 1995.
- [3] J. M. Hausdorff, "Gait dynamics in Parkinson's disease: common and distinct features," *Eur. J. Neurol.*, vol. 16, pp. 6–16, 2009.
- [4] P. J. Treffner and J. A. S. Kelso, "Dynamic encounters: Long memory in human coordination," *Phys. Rev. E*, vol. 60, no. 5, p. 5681, 1999.
- [5] B. J. West, "Fractal physiology and the mind-body problem," *Ann. N. Y. Acad. Sci.*, vol. 1198, pp. 264–276, 2010.
- [6] M. S. Taqqu, V. Teverovsky, and W. Willinger, "Estimators for long-range dependence: an empirical study," *Fractals*, vol. 3, no. 04, pp. 785–798, 1995.
- [7] J. B. Dingwell and J. P. Cusumano, "Nonlinear time series analysis of normal and pathological human walking," *Chaos*, vol. 10, no. 4, pp. 848–863, 2000.
- [8] H. D. I. Abarbanel, *Analysis of Observed Chaotic Data*. Springer, 1996.
- [9] P. Grassberger and I. Procaccia, "Characterization of strange attractors," *Phys. Rev. Lett.*, vol. 50, no. 5, p. 346, 1983.
- [10] M. T. Rosenstein, J. J. Collins, and C. J. De Luca, "A practical method for calculating largest Lyapunov exponents from small data sets," *Physica D*, vol. 65, no. 1-2, pp. 117–134, 1993.
- [11] H. Kantz and T. Schreiber, *Nonlinear Time Series Analysis*, 2nd ed. Cambridge University Press, 2004.
- [12] P. E. Rapp et al., "Filtered noise can mimic low-dimensional chaotic attractors," *Phys. Rev. E*, vol. 47, no. 4, p. 2289, 1993.
- [13] J. Theiler, "Some comments on the correlation dimension of 1/f^\alpha noise," *Phys. Lett. A*, vol. 159, no. 8-9, pp. 480–493, 1991.
- [14] J. Theiler et al., "Testing for nonlinearity in time series: the method of surrogate data," *Physica D*, vol. 58, no. 1-4, pp. 77–94, 1992.
- [15] T. Schreiber and A. Schmitz, "Improved surrogate data for nonlinearity tests," *Phys. Rev. Lett.*, vol. 77, no. 4, pp. 635–638, 1996.
- [16] T. Schreiber and A. Schmitz, "Surrogate time series," *Physica D*, vol. 142, no. 3-4, pp. 346–382, 2000.
- [17] J. S. Richman and J. R. Moorman, "Physiological time-series analysis using approximate entropy and sample entropy," *Am. J. Physiol. Heart Circ. Physiol.*, vol. 278, no. 6, pp. H2039–H2049, 2000.
- [18] S. M. Pincus, "Approximate entropy as a measure of system complexity," *Proc. Natl. Acad. Sci. U.S.A.*, vol. 88, no. 6, pp. 2297–2301, 1991.
- [19] M. Costa, A. L. Goldberger, and C.-K. Peng, "Multiscale entropy analysis of complex physiologic time series," *Phys. Rev. Lett.*, vol. 89, no. 6, p. 068102, 2002.
- [20] D. Kugiumtzis, "Surrogate data test for nonlinearity including monotonic transformations," *Phys. Rev. E*, vol. 62, no. 1, p. R25, 2000.

# What We Learned Building a Cat Qubit — and a Small Experiment in Preparing One

**Introductory Track, Task 1 — DuQuantum 2026 (Alice & Bob challenge).**
*Draft 2. Full derivations and tables are in `Task_1.0.md` – `Task_1.3.md`. All figures referenced are in `figures/`.*

## The story in one paragraph
A cat qubit stores information in which of two "valleys" a microwave oscillator sits in: the states $|+\alpha\rangle$ ($|0_L\rangle$) and $|-\alpha\rangle$ ($|1_L\rangle$), with $\alpha=2.1$. We first built up the physics one ingredient at a time (Tasks 1.0–1.2) and found something surprising: switching on the stabilization from an empty oscillator **never** lands in one valley. It always produces a "both valleys at once" cat state, which then slowly loses its quantum coherence. Our small experiment (Task 1.3) asks how to land in one valley on purpose. The answer is simple: **give the oscillator a short kick toward $+\alpha$ before switching the stabilization on (protocol A).** Almost everything we learned in the earlier tasks explains why this works so well.

---

## Part 1 — What the warm-ups taught us (Tasks 1.0–1.2)

**A few words we use throughout.**
- The **memory** is the oscillator that stores the qubit. The **buffer** is a deliberately leaky helper mode used to stabilize it.
- A **coherent state** $|\alpha\rangle$ is the most "classical" state of an oscillator: a definite amplitude and phase, plus the minimum quantum fuzz. In the phase-space pictures (Wigner plots) it is a round blob at position $\alpha$.
- A **cat state** is two blobs at $\pm\alpha$ *at the same time*, with stripes ("fringes") between them that show the two are in superposition.
- **Parity** asks whether the number of photons is even (+1) or odd (−1).
- **Fidelity** (0 to 1) measures how close the state is to the one we wanted.

### 1.0(a) Losing photons one at a time doesn't damage a coherent state
**What we did.**
- Started the memory in a coherent state with amplitude 2, i.e. 4 photons on average.
- Let it oscillate at 1 MHz, so the blob goes around phase space once per µs.
- Let it leak single photons at an (exaggerated) rate $\kappa/2\pi=1$ MHz, for 1 µs.

**What we saw.**
- The blob **spirals clockwise into the origin**, completing exactly one turn in 1 µs.
- The photon number falls from 4 to 0.0075, following the textbook $4e^{-\kappa t}$ curve to within $10^{-8}$.
- Throughout, the blob stays **round and pure**: purity never drops below 0.999996.
- At every moment the state is the coherent state that theory predicts (fidelity > 0.999998).
- With leakage switched off, it circles at a constant 4 photons.

**Why.**
- A coherent state behaves like a pendulum with friction: the rotation is the oscillation and the shrinking is the friction.
- The special feature of a coherent state is that *removing one photon leaves it a coherent state*. Mathematically, $a|\alpha\rangle=\alpha|\alpha\rangle$. So loss can only make it smaller, never distort it.

**How we know it's right.** Getting one turn per µs and a factor $e^{-2\pi}$ decay at 1 µs is a direct check of our units. Forgetting a $2\pi$ would make both six times slower.
*Figures: `1.0a_photon_number_and_trajectory.png`, `1.0a_wigner.png`.*

> **Lesson 1:** single-photon loss shrinks a coherent state but never flips it or smears it out.

### 1.0(b) Losing photons in pairs remembers whether the photon number was even or odd
**What we did.** Same starting state, but now the only process is losing photons **two at a time**, at the rate $\kappa_2$ that the real device provides. No rotation, no drive.

**What we saw.**
- The photon number drops very fast at first: 4 → 2.2 in 20 ns → 0.90 in 100 ns.
- Then it **stalls**: 0.50 at 500 ns, and **0.4998** from 1 µs onward.
- At the end the memory is 50.02% in the empty state $|0\rangle$ and 49.98% in the one-photon state $|1\rangle$.

**Why.**
- Pair loss can't act on $|0\rangle$ or $|1\rangle$: you can't remove two photons from fewer than two. Those are the only "untouched" states.
- Only **9%** of the initial state was in them, so the plateau is *not* "what was left alone".
- The real reason: pair loss **never changes whether the photon number is even or odd**. Every even component slides down to $|0\rangle$ and every odd component to $|1\rangle$. A coherent state with 4 photons is almost exactly half even and half odd, hence the 0.4998.
- The fast-then-slow shape comes from the pair-loss rate growing like the photon number *squared*, so it collapses as photons disappear.

**How we know it's right.**
- The parity-based prediction, $(1-e^{-8})/2=0.499832$, matches the simulation to six digits.
- The measured parity is unchanged from start to finish.

*(Honest note: our first guess was that the plateau would be the 9% "untouched" part. The simulation proved us wrong and pointed us to parity.)*
*Figure: `1.0b_two_photon_loss.png`.*

> **Lesson 2:** pair processes cannot change parity. Whatever parity you start with, you keep.

### 1.0(c) Pair loss plus pair drive creates two stable valleys
**What we did.**
- Combined pair loss with a pair *drive*, using the simplified one-mode model of the stabilization, and kept the weak single-photon leak.
- Started from vacuum and ran for 2 µs.
- Repeated with the stabilization 4× weaker and 4× stronger.

**What we saw.**

| Stabilization strength | 4× weaker | nominal | 4× stronger |
|---|---|---|---|
| time to reach 90% of final photon number | 610 ns | 155 ns | 40 ns |
| final photon number | 4.387 | 4.404 | 4.408 |

- The photon number rises **smoothly, with no overshoot**, to about 4.40. That is very close to the target $\alpha^2=4.41$.
- The Wigner picture shows the vacuum blob stretching sideways and **splitting into two blobs at ±2.1, with fringes between them**: a cat.

**Why.**
- The stabilization has two **resting points**, $|+\alpha\rangle$ and $|-\alpha\rangle$. Any state there is left alone, and every other state is pushed toward them, like two valleys.
- The strength only sets *how fast* the state rolls in, not *where* it ends up. With the leak switched off, the three curves become **identical** (to $10^{-6}$) once time is rescaled by the strength. The leak adds a tiny correction: it pulls the resting point slightly inward, more so when the stabilization is weak, which explains the small spread in the final values.

**Foreshadowing.** The parity has already dropped to about 0.37 after 2 µs. Something is slowly eroding the cat; see 1.3(a).
*Figures: `1.0c_stabilization_kappa2_scan.png`, `1.0c_wigner.png`.*

> **Lesson 3:** $|+\alpha\rangle$ and $|-\alpha\rangle$ are *resting points*. A state already sitting on one has nothing to correct.

### 1.1 The real two-mode device is wilder than the simple model
**What we did.** Simulated the actual mechanism, still starting from vacuum in both modes, for 4 µs:
- two memory photons convert into one buffer photon and back;
- the buffer leaks quickly;
- the buffer is driven.

**What we saw.**
- **The buffer fills first**, peaking at about **2 photons** at 130 ns.
- **The memory then overshoots** to **6.9 photons** at 230 ns, 56% more than the target.
- The memory **rings** with a period of about 0.32 µs. It is within 1% of its final value after about **1.06 µs**.
- The final memory photon number is 4.403, the same as the simple model. The buffer drains to almost nothing ($2\times10^{-5}$ photons).
- In the Wigner snapshots:
  - the blob is first **squeezed** into a thin horizontal streak;
  - it then splits into two lobes, which swing *past* ±2.1 during the overshoot and come back;
  - the fringes between the lobes slowly fade between 1 µs and 4 µs.

**Why.**
- The simple model assumes the buffer drains instantly, like a bucket with a huge hole. For these device parameters the hole isn't big enough: the buffer leak rate is only about 3× the coupling and *smaller* than the drive.
- So photons pile up in the buffer, pour into the memory faster than they can escape, and **slosh back and forth** before settling.
- A small analytic model of this sloshing predicts the ringing period (0.319 µs vs 0.316 µs measured) with no fitting.

**How we know it's right.**
- We tracked how much population reached the highest simulated levels. It stayed at about $10^{-3}$ or below.
- With the README's suggested buffer size, 4.6% of the population piled up in its top level, which was a warning sign (see 1.2).

*Figures: `1.1_photon_numbers_truncation.png`, `1.1_vs_effective.png`, `1.1_wigner_mosaic.png`.*

> **Lesson 4:** inflating a cat from vacuum is a violent transient: overshoot, ringing, a busy buffer. The simple model gets the destination right but the journey wrong.

### 1.2 The steady state is easy to simulate; the transient is not
**What we did.**
- Compared the final photon number with a back-of-the-envelope (semiclassical) prediction.
- Ran a **convergence study**: repeated the 1.1 simulation while changing how many energy levels we keep for the memory (16–32) and for the buffer (4–14).

**What we saw.**
- **Final photon number.**
  - Prediction ignoring photon loss: 4.410.
  - Prediction including it: 4.405.
  - Simulation: **4.403**.
  - The small deficit is real physics (the leak pulls the valleys inward), not a numerical error.
- **Convergence.**

| Buffer levels kept | 4 | 6 (README default) | 8 | 10 | 12 | 14 |
|---|---|---|---|---|---|---|
| peak memory photons | 4.46 (**no overshoot!**) | 6.11 (−11%) | 6.74 (−2%) | 6.87 | 6.88 | 6.88 |
| final memory photons | 4.403 | 4.403 | 4.403 | 4.403 | 4.403 | 4.403 |

| Memory levels kept | 16 | 20 | 24 | 32 |
|---|---|---|---|---|
| peak memory photons | 6.37 (−8%) | 6.80 (−1.4%) | 6.88 | 6.89 |
| final memory photons | 4.406 | 4.403 | 4.403 | 4.403 |

**Why.**
- The **final state is calm**: about 4.4 photons in the memory and an empty buffer, so even small simulations describe it perfectly. It is also an *attractor*: even when a too-small simulation distorts the journey, the state still ends up in the same place.
- The **overshoot is not calm**. The buffer briefly holds about 2 photons with a broad spread, and the memory about 7. Cutting off too few buffer levels clips exactly the photons that cause the overshoot.
- With only 4 buffer levels the overshoot vanishes, and the result *looks like the simple model*. A badly truncated simulation could have convinced us the simple model was fine.

**What we chose.** 24 memory levels and 12 buffer levels, converged for both the final value and the peak.
*Figure: `1.2_convergence.png`.*

> **Lesson 5:** the calm, low-photon parts of the dynamics are cheap and safe to simulate. The violent parts are where errors hide. Always check convergence on the quantity you actually report.

### 1.3(a) What inflation from vacuum actually makes
**What we did.** Took the 1.1 simulation and asked which state it actually produces, tracking:
- the parity;
- the average amplitude $\langle a\rangle$ (does it prefer a side?);
- the fidelity with the even cat $|\mathcal C_+\rangle$ and with $|+\alpha\rangle$;
- the purity.

**What we saw.**
- $\langle a\rangle$ is **exactly zero** the whole time, so the state never picks a side. Its fidelity with $|+\alpha\rangle$ settles at 0.50.
- **The parity** starts at +1 and decays: 0.96 at 0.2 µs, 0.63 at 1 µs, 0.13 at 4 µs, with a time constant of **1.95 µs** (theory: 1.94 µs).
- It is a true superposition, not a "one or the other" mixture:
  - a mixture of the two blobs would have parity ≈ 0.0001, versus 0.63 here at 1 µs;
  - the Wigner function has fringes with negative values;
  - the value at the centre equals the parity, exactly as theory says.
- **The best fidelity with the ideal cat is only 0.84**, at 0.63 µs.

**Why.**
- By Lesson 2, the vacuum (even) can only become the even cat, "both valleys at once".
- Each photon that leaks out of the memory flips the parity, turning the even cat into the odd one. These random flips gradually wash out the superposition. This is the *phase-flip* error that cat qubits are known for.
- Early on there is one more effect: the memory is briefly entangled with the busy buffer from 1.1, which lowers its purity temporarily.

*Figures: `1.3a_which_state.png`, `1.3a_superposition_vs_mixture.png`.*

> **Lesson 6:** "turn it on and wait" gives a fragile, decaying superposition, not a computational state. To get $|0_L\rangle$, we must do something extra (Part 2).

---

## Part 2 — Our small experiment: preparing $|0_L\rangle=|+\alpha\rangle$ (protocol A)

### The question
How do we make the oscillator land in the $+\alpha$ valley, reliably and quickly?

### The idea
Lessons 2 and 6 say that the stabilization alone cannot choose a valley. Something has to break the symmetry between left and right. Lesson 3 suggests the most direct way to do that: **just put the state where we want it, then let the stabilization hold it.**

### The protocol
1. With the stabilization **off**, apply a short (20 ns) drive to the memory that displaces it from the vacuum to the coherent state $|+2.1\rangle$. This is a "kick" along the real axis.
2. Switch the stabilization **on** and leave it on.

### What we measured
- Fidelity with the target $|+\alpha\rangle$.
- Population left in the wrong valley.
- Parity.
- Buffer photons.

Everything was simulated in the full two-mode model, over 3 µs.

### Results
*Figure: `1.3b_A_wigner.png`. The blob jumps from the origin to $+2.1$ during the 20 ns pulse and then simply stays there.*

| Quantity | Inflate from vacuum (1.1 / 1.3a) | **Protocol A** |
|---|---|---|
| Fidelity with $\lvert+\alpha\rangle$ | 0.50 (no valley chosen) | **0.99998** |
| Time to reach it | never | **20 ns** (the pulse length) |
| Still there after 3 µs? | the cat fades with a time constant of 1.95 µs | **yes, unchanged** |
| Wrong-valley population | 50% | **< 10⁻⁵** |
| Memory photon overshoot | 6.9 photons | **none** (stays at 4.4) |
| Buffer photons (peak) | ≈ 2 | **0.00003** |
| Parity | starts at +1, decays | ≈ 0, as expected for a single valley |

### A mini robustness experiment (A′): how sloppy can the kick be?
A real pulse is never perfectly calibrated. We deliberately started from imperfect kicks, too weak, too strong or in the wrong direction, and let the stabilization act.
*Figure: `1.3b_Aprime_scans.png`.*

| Kick | Final fidelity with $\lvert+\alpha\rangle$ |
|---|---|
| perfect ($\beta=2.1$, no phase error) | 0.99999 |
| 30% too weak ($\beta=1.5$) / 30% too strong ($\beta=2.7$) | 0.9988 / 0.9994 |
| half strength ($\beta=1.0$) | 0.978 |
| aimed 22° off the real axis | 0.998 |
| aimed 45° off | 0.972 |
| aimed 67° off | 0.83 |

**What this shows**
- **The kick only has to land in the right valley's catchment area.** The stabilization does the rest, rolling the state into the bottom of the valley and even rotating it back onto the axis.
- The error that remains is simply the part of the initial blob that starts on the wrong side of the ridge.
- **Strength barely matters, but direction does.** The phase of the drive relative to the cat axis is the one thing that must be calibrated carefully.
- Other checks:
  - the pulse length, anywhere from 10 to 100 ns, makes no difference;
  - leaving the stabilization on during the pulse still gives 0.99993.

---

## Part 3 — What protocol A means, and how it connects to everything before

### Each earlier lesson explains part of the result

| Earlier finding | What it means for protocol A |
|---|---|
| **Lesson 1** (1.0a): photon loss shrinks a coherent state but never flips it | The prepared $\lvert+\alpha\rangle$ is **immune to the error that destroyed the cat in 1.3(a)**. It stays put, while the cat faded in about 2 µs. |
| **Lesson 2** (1.0b): pair processes conserve parity | This is *why* a kick is needed: the stabilization alone can never choose a valley. The kick creates a state with no fixed parity, which frees the stabilization to settle into one valley. |
| **Lesson 3** (1.0c): $\lvert\pm\alpha\rangle$ are resting points | After the kick the state is *already* at a resting point, so the stabilization has nothing to do. That's why the fidelity is reached in 20 ns instead of hundreds. |
| **Lesson 4** (1.1): inflation from vacuum overshoots, rings and fills the buffer | Protocol A **skips the violent transient entirely**: no overshoot, about 0 buffer photons. The memory starts exactly where the drive and pair loss balance. |
| **Lesson 5** (1.2): calm dynamics are easy to simulate, violent ones are not | Protocol A stays at low photon number with an empty buffer. Its results are the **least sensitive to simulation truncation** of anything we ran, which makes this the most trustworthy number in the report. |
| **1.2** steady state slightly below $\alpha^2$ because of photon loss | This sets the tiny remaining infidelity: the stabilized state is a hair smaller than $\lvert2.1\rangle$ (mean amplitude 0.9992 × 2.1). |
| **Lesson 6** (1.3a): vacuum inflation gives a fragile cat | Protocol A gives a *computational* state instead. Same hardware, different first 20 ns. |

### Implications
1. **Preparing $|0_L\rangle$ is easy; it is $|\pm_L\rangle$ (the cats) that are fragile.** This is the cat qubit's central bargain seen from the preparation side. States in one valley are protected; superpositions of the two valleys suffer phase flips. Our simulations show both sides: 0.99998 held for 3 µs versus a cat fading in about 2 µs.
2. **The stabilization acts as a built-in error corrector for preparation.** Because it pulls any state in a valley's catchment area to the bottom, a coarse, imperfect pulse is good enough. In hardware this means cheap calibration, except for the drive phase.
3. **Hardware cost is small.** All protocol A needs is a drive line on the memory, which cat-qubit devices typically already have for gates and measurement, plus phase-locking that drive to the pump. This matches how experiments actually prepare these states (Réglade *et al.*, *Nature* 2024).
4. **It sidesteps the device's weakest regime.** The 1.1 transient (buffer not fast enough, large overshoot) is exactly where our simple model broke down and our simulations were hardest to converge. Avoiding it is good for the real device too: fewer photons means fewer opportunities for unwanted nonlinear effects.
5. **To prepare $|1_L\rangle=|-\alpha\rangle$, kick the other way.** The same protocol works with the drive phase flipped by 180°. By symmetry, the results are identical.

### Caveats
Our pulses are idealized square pulses, and we did not model:
- pump switch-on transients;
- frequency shifts caused by the pump;
- memory nonlinearity (Kerr);
- thermal photons.

Each would add some error. The robustness experiment suggests the stabilization would absorb much of it, as long as the state starts on the right side of the ridge. Fidelities are only trustworthy to about $10^{-4}$, because of the solver tolerance.

### What we would try next
- Add a realistic pulse shape, memory Kerr and pump switch-on dynamics, and re-measure the robustness map.
- Measure how long a protocol-A state keeps its valley under realistic noise, connecting preparation to the bit-flip and phase-flip rates studied in the Standard Track.

*AI use: an AI assistant (Claude Code) helped write the code and text and ran the simulations. The team must verify, and be able to defend, every result.*

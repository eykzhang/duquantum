# Preparing a definite computational state $|0_L\rangle = |+\alpha\rangle$ in a dissipative cat qubit

*Literature notes for Task 1.3b/c. Model: memory $a$ coupled to buffer $b$ with*
$H = g_2(a^2 b^\dagger + a^{\dagger 2} b) - \epsilon_d (b + b^\dagger)$, *jump operators* $\sqrt{\kappa_b}\,b$, $\sqrt{\kappa_a}\,a$.
*Parameters:* $g_2/2\pi = 0.763$ MHz, $\kappa_b/2\pi = 2.6$ MHz, $\kappa_a/2\pi = 9.3$ kHz, $\alpha = 2.1$, $\epsilon_d = \alpha^2 g_2$.

---

## 0. Numbers for our parameters

If $\kappa_b \gg g_2|\alpha|$, the buffer can be adiabatically eliminated. Then $b \approx -\tfrac{2i g_2}{\kappa_b}(a^2 - \alpha^2)$, with $\alpha^2 = \epsilon_d/g_2$ real, and the memory sees the effective dissipator $\kappa_2 \mathcal{D}[a^2 - \alpha^2]$ with $\kappa_2 = 4g_2^2/\kappa_b$.

| Quantity | Value | Meaning |
|---|---|---|
| $\kappa_2/2\pi = 4g_2^2/\kappa_b/2\pi$ | 0.896 MHz | two-photon loss rate ($1/\kappa_2 \approx 178$ ns) |
| $\kappa_2|\alpha|^2/2\pi$ | 3.95 MHz | instability rate of vacuum, $\tau \approx 40$ ns |
| $2\kappa_2|\alpha|^2/2\pi$ | 7.9 MHz | confinement gap ("return to manifold"), $\tau \approx 20$ ns |
| $\epsilon_d/2\pi$ | 3.36 MHz | buffer drive |
| $2\epsilon_d/\kappa_b$ | 2.6 | buffer amplitude **if the memory is empty** ($\bar n_b \approx 6.7$) |
| $8 g_2 \alpha/\kappa_b$ | 4.9 | adiabaticity parameter (Réglade et al. used ≈3.6). **Elimination of the buffer is not well justified here.** |
| $\kappa_a|\alpha|^2/2\pi$ | 41 kHz | phase-flip rate of the cats, $T_Z \approx 3.9\ \mu$s |
| $\kappa_a/\kappa_2$ | 0.010 | ratio that sets the quality of the "cat"; smaller is better |
| $\langle \alpha | -\alpha\rangle = e^{-2|\alpha|^2}$ | $1.5\times10^{-4}$ | overlap of the two wells |

**Key physical point.** $|0_L\rangle \equiv (|C_+\rangle + |C_-\rangle)/\sqrt2$ equals $|+\alpha\rangle$ up to corrections of order $e^{-2|\alpha|^2}$. It is a (quasi-)fixed point of the stabilization. Single-photon loss acts as $a|\pm\alpha\rangle = \pm\alpha|\pm\alpha\rangle$, so it does **not** flip it. Once you reach the right well, the state stays there for the bit-flip time, which is $\gg$ all other timescales and grows roughly like $e^{+2|\alpha|^2}$ (the bit-flip *rate* is the one suppressed as $e^{-2|\alpha|^2}$).

The real problem is narrower than "preparing a fragile superposition". You must **break the $a \to -a$ symmetry** of the vacuum, or of the even cat, so the system lands in the $+\alpha$ basin. Two-photon dissipation conserves parity, so starting from vacuum it can only produce $|C_+\rangle$.

---

## 1. General state-preparation toolbox in bosonic circuit QED

1. **Coherent displacement drives.** A resonant drive $H = i\epsilon(t)(a^\dagger - a)$ generates $D(\beta)$ with $\beta = \int \epsilon\,dt$, mapping vacuum to $|\beta\rangle$. It is linear, so it is fast, simple and calibration-light. It is the workhorse for initialization and tomography in all cat experiments.

2. **Reservoir engineering / dissipative stabilization.** Engineer a jump operator whose dark states are the target states (Poyatos, Cirac & Zoller 1996). For cats the jump operator is $L_2 = a^2 - \alpha^2$ (Mirrahimi et al. 2014; Leghtas et al. 2015). It is autonomous and robust, but it only selects a *manifold*. Within the manifold, conserved quantities, such as parity for $L_2$, remember the initial condition.

3. **Measurement-based preparation.** Do a QND measurement (parity, a quadrature, or photon number), then either postselect (heralding) or apply feedback. Examples:
   - repeated parity measurements (Sun et al. 2014);
   - parity-based random preparation of cat product states in the AWS repetition-code experiment (Putterman et al. 2025).

4. **Optimal control.** GRAPE on a transmon plus cavity (Khaneja et al. 2005; Heeres et al. 2017), and SNAP gates combined with displacements, which give universal control (Krastanov et al. 2015; Heeres et al. 2015). These can reach high fidelity, but they need a dispersively coupled ancilla qubit and a precise system model. They are used to encode cats in 3D cavities (Vlastakis et al. 2013; Ofek et al. 2016).

5. **Adiabatic protocols.** Slowly turn on a nonlinearity or drive so that an easily prepared eigenstate maps continuously onto the target.
   - Kerr-cat: ramping the squeezing drive maps $|0\rangle,|1\rangle \to |C_+\rangle,|C_-\rangle$ (Puri, Boutin & Blais 2017; Grimm et al. 2020).
   - Bifurcation-based / annealing schemes use a small bias field during the ramp to pick one of the two coherent states (Goto 2016; Puri et al. 2017, Nat. Commun.).

6. **Heralding / postselection.** Accept only runs whose measurement record certifies the target. The success probability is below 1, so this is useful for experiments but not deterministic.

---

## 2. Cat-qubit literature: how $|\pm\alpha\rangle$ and $|C_\pm\rangle$ are prepared

### 2.1 X basis ($|C_\pm\rangle$)

- **Dissipative inflation from vacuum.** With $L_2 = a^2-\alpha^2$ the photon-number parity $e^{i\pi a^\dagger a}$ commutes with the dynamics, so vacuum flows to $|C_+\rangle$. This was proposed by Mirrahimi et al. (2014) and observed with transient cats by Leghtas et al. (2015). Réglade et al. (2024) prepare $|+\rangle_\alpha$ this way in 0.4–1 µs. They state that *"preparing $|-\rangle_\alpha$ requires an additional memory drive to perform a full Zeno-blocked π rotation."*
- **Kerr-cat.** Prepare Fock $|0\rangle$ or $|1\rangle$, then ramp the squeezing drive adiabatically (tanh profile over 320 ns, $\gg 1/2K$) to map $|0\rangle,|1\rangle \to |C_+\rangle,|C_-\rangle$. The mapping fidelity was ≈0.855 (Grimm et al. 2020).
- **Measurement.** The AWS experiment (Putterman et al. 2025) prepares X-basis product states by measuring each storage-mode parity under stabilization. This "randomly prepares the logical qubit into one of the $2^d$ product cat states", and the record is then tracked.

### 2.2 Z basis ($|\pm\alpha\rangle$)

**(a) Displace, then stabilize. This is the standard method.**
- Réglade et al. (2024): "starting from the vacuum, the memory is displaced to the target amplitude", then the two-photon pump and buffer drive stabilize $|\pm\alpha\rangle$.
- Putterman et al. (2025): data cats are initialized "in a tensor-product of coherent states" for the Z-basis memory experiment. Their stability tests "displace the storage mode into a coherent state" and then apply two-photon dissipation.
- Touzard et al. (2018) and Lescanne et al. (2020) likewise use displacements of the memory for initialization and Wigner tomography.

Equations. Apply $D(\beta)|0\rangle = |\beta\rangle$, then switch on $\epsilon_d$. The phase of $\beta$ relative to $\sqrt{\epsilon_d/g_2}$ selects the well. Any $\beta$ with $\mathrm{Re}(\beta e^{-i\arg\alpha}) > 0$ flows predominantly to $+\alpha$. Amplitude errors are removed at the confinement rate $\sim 2\kappa_2|\alpha|^2$. The residual weight in the wrong well falls off roughly like the overlap of $|\beta\rangle$ with the $-\alpha$ basin.

The exact asymptotic state for any initial $\rho$ under pure $\mathcal D[a^2-\alpha^2]$ can be computed from the conserved quantities $J_{++}, J_{+-}$ given by Mirrahimi et al. (2014). We have not reproduced their closed form here, so simulate it.

Expected fidelity: limited by $\kappa_a$-induced amplitude shrinkage, of relative size $\sim\kappa_a/\kappa_2 \approx 1\%$ in $|\alpha|^2$, by thermal photons, and by non-adiabatic buffer dynamics. We expect $F > 0.99$ with respect to the stabilized $|+\alpha\rangle$.

**(b) Single-photon drive during or after inflation: the "Zeno gate".**

Projected onto the code space, $a \approx \alpha Z_L$ with $Z_L = |{+\alpha}\rangle\langle{+\alpha}| - |{-\alpha}\rangle\langle{-\alpha}|$. Therefore
$$H_Z = \epsilon_Z(a e^{-i\phi} + a^\dagger e^{i\phi}) \;\to\; 2\epsilon_Z|\alpha|\cos(\phi - \arg\alpha)\, Z_L .$$
This is the Zeno-blocked rotation $|C_+\rangle \leftrightarrow |C_-\rangle$ (Mirrahimi et al. 2014; Touzard et al. 2018; Réglade et al. 2024, where a π rotation took 235 ns at $|\alpha|^2 = 11.3$).

**Caveat that matters for this task:** $Z_L$ rotations move $|C_+\rangle$ around the X–Y equator and never reach $|0_L\rangle$. A Zeno drive alone therefore *cannot* prepare $|+\alpha\rangle$ from $|C_+\rangle$.

The leakage it causes, through the drive component orthogonal to the manifold, scales roughly as $(\epsilon_Z/\kappa_2|\alpha|)^2$ in population. The trade-off between gate time $T$ and error has the form (Guillaud & Mirrahimi 2019; Guillaud, Cohen & Mirrahimi 2023)
$$p_Z \sim \kappa_a|\alpha|^2 T + \frac{c}{\kappa_2|\alpha|^2 T},$$
with an $O(1)$ constant $c$. The minimum is $\propto\sqrt{\kappa_a/\kappa_2}$. We did not verify the exact prefactors.

Réglade et al. also report drive-induced dephasing that grows with amplitude, an optimal drive amplitude, and a phase asymmetry they had to calibrate within ±15°.

**(b′) Symmetry-breaking "bias" drive along the cat axis: our proposal, based on the literature.**

Drive in the *other* quadrature, $H_b = i\epsilon_b(a^\dagger - a)$ for real $\alpha$. In the code space $P(a^\dagger - a)P \approx (\alpha^* - \alpha)Z_L = 0$, so to first order this drive does **no** logical rotation. It only shifts the well positions by $\delta \approx \epsilon_b/(2\kappa_2|\alpha|^2)$: $+\alpha$ moves outward and $-\alpha$ inward.

Near the vacuum the mean-field equation is $\dot\alpha_c \approx \kappa_2\alpha^2\alpha_c^* + \epsilon_b$. The real quadrature is unstable, with rate $\kappa_2|\alpha|^2$ (τ ≈ 40 ns for us). The bias tips the bifurcation toward $+\alpha$, like the local bias fields in Kerr-oscillator annealing (Goto 2016; Puri et al. 2017, Nat. Commun.). For the push to beat vacuum fluctuations of order $1/2$ within one instability time, we need $\epsilon_b \gtrsim \kappa_2|\alpha|^2/2$, i.e. $\epsilon_b/2\pi \gtrsim 2$ MHz. Weaker bias works if it is held on longer, or during a slow ramp of $\epsilon_d$.

This is essentially "displacement during inflation". Its advantage is that it is self-correcting and needs little calibration, because it only has to push in the right half-plane. We have not found a paper that uses exactly this protocol to prepare dissipative cats, so treat it as our own (literature-motivated) proposal.

**(c) Ramping the pump amplitude or phase.**
- *Amplitude ramp* $\epsilon_d(t) = g_2\alpha(t)^2$, combined with a weak bias $\epsilon_b$. This is an adiabatic bifurcation. A slower ramp makes a smaller $\epsilon_b$ sufficient, and leaves less residual leakage when $\epsilon_b$ is switched off.
- *Phase ramp.* Rotating the pump phase, $\alpha \to \alpha e^{i\theta(t)}$, rotates the wells. A rotation by $\theta = \pi$ implements the bit flip $X_L$ (Mirrahimi et al. 2014; Guillaud & Mirrahimi 2019). It does not create a definite Z state from $|C_+\rangle$ by itself, but it is the physical "feedback" operation used in (d).

**(d) Measure Z, then feedback or postselect.**

Prepare $|C_+\rangle$ and perform a Z (which-well) measurement. The outcome is $\pm$ with probability 1/2 each, and the state is projected to $|\pm\alpha\rangle$. Then either:
- **postselect** on "+" (heralded, 50% success), or
- apply $X_L$ when the outcome is "−".

Here $X_L = e^{i\pi a^\dagger a}$ commutes with $a^2-\alpha^2$. It can therefore be done by a π phase-space rotation, or, in our reasoning, simply by a **frame update**: shift the phase of all subsequent memory drives by π, analogous to virtual-Z gates on transmons. We have not seen this phrasing in a paper, so treat it as our reasoning.

Feedback latency is harmless here. Once projected, the state lives for the bit-flip time (seconds in Réglade et al. 2024; ~0.1 s in Berdou et al. 2023; 0.3 s in Marquet et al. 2024).

**(e) Z readout through the buffer or a readout chain.**
- *Réglade et al. (2024)*, transmon-free: they read Z by mapping $|\pm\alpha\rangle$ to $|0\rangle$ or $|2\alpha'\rangle$ followed by a photon-number readout. For long trajectories they monitor the buffer's response to a weak memory drive, which gives real-time bit-flip detection.
- *Putterman et al. (2025)*: single-shot Z readout via "displaced-vacuum population measurements" using an ancilla transmon.
- *Grimm et al. (2020)*, Kerr-cat: the "cat-quadrature readout" converts the cat quadrature into a drive on a readout cavity. It took 3.6 µs, with fidelity 0.74 and QND-ness 0.85.

### 2.3 Related confinement schemes

- **Kerr-cats** (Puri, Boutin & Blais 2017; Puri et al. 2020; Grimm et al. 2020; Frattini et al. 2024) confine the state with a Hamiltonian $-K a^{\dagger2}a^2 + \epsilon_2(a^{\dagger2} + a^2)$. Its Fock-to-cat map can produce $|+\alpha\rangle$ if one starts from $(|0\rangle+|1\rangle)/\sqrt2$.
- **Combined dissipative + two-photon-exchange Hamiltonian confinement** (Gautier, Sarlette & Mirrahimi 2022) speeds up return to the manifold and tightens confinement. This directly helps protocols (a), (b′) and (c).
- **Architecture context:** repetition cat code (Guillaud & Mirrahimi 2019; Le Régent et al. 2023), concatenated cat codes (Chamberland et al. 2022), and the AWS experiment (Putterman et al. 2025).

---

## 3. Physical limitations and practical constraints

1. **Extra control line and phase locking.** Displacement and bias drives need a memory drive port at $\omega_a$, typically shared with the Zeno-gate/tomography line. The phase must be locked to the pump, at $2\omega_a - \omega_b$, and to the buffer drive at $\omega_b$. The cat axis is set by $\arg\alpha = \tfrac12\arg(\epsilon_d/g_2)$ up to π, and the memory drive must be calibrated against it. Getting the phase wrong by 90° turns a bias drive into a Zeno drive.
2. **Amplitude calibration.** Line attenuation must be converted to photons, for example from Wigner or Q-function fits. Protocol (a) is forgiving because the stabilization corrects $|\beta|$ errors. Protocol (b′) is forgiving in amplitude too, but not in phase.
3. **Bandwidth and rise time.** A Gaussian pulse of ~20–50 ns (e.g. $\epsilon/2\pi \approx 10$ MHz for 33 ns gives $\beta = 2.1$) is easy for AWGs. Faster pulses spread spectrally into the buffer, and may hit other modes or pump sidebands.
4. **Stark shifts and frequency shifts.** The pump AC-Stark-shifts the memory and the buffer. A memory drive applied with the pump on must use the shifted frequency, or it accumulates a phase drift that rotates $\beta$ relative to the cat axis. Displacing *before* the pump avoids this, but then $|\beta\rangle$ sits briefly without protection. That costs little, since $\kappa_a T \sim 10^{-3}$.
5. **Kerr and higher-order ATS terms.** The asymmetrically threaded SQUID (ATS) of Lescanne et al. (2020) is operated where the Kerr effect is suppressed. Residual self-Kerr $K a^{\dagger2}a^2$ shears the coherent state during displacement and adds a photon-number-dependent rotation of $\arg\alpha$. Pump-induced spurious conversions are another issue: Berdou et al. (2023) and Marquet et al. (2024) discuss parasitic processes that limit the bit-flip time.
6. **Pump-induced heating and thermal population.** Thermal photons, through $\kappa_a n_{th}\mathcal D[a^\dagger]$, induce phase flips and, at small $\alpha$, bit flips. The initial thermal state of the memory blurs which well a weak bias selects.
7. **Breakdown of adiabatic elimination.** Here $8g_2\alpha/\kappa_b \approx 4.9$ and $\epsilon_d/\kappa_b \approx 1.3$. The buffer is not fast, so the effective $\kappa_2$ model is only qualitative. Simulate the full two-mode model. Note the buffer transient: with the memory empty, $\bar n_b \approx (2\epsilon_d/\kappa_b)^2 \approx 6.7$. Displacing the memory first suppresses this transient, because $g_2 a^2 \approx \epsilon_d$.
8. **Leakage out of the manifold.** Any memory drive with $\epsilon \sim \kappa_2|\alpha|$ deforms the wells. Leakage is $\sim(\epsilon/\kappa_2|\alpha|)^2$ for the transverse component and $\delta \approx \epsilon/(2\kappa_2|\alpha|^2)$ for the longitudinal (bias) component. It is reabsorbed within ~$1/(2\kappa_2|\alpha|^2) \approx 20$ ns after the drive is switched off.
9. **Drive-induced phase flips.** The quadrature component of a memory drive that is along $i\alpha$ (the "Zeno" direction) rotates about $Z_L$. That is harmless for a pure $|+\alpha\rangle$, which is a $Z_L$ eigenstate. It matters if you drive during the inflation of a superposition.
10. **Measurement fidelity and latency (protocol d).** Real Z readouts are imperfect: 0.74 for Kerr-cat CQR, and limited by $g_2/\kappa_a$ in Réglade et al. Readout errors translate directly into preparation infidelity, unless the measurement is repeated or postselected. Latency is not a concern because the bit-flip time is very long.
11. **Simulation truncation.** For $|\alpha|^2 = 4.4$, use a memory cutoff $N_a \gtrsim |\alpha|^2 + 6|\alpha| \approx 17$; use 20–30 and check convergence. Use a buffer cutoff $N_b \approx 10$–15 to capture the 6.7-photon transient. Displacement overshoot (e.g. $\beta = 3$, $|\beta|^2 = 9$) needs $N_a \gtrsim 30$.

---

## 4. Practical recommendations for our implementation (dynamiqs)

Simulate the full two-mode Lindblad model with jump operators $\sqrt{\kappa_b}b$ and $\sqrt{\kappa_a}a$, and optionally $\sqrt{\kappa_a n_{th}}a^\dagger$. Use time-dependent drives (`dq.pwc`/`dq.modulated` or Python callables in `dq.mesolve`).

**Metrics to report** (after `ptrace` onto $a$):
- $F_0 = \langle +\alpha|\rho_a|+\alpha\rangle$, or the logical $P_0 = \langle 0_L|\rho_a|0_L\rangle$ with $|0_L\rangle = (|C_+\rangle+|C_-\rangle)/\sqrt2$;
- the logical $\langle Z_L\rangle = P_0 - P_1$;
- $\mathrm{Re}\langle a\rangle/\alpha$, which is ≈1 for success, 0 for a cat, and −1 for the wrong well;
- parity $\langle e^{i\pi a^\dagger a}\rangle$, which goes from 1 for $|C_+\rangle$ to $\approx e^{-2|\alpha|^2}\approx 0$;
- leakage $1 - \mathrm{Tr}[(|C_+\rangle\langle C_+| + |C_-\rangle\langle C_-|)\rho_a]$;
- Wigner snapshots;
- time to reach $F_0 > 0.99$.

### Rank 1: displace, then stabilize (most practical, literature standard)

- *Variants:*
  - (i) ideal initial state $|\beta\rangle\otimes|0\rangle_b$;
  - (ii) a finite Gaussian pulse $H = i\epsilon(t)(a^\dagger - a)$ with $\int\epsilon\,dt = \beta$ and duration 10–100 ns, with the pump off ($g_2=\epsilon_d=0$), then everything switched on;
  - (iii) the same pulse with stabilization already on.
- *Scan:* $\beta \in [0, 3]$ (show that even $\beta \approx 1$ already biases strongly toward $+\alpha$, and that overshoot is corrected); phase error $\theta \in [0, \pi/2]$; pulse width.
- *Expected:* $F_0 \gtrsim 0.99$ within ~0.2–0.5 µs. The infidelity floor comes from $\kappa_a$, a slight shrinkage of $\alpha$, and buffer non-adiabaticity. Fidelity falls to 1/2 as $\theta \to \pi/2$.
- *Cost:* one memory drive line (usually already present), a phase lock to the pump, and a coarse amplitude calibration. It also suppresses the buffer transient.

### Rank 2: bias drive during inflation (symmetry breaking, self-correcting)

- Start from vacuum. Apply $\epsilon_d$ and $H_b = i\epsilon_b(a^\dagger - a)$ for a time $T_b$, then switch $\epsilon_b$ off.
- *Scan:* $\epsilon_b/2\pi \in \{0.2, 0.5, 1, 2, 5\}$ MHz and $T_b \in [50, 1000]$ ns.
- *Expected:* $P_0$ rises from 0.5 (no bias) toward 1 once $\epsilon_b T_b \gtrsim 1$. During the drive the well shifts by $\delta \approx \epsilon_b/(2\kappa_2|\alpha|^2)$, about 0.13 for 1 MHz, and this leakage is reabsorbed within ~20 ns after switch-off.
- *Instructive control experiment:* repeat with the orthogonal quadrature $\epsilon(a + a^\dagger)$. It produces Zeno rotations of the cat ($|C_+\rangle\to|C_-\rangle$) instead of $|+\alpha\rangle$, which shows the phase-calibration cost.
- *Cost:* same line as rank 1. It needs phase calibration relative to the cat axis, but loose amplitude calibration.

### Rank 3: Z measurement + feedback (or postselection) — the "measurement-based" answer

- Prepare $|C_+\rangle$ by inflation, about 1–2 µs. Apply an ideal projective measurement with $\Pi_\pm = |\pm\alpha\rangle\langle\pm\alpha|$ in the code basis, or $\Theta(\pm\hat x)$. On outcome "−", apply $e^{i\pi a^\dagger a}$ (a virtual X).
- For realism, model a finite-fidelity readout with error $p_m \in [0, 0.15]$, giving $F_0 \approx 1-p_m$. Optionally show two repeated measurements with a majority vote.
- *Cost:* a full readout chain (transmon or buffer-based) and real-time FPGA logic. In exchange it gives heralding. Latency is irrelevant given the bit-flip times.

### Rank 4 (stretch): adiabatic pump ramp + weak bias, or optimal control

- Ramp $\epsilon_d(t) = g_2\alpha^2 s(t)$ with a tanh or linear profile over $T_r \in [0.2, 2]$ µs, together with a weak bias $\epsilon_b/2\pi \in [0.05, 0.5]$ MHz. Show that a longer ramp allows a smaller bias and less leakage, in the style of Goto and Puri.
- Alternatively, use dynamiqs' JAX differentiability to optimize a piecewise-constant $\epsilon_a(t)$, GRAPE-style, maximizing $F_0$ at fixed $T$ (e.g. 100 ns).
- Expect at best marginal gains over rank 1, at a large calibration cost.

**Bottom line for the write-up (1.3c).** Preparing $|0_L\rangle$ is easy compared with $|C_-\rangle$: it only needs symmetry breaking, after which the stabilization itself does the error correction. The cost is one phase-locked memory drive and the associated calibration (phase relative to the pump, Stark shift if the pump is on, and photon-number calibration), plus a transient deformation of the wells while the drive is on.

---

## References

All entries were checked against arXiv/journal pages, except those marked [unverified].

1. M. Mirrahimi, Z. Leghtas, V. V. Albert, S. Touzard, R. J. Schoelkopf, L. Jiang, M. H. Devoret, "Dynamically protected cat-qubits: a new paradigm for universal quantum computation," *New J. Phys.* **16**, 045014 (2014). arXiv:1312.2017, doi:10.1088/1367-2630/16/4/045014.
2. Z. Leghtas et al., "Confining the state of light to a quantum manifold by engineered two-photon loss," *Science* **347**, 853 (2015). arXiv:1412.4633, doi:10.1126/science.aaa2085.
3. S. Touzard et al., "Coherent oscillations inside a quantum manifold stabilized by dissipation," *Phys. Rev. X* **8**, 021005 (2018). arXiv:1705.02401, doi:10.1103/PhysRevX.8.021005.
4. R. Lescanne et al., "Exponential suppression of bit-flips in a qubit encoded in an oscillator," *Nat. Phys.* **16**, 509 (2020). arXiv:1907.11729, doi:10.1038/s41567-020-0824-x.
5. A. Grimm et al., "Stabilization and operation of a Kerr-cat qubit," *Nature* **584**, 205 (2020). arXiv:1907.12131.
6. S. Puri, S. Boutin, A. Blais, "Engineering the quantum states of light in a Kerr-nonlinear resonator by two-photon driving," *npj Quantum Inf.* **3**, 18 (2017). arXiv:1605.09408, doi:10.1038/s41534-017-0019-1.
7. S. Puri et al., "Bias-preserving gates with stabilized cat qubits," *Sci. Adv.* **6**, eaay5901 (2020). arXiv:1905.00450.
8. J. Guillaud, M. Mirrahimi, "Repetition cat qubits for fault-tolerant quantum computation," *Phys. Rev. X* **9**, 041053 (2019). arXiv:1904.09474.
9. J. Guillaud, J. Cohen, M. Mirrahimi, "Quantum computation with cat qubits," *SciPost Phys. Lect. Notes* **72** (2023). arXiv:2203.03222.
10. R. Gautier, A. Sarlette, M. Mirrahimi, "Combined dissipative and Hamiltonian confinement of cat qubits," *PRX Quantum* **3**, 020339 (2022). arXiv:2112.05545.
11. C. Berdou et al., "One hundred second bit-flip time in a two-photon dissipative oscillator," *PRX Quantum* **4**, 020350 (2023). arXiv:2204.09128.
12. U. Réglade et al., "Quantum control of a cat qubit with bit-flip times exceeding ten seconds," *Nature* **629**, 778 (2024). arXiv:2307.06617, doi:10.1038/s41586-024-07294-3.
13. A. Marquet et al., "Autoparametric resonance extending the bit-flip time of a cat qubit up to 0.3 s," *Phys. Rev. X* **14**, 021019 (2024). arXiv:2307.06761.
14. H. Putterman et al., "Hardware-efficient quantum error correction via concatenated bosonic qubits," *Nature* **638**, 927 (2025). arXiv:2409.13025, doi:10.1038/s41586-025-08642-7.
15. C. Chamberland et al., "Building a fault-tolerant quantum computer using concatenated cat codes," *PRX Quantum* **3**, 010329 (2022). arXiv:2012.04108.
16. H. Goto, "Bifurcation-based adiabatic quantum computation with a nonlinear oscillator network," *Sci. Rep.* **6**, 21686 (2016). arXiv:1510.02566.
17. S. Puri, C. K. Andersen, A. L. Grimsmo, A. Blais, "Quantum annealing with all-to-all connected nonlinear oscillators," *Nat. Commun.* **8**, 15785 (2017). arXiv:1609.07117.
18. R. W. Heeres et al., "Cavity state manipulation using photon-number selective phase gates," *Phys. Rev. Lett.* **115**, 137002 (2015). arXiv:1503.01496.
19. R. W. Heeres et al., "Implementing a universal gate set on a logical qubit encoded in an oscillator," *Nat. Commun.* **8**, 94 (2017). arXiv:1608.02430.
20. S. Krastanov et al., "Universal control of an oscillator with dispersive coupling to a qubit," *Phys. Rev. A* **92**, 040303 (2015). arXiv:1502.08015. [unverified in this session; from memory]
21. N. Khaneja et al., "Optimal control of coupled spin dynamics: design of NMR pulse sequences by gradient ascent algorithms," *J. Magn. Reson.* **172**, 296 (2005). [unverified in this session; from memory]
22. J. F. Poyatos, J. I. Cirac, P. Zoller, "Quantum reservoir engineering with laser cooled trapped ions," *Phys. Rev. Lett.* **77**, 4728 (1996). [unverified in this session; from memory]
23. B. Vlastakis et al., "Deterministically encoding quantum information using 100-photon Schrödinger cat states," *Science* **342**, 607 (2013); L. Sun et al., "Tracking photon jumps with repeated quantum non-demolition parity measurements," *Nature* **511**, 444 (2014); N. Ofek et al., "Extending the lifetime of a quantum bit with error correction in superconducting circuits," *Nature* **536**, 441 (2016). [unverified in this session; from memory]
24. N. E. Frattini et al., "Observation of pairwise level degeneracy and the quantum regime of the Arrhenius law in a double-well parametric oscillator," *Phys. Rev. X* **14**, 031040 (2024). arXiv:2209.03934. [unverified in this session; from memory]
25. F.-M. Le Régent et al., "High-performance repetition cat code using fast noisy operations," *Quantum* **7**, 1198 (2023). arXiv:2212.11927. [unverified in this session; from memory]
26. dynamiqs: GPU-accelerated, differentiable quantum simulation in JAX, https://github.com/dynamiqs/dynamiqs.

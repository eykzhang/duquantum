# Task 1 — Issues log (what didn't work, what surprised us, how we resolved it)

This file records problems hit while building `task1.ipynb`, wrong turns, and results that differed from expectations. The README asks us to report these honestly. Where a number is quoted, the source is stated: a **prototype** script run during development, or the **notebook** itself.

> **Status (2026-10-24):** every simulation in the notebook was first prototyped in standalone scripts, and those numbers are the ones quoted in the markdown "Results" cells. The first full end-to-end execution of the finished notebook was **stopped by the OS/Claude Code for low system memory** before it finished, so the notebook is currently saved **without outputs**. To do: re-run it top to bottom, then check each "Results" cell against the printed numbers and correct any that disagree. Rough runtime is ~15 min on CPU. Section 1.2 (convergence) and 1.3(b) are the heaviest. If memory is tight, restart the kernel between sections, or drop `N_a=32` from `Na_scan`.

---

## 1. Tooling / API issues

| Problem | Symptom | Fix |
|---|---|---|
| `dq.mesolve(..., options=dq.Options(...))` rejected | `TypeError: mesolve() got an unexpected keyword argument 'options'` | In dynamiqs 0.3.7, options such as `save_states` and `save_extra` are **direct keyword arguments** of `mesolve`. |
| Progress bars flood output | Each solve printed thousands of `elapsed ... remaining` lines (the 120 KB log of one prototype) | `dq.set_progress_meter(False)` in the setup cell. |
| Memory cost of storing two-mode states | $(N_aN_b)^2=288^2$ complex entries × 401 times ≈ 0.5 GB per run at $N_a=24,N_b=12$ | `save_states=False` plus `save_extra=lambda t, rho: dq.ptrace(rho, 0, (Na, Nb))`. Only the 24×24 memory state is stored, about 3.7 MB. |
| Full notebook run killed | Low system memory while running the whole notebook in one kernel (see status above) | Not yet resolved. Run section by section, or on a machine with more RAM. JAX also caches compiled solvers for every new shape, so the `N_a`/`N_b` scan accumulates memory. |
| `nbformat`/`nbclient` not installed | Needed to execute the notebook headlessly | Installed into a scratch folder only. Running the notebook interactively in Jupyter does not need them. |
| Starter code comment (README) | The README flags `dq.destroy(Na, Nb)` and `dq.ptrace` as unverified | Both work in dynamiqs 0.3.7: `destroy(Na, Nb)` returns a tuple `(a, b)`, and `ptrace(rho, 0, (Na, Nb))` keeps the memory. |
| Wigner axis convention | Needed to know what the plot axes mean | dynamiqs uses `hbar=0.5`, so the axes are $\mathrm{Re}\,\alpha$, $\mathrm{Im}\,\alpha$ and a coherent state $|2\rangle$ sits at $x=2$. With this convention $W(0)=\tfrac{2}{\pi}\langle P\rangle$. |

## 2. Physics surprises and things that "did not work"

1. **The starter truncation $N_b=6$ is wrong for the transient** (prototype). The overshoot peak was 6.11 at $N_b=6$ and 6.87–6.90 at $N_b\ge10$, an error of about 11%. With $N_b=4$ there is no overshoot at all ($\langle n\rangle_{\max}=4.46$, monotonic-looking). That looks deceptively like the effective model. The steady state agreed to 0.01% for every truncation, so **checking only the final value would have hidden the bug**. In the starter run the top buffer level held up to 4.6% of the population.

2. **$N_a=16$ looks converged at steady state but is not during the transient.** Its top memory levels hold 5.7% of the population during the overshoot, and the peak is wrong by about 7%. $N_a=20$ is still about 1% low on the peak.

3. **The effective (adiabatically eliminated) model fails to describe the transient.** It predicts a monotonic rise, while the two-mode model overshoots by about 55%. Reason: $g_2/\kappa_b=0.29$ and $\epsilon_d/\kappa_b=1.29$, so the condition $\kappa_b\gg g_2,\epsilon_d$ does not hold. We explained the ringing with a linearized analysis, $8g_2\alpha/\kappa_b\approx4.9>1$, which gives under-damping with period $\approx0.32\ \mu$s and envelope rate $\kappa_b/4$. The notebook checks these predictions against the extrema of $\langle n_a\rangle(t)$. *This comparison has not yet been verified in the full notebook run.*

4. **The steady state is 0.16% below $\alpha^2=4.41$.** This is not a numerical artifact. Including $\kappa_a$ in the semiclassical equations gives $\alpha^2-\kappa_a/(2\kappa_2)=4.4048$, against 4.4027 simulated. The remaining 0.05% was not fully explained; we attribute it to quantum (beyond-mean-field) corrections.

5. **Pure two-photon loss (1.0b) plateau.** We first assumed the plateau is "the population that was already in $|0\rangle,|1\rangle$", which is 9.2%. That is wrong: it is the *odd-parity weight*, $(1-e^{-8})/2=0.49983$, which matches the simulation (0.49983).

6. **Purity of the inflated cat is well below 1, even early** (about 0.68 at 0.2 µs, prototype). At first this looked like the state was "becoming a mixture of $\pm\alpha$". It is not: $\langle P\rangle$ stays large, about 0.8 at 0.5 µs, which a $\pm\alpha$ mixture cannot have. The purity loss comes from entanglement with the buffer during the transient, plus $C_+\leftrightarrow C_-$ phase-flip mixing from $\kappa_a$. Lesson: **purity alone cannot tell the two kinds of mixture apart. Parity and $W(0)$ can.**

7. **The parity (phase-flip) time is about 1.9 µs, not 3.9 µs.** The research summary quotes $T_Z\approx3.9\ \mu$s, which is $1/(\kappa_a\alpha^2)$, the *flip-probability* rate. $\langle P\rangle$ decays at $2\kappa_a\alpha^2$, giving 1.94 µs, which is the README's "roughly $1/(2\kappa_a\alpha^2)$". Both are correct; they are different quantities. Make sure to state which one you mean.

8. **State preparation: the Zeno-quadrature drive $\epsilon(a+a^\dagger)$ does not work** (prototype: $F_+\to0.50$, $\mathrm{Re}\langle a\rangle=0$). Projected onto the code space it is a logical Z rotation and cannot change the $\pm\alpha$ populations. Only the $i(a^\dagger-a)$ quadrature, a real-axis displacement, breaks the symmetry. We kept it in the notebook as a control (protocol D).

9. **The bias drive during inflation (protocol B) saturates around $F_+\approx0.97$–$0.98$** (prototype), even with stronger drives:

   | drive | $F_+$ |
   |---|---|
   | 0.5 MHz, 300 ns | 0.74 (too weak) |
   | 2 MHz, 100 ns | 0.975 |
   | 2 MHz, 300 ns | 0.983 |
   | 5 MHz, 100 ns | 0.973 |

   It does not reach the >0.999 of "displace, then stabilize". Our interpretation: while the bias is on, the stabilization is already inflating the vacuum symmetrically and the buffer transient is large, so a few percent of the population still escapes to $-\alpha$. We did not optimize pulse shapes or ramps further. That is a possible extension (research file, rank 4).

10. **A small displacement ($\beta\le1$) is not enough** (prototype: $\beta=0.5$ gives $F_+=0.84$, $\beta=1$ gives 0.978, $\beta=2.1$ gives 1.000). The half-line estimate $\tfrac12\mathrm{erfc}(\sqrt2\beta)$ captures only the trend, not the exact value, because the basin boundary is not the line $x=0$.

## 3. Modelling simplifications (limitations, not bugs)

- Pulses are square (`dq.modulated` with a step function). There are no rise times, no AWG bandwidth limits, and no pump-switching transients or Stark shifts.
- The memory has no Kerr nonlinearity and there is no thermal population ($n_{th}=0$).
- Protocol C uses an ideal, instantaneous projector onto $x>0$, so it is an upper bound on any real measurement-based scheme.
- In the A′ scans the buffer truncation is reduced to $N_b=8$ for speed. This is justified because these runs barely excite the buffer and the steady state is insensitive to $N_b$ (Task 1.2).

## 4. AI-use note (for the README, as required by §0.3)

An AI assistant (Claude Code) did the following:
- drafted the notebook cells and markdown derivations;
- ran prototype simulations;
- produced the literature survey `research_state_preparation.md`, via a research sub-agent. Citations marked "[unverified]" there were not checked against the source.

The team must verify every derivation and re-run the notebook before presenting. In particular, check the items in the status note at the top.

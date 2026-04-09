# **_Skepthical_** review: *ACT DR6 Internal Consistency from Map-Domain Diagnostics at 90 and 150 GHz*

## Summary

This manuscript presents a deliberately lightweight, map-domain set of internal-consistency diagnostics for ACT DR6.02 All-Array (AA) temperature maps at 90 and 150 GHz, using only the publicly released temperature maps and inverse-variance (ivar) maps. The core statistic is an ivar-normalized residual-width estimator (Eq. (1)) whose null expectation is $\sigma_{\rm norm}=1$ under independent Gaussian noise with per-pixel variance given by ivar. The authors apply this and related weighted-variance and pixel-scale “roughness” (Eq. (2)) metrics to (i) day vs night AA coadds, (ii) four time splits (set0–set3) via set–coadd and set–set differences, and (iii) elevation null products (null-el1) compared to standard AA coadds for PA5 f090/f150 and, as a cross-check, PA4 f150 (Sec. 2–4). Across these tests, the empirical scatter in differences is consistently larger than the simple ivar-based expectation, with $\sigma_{\rm norm}$ reaching $\sim1.9$–$2.0$ for day–night differences and $\sim1.2$–$1.9$ for time-split tests, while null-el1 maps show $\sim1.3$–$1.5\times$ higher weighted variance and $\sim1.7$–$1.8\times$ higher pixel-scale roughness than standard coadds in the ivar core (Sec. 4.1–4.3; Tables 1–2; Figs. 1–5). The results are clear and potentially very useful as a reproducible “sanity-check” baseline for external DR6 users, but several key interpretational and reproducibility details need tightening—especially the dependence/normalization in set–coadd tests, the exact masking/weighting/gradient implementation, the intended meaning of DR6 ivar (and null-map ivar), and basic robustness/scale-separation checks (Sec. 2–5).

## Strengths

- Well-scoped and practical contribution: simple, map-domain internal-consistency checks that can be run directly on public DR6.02 temperature and ivar products without harmonic-space machinery (Introduction; Sec. 1–3).
- Diagnostics are transparent and easy to reimplement: $\sigma_{\rm norm}$ (Eq. (1)), weighted variance, and a clearly labeled “roughness” proxy (Eq. (2)) that together probe complementary aspects of map differences (Sec. 3.1–3.4).
- Systematic coverage of multiple split types (day/night; four time splits; elevation nulls) and multiple arrays/frequencies (PA5 f090/f150; PA4 f150 cross-check) yields a coherent overall picture (Sec. 4.1–4.3).
- Balanced framing that (appropriately) avoids overclaiming about cosmological results and positions the work as a baseline for map-level users (Introduction; Sec. 5–6).
- Figures and tables convey the main outcomes clearly and consistently (Tables 1–2; Figs. 1–5), and the qualitative distinction between large-scale day–night structure and smaller-scale scan-structured null-el1 residuals is helpful (Sec. 4; Fig. 3).
- Eq. (1) is algebraically consistent under the stated independence/variance-additivity assumptions, and $z_i$ is dimensionless as written.
- The use of PA4 f150 as an independent cross-check for the null-el1 behavior is a strong design choice (Sec. 4.3).

## Major issues

1.  **Set–coadd tests are not statistically independent if the coadd includes the tested set, so the null expectation $\sigma_{\rm norm}=1$ used for interpreting set–coadd residual widths can be incorrect (Sec. 3.3; interpretation in Sec. 4.2).** This directly affects the quantitative meaning of the reported $\sigma_{\rm norm}$ excess in the set–coadd panel(s) and could bias conclusions about “ivar underprediction” for those particular tests.
    
    *Recommendation:* In Sec. 3.3, explicitly define how the “nighttime coadd” used in set–coadd comparisons is constructed: (a) coadd of all four sets (including the tested set), or (b) leave-one-out coadd excluding the tested set. Prefer (b) for a clean independence assumption. If (a) is kept, derive (and state) the expected variance of (set $-$ coadd) including the covariance term, and adjust the normalization/expectation for $\sigma_{\rm norm}$ accordingly. Update Fig. 2 / Sec. 4.2 text to interpret set–coadd values against the correct null expectation.

2.  **Several core methodology choices are under-specified, limiting exact reproducibility and blurring interpretation: masking beyond “positive-ivar support,” treatment of low-ivar/edge pixels, whether any monopole/dipole (or low-order mode) removal is applied before differences, and the exact weighting conventions for weighted means/standard deviations (Sec. 2; Sec. 3.1–3.4; Sec. 4.1–4.3).** Because $\sigma_{\rm norm}$ is sensitive to sky support and large-scale modes, these details materially matter.
    
    *Recommendation:* Add (or expand) a dedicated “Implementation details” subsection in Sec. 2–3 specifying: (i) the exact sky mask(s) used (or explicitly state none beyond common positive-ivar), including whether Galactic plane / bright-source masks are applied; (ii) any ivar thresholds or trimming (e.g., excluding boundary pixels, applying an ivar percentile cut) and how missing pixels are handled; (iii) whether mean/monopole (and/or dipole) is removed from maps or from each difference map prior to computing $\sigma_{\rm norm}$ and roughness; (iv) explicit formulas for weighted mean and weighted standard deviation (including which weight map is used: $w_A$, $w_B$, or the combined weight from Eq. (1), and whether any Bessel/effective-$N$ correction is applied). Provide a small snippet of pseudo-code or an appendix with the exact computation pipeline so others can reproduce the numbers in Tables 1–2 and Figs. 1–5 unambiguously.

3.  **The paper’s language sometimes implies “ivar miscalibration,” but $\sigma_{\rm norm}>1$ conflates multiple effects beyond an overall ivar scaling error: correlated (non-white) noise, mapmaking transfer-function differences between splits, beam mismatch, and residual sky-signal leakage into differences (Sec. 3.1; Sec. 5).** Without clarifying what DR6 ivar is intended to represent, it is ambiguous whether the results diagnose an ivar normalization problem or (more generally) the limits of a per-pixel white-noise model for map-to-map scatter.
    
    *Recommendation:* In Sec. 3.1 and Sec. 5, explicitly list the assumptions required for $\sigma_{\rm norm}\approx1$: independence of noise between splits, Gaussianity, correct per-pixel variance $1/{\tt ivar}$, and identical effective beam/transfer function between the compared maps. Then, in Sec. 2/Sec. 5, summarize (with citations to Naess et al. 2025 and any DR6 documentation) how DR6 ivar maps are constructed and what they are/are not intended to capture (e.g., white-noise pixel variance vs inclusion/exclusion of correlated noise and filtering). Adjust phrasing throughout to distinguish: “ivar underpredicts the variance of difference maps under an independence+white-noise model” from the stronger claim “ivar is miscalibrated,” unless you add evidence isolating a pure scaling error.

4.  **Beam/transfer-function mismatch and scale dependence are not controlled, yet the diagnostics are performed on raw maps at native pixelization with no explicit beam matching.** In pixel-space residuals, small differences in effective beam or filtering between splits can inflate $\sigma_{\rm norm}$ and the roughness metric even if power-spectrum analyses remain well behaved (Sec. 1; Sec. 4.1–4.3).
    
    *Recommendation:* Add one controlled, map-space scale test: repeat the headline $\sigma_{\rm norm}$ (day–night; at least one representative set–set; and std vs null-el1) after smoothing both maps to a common lower resolution (e.g., Gaussian $2'$–$5'$) and/or applying a mild low-pass filter, and report how $\sigma_{\rm norm}$ changes with smoothing scale. This can be presented as a small additional panel/figure or a short table in Sec. 4. If $\sigma_{\rm norm}$ remains high after smoothing, that supports large-scale/low-$\ell$ contributions; if it drops, that points to high-$\ell$/beam/transfer or pixel-scale structure.

5.  **Spatial dependence and robustness to sky-signal contamination are only qualitatively addressed.** Global $\sigma_{\rm norm}$ values over “positive-ivar” (and “top-10% ivar core” for null-el1) may hide localization to edges, specific scan regions, or foreground-contaminated areas, and may incorporate residual sky signal (e.g., foregrounds or variable sources) that does not cancel perfectly (Sec. 3.2–3.4; Sec. 4; Sec. 5).
    
    *Recommendation:* Augment Sec. 4 with one lightweight robustness set: (i) compute $\sigma_{\rm norm}$ (and null-el1 variance/roughness ratios) in several large sky tiles/patches and show the distribution or scatter across patches; (ii) repeat headline numbers with a conservative high-latitude/foreground-avoidance mask (or a simple bright-source mask / $|T|$ clipping) to bound sky-signal leakage; (iii) for null-el1, repeat ratios for alternative ivar-core thresholds (e.g., top $5\%$, $20\%$, $50\%$, and full positive-ivar) to demonstrate stability. These checks can remain fully map-domain and would substantially strengthen the attribution and user guidance.

6.  **Null-el1 comparisons need clearer specification of weights and of what the null-map ivar means.** Null maps are constructed by differencing subsets, so their noise properties and appropriate weighting are not necessarily comparable to standard coadds. As written, it is unclear which ivar is used for the weighted variance/roughness and why that choice yields an apples-to-apples comparison (Sec. 3.4; Sec. 4.3; Table 2; Fig. 5).
    
    *Recommendation:* In Sec. 3.4 / Sec. 4.3, explicitly state whether the null-el1 statistics use: (a) the null map’s ivar, (b) the standard coadd’s ivar, or (c) a combined/constructed weight map. Justify the choice given the null construction. Consider adding a parallel unweighted (or fixed-mask, fixed-weight) comparison over the same geometric region so that the std vs null differences in variance/roughness are not driven by differing weight definitions. Clarify in Table 2 and Fig. 5 captions what “weighted $\sigma_T$” and roughness are weighted by.

7.  **The manuscript provides no uncertainty estimates (error bars / confidence intervals) for $\sigma_{\rm norm}$, variance ratios, or roughness ratios, making it difficult to assess the significance of differences between frequencies, split types, or arrays, and to interpret apparent changes at the $\sim10$–$20\%$ level (Sec. 4; Tables 1–2; Figs. 1–5).**
    
    *Recommendation:* Add uncertainty estimates via a block jackknife/bootstrap over sky patches (e.g., CAR tiles or RA/Dec blocks) to account for spatial correlations and non-Gaussianity. Report error bars (or at least patch-to-patch scatter) for the headline $\sigma_{\rm norm}$ values and for the null-el1 ratios in Table 2 / Figs. 4–5. Include brief methodological details (tile size, number of patches, estimator) in Sec. 3 or figure captions.

## Minor issues

1.  Data/product description is too brief for non-expert reproducibility: AA coadd definition, exact DR6.02 product identifiers, units, and preprocessing (e.g., calibration convention, any mode removal) are not fully specified; map dimensions appear unclear/possibly corrupted; set0–set3 definitions are not described despite being central (Sec. 2; Sec. 3.3).
    
    *Recommendation:* Expand Sec. 2 to define AA coadds, list the exact DR6.02 filenames/version tags used, confirm units (e.g., $\mu{\rm K}_{\rm CMB}$), state pixel size and footprint in clear terms, and briefly describe the DR6 definitions of set0–set3 (temporal interleaving/season grouping and whether depths are comparable), with pointers to DR6 documentation.

2.  Figure captions are not fully self-contained and sometimes omit key processing/selection details (masking, weights, “median-ivar proxy,” definition of “null-el1,” definition of ivar-core, and whether maps are beam-matched). This reduces stand-alone interpretability (Figs. 1–5; Tables 1–2).
    
    *Recommendation:* Revise captions for Figs. 1–5 and Tables 1–2 to explicitly define: AA vs PA-specific products, set0–set3, null-el1, the mask used, the weight used, the ivar-core definition, and any mean subtraction/smoothing. Ensure consistent unit labeling ($\mu{\rm K}_{\rm CMB}$, $\mu{\rm K}$-arcmin) and consistent frequency notation (90/150 GHz vs f090/f150).

3.  Eq. (2) uses derivative notation ($\partial_xT$, $\partial_yT$) while the text indicates nearest-neighbor pixel differences; without dividing by pixel spacing it is not a true gradient, and the units should be clarified (Sec. 3.4; Table 2; Fig. 5).
    
    *Recommendation:* Either relabel the quantities as finite differences ($\Delta_xT$, $\Delta_yT$) or explicitly divide by the pixel size to form a true gradient and update units accordingly. State the finite-difference scheme (forward/central) and edge handling.

4.  Day–night “weighted mean difference consistent with zero” is stated without an uncertainty, and the corresponding statistic is not consistently reported for both frequencies/arrays (Sec. 4.1).
    
    *Recommendation:* Report the weighted mean day–night difference with an uncertainty estimate (e.g., patch jackknife standard error) for both 90 and 150 GHz (and/or for the main array shown), so the “consistent with zero” statement is auditable.

5.  Depth estimate from “median inverse-variance” in Table 1 lacks an explicit formula, including pixel-area conversion to $\mu{\rm K}$-arcmin (Sec. 2; Table 1).
    
    *Recommendation:* Provide the explicit conversion used from per-pixel ivar to depth in $\mu{\rm K}$-arcmin (including pixel size/area factors and whether the median is taken over a specific mask).

6.  The top-10% ivar-core choice for null-el1 tests is not justified (Sec. 3.4; Sec. 4.3).
    
    *Recommendation:* Add a short rationale (e.g., focusing on deepest/highest-quality regions while avoiding edge systematics) and, if not already added under Major Issues, include a brief sensitivity check across alternative percentiles.

7.  Qualitative “large-scale” vs “small-scale/scan-structured” statements would benefit from minimal quantitative scale markers (Sec. 4.1–4.3; Fig. 3).
    
    *Recommendation:* Add a simple quantitative indicator: e.g., recompute $\sigma_{\rm norm}$ after smoothing at a few scales (also addresses a Major Issue) and/or report a characteristic correlation length from map-space structure functions, without turning the paper into a full harmonic-space analysis.

8.  Discussion currently provides limited concrete guidance for typical DR6 map-level use cases (stacking, matched filters, map-level weighting, component separation), despite the stated “baseline for users” motivation (Sec. 5–6).
    
    *Recommendation:* Add a short, structured “Implications for users” subsection in Sec. 5–6: specify which results most directly impact pixel-level weighting (e.g., stacking/matched filtering) and suggest practical mitigations (e.g., empirical ivar rescaling by measured $\sigma_{\rm norm}$ in a chosen mask; preference for nighttime maps for certain applications; using patch-based empirical noise estimates). Clearly separate firm recommendations from speculative hypotheses.

9.  Context relative to prior ACT releases and other experiments is limited; readers may not know whether $\sigma_{\rm norm}\sim1.2$–$2.0$ is typical in map-space nulls (Introduction; Sec. 5).
    
    *Recommendation:* Add brief contextualization in the Introduction or Sec. 5 referencing relevant ACT DR4/DR5 internal-consistency or map-domain null discussions and, where applicable, similar map-domain checks from Planck/SPT. If direct comparisons are not available, state that explicitly and position the numbers as a baseline.

## Very minor issues

1.  References and citations appear inconsistent and may include placeholder entries (e.g., “(1)”), giving an unfinished impression (References; in-text citations in Sec. 1–6).
    
    *Recommendation:* Clean and standardize the References: remove placeholders, ensure uniform formatting, and verify every in-text citation has a complete entry (including Naess et al. 2025 and DR6 documentation for ivar/null-map construction).

2.  Formatting artifacts (all-caps strings that look like running headers, stray symbols like leading “#”, broken words likely from OCR/line wrapping) appear in the body text (Sec. 2; Sec. 4–5; general).
    
    *Recommendation:* Proofread the source to remove running-header text from the body, standardize section-heading formatting, and fix broken words/typographical artifacts; standardize unit typography (e.g., “150 GHz”, “$\mu{\rm K}_{\rm CMB}$”).

3.  Some figure design choices reduce accessibility (color-only encodings, small fonts, low-contrast annotations, potential overlap) (Figs. 1–5).
    
    *Recommendation:* Increase font sizes, ensure high-contrast annotation placement, and add non-color encodings (hatching/outlines) so plots remain legible in grayscale/for colorblind readers.

4.  Figure scaling choices (e.g., different $\pm$ ranges across panels in Fig. 3) may confuse quick comparisons if not explicitly noted.
    
    *Recommendation:* Explicitly state in captions when colorbar ranges differ between panels; optionally provide an additional row/panel with a common scale for the difference maps.

5.  Minor rounding inconsistencies in ratio reporting (second decimal place) can be confusing when readers attempt to reproduce table entries exactly (Table 2).
    
    *Recommendation:* State a single rounding rule (e.g., round-to-nearest at 2 decimals) or report ratios with one extra significant digit / exact numerator-denominator values in a footnote.


## Key statements and references

- ⚠ **Data Release 6 (DR6) of the Atacama Cosmology Telescope provides final Advanced ACTPol maps covering approximately $19{,}000~{\rm deg}^2$ of sky at 90, 150, and 220 GHz, along with associated products such as daytime/nighttime maps, four-way time splits, null-test maps, per-split beam transfer functions, and temperature-to-polarization leakage profiles, all made publicly available through the ACT DR6.02 data archive.**
  - _Reference(s):_ Naess et al. (2025)
  - _Justification:_ Naess et al. (2025) states that DR6 (DR6.02) provides Advanced ACTPol maps covering $\sim19{,}000~{\rm deg}^2$ at f090/f150/f220 (98/150/220 GHz), and releases day and night maps with four independent noise splits and dedicated null-test maps on LAMBDA (see abstract; section 4; table 9; sections 3.5–3.6). The paper describes beam estimation with per-split harmonic profiles and a beam model including temperature-to-polarization leakage (section 2.7; appendix B), but it does not explicitly document that per-split beam transfer functions or T$\rightarrow$P leakage profiles are distributed as DR6.02 archive products (they are not listed in table 9). Hence the claim is only partially supported.

- ✖ **The day-to-night depth ratios of $\times1.76$ at 90 GHz and $\times1.77$ at 150 GHz inferred from the median inverse-variance of the DR6 AA coadds are consistent with the expected sensitivity penalty from daytime atmospheric loading as characterized in the ACT DR6 data release.**
  - _Reference(s):_ Naess et al. (2025)
  - _Justification:_ Not supported. Naess et al. (2025) quantify day vs night sensitivity and depth and do not report day-to-night depth ratios near $1.76$–$1.77$. Table 6 shows day-time instantaneous sensitivities only $\sim5\%$ worse than night ($8.8$ vs $8.4~\mu{\rm K}\sqrt{\rm s}$ at 90 GHz; $9.4$ vs $8.9~\mu{\rm K}\sqrt{\rm s}$ at 150 GHz). Using the day and night total weights (Table 6: f090 $0.173$ vs $0.311$; f150 $0.156$ vs $0.288$), the implied day/night depth ratios are $\sim\sqrt{0.311/0.173}\approx1.34$ and $\sqrt{0.288/0.156}\approx1.36$, not $1.76$–$1.77$. Figure 7 also shows only modest improvement when adding day data to night (e.g., combined depth $14~\mu{\rm K}$ arcmin vs night-only $15$–$16$). The paper characterizes only a small day-time penalty from atmospheric loading, not factors of $\sim1.76$–$1.77$.

- ✔ **The ACT DR6 maps have already been used to obtain key cosmological results, including cosmological constraints from DR6 power spectra, component-separated CMB and Compton-y maps produced with a needlet internal linear combination method, and a $43\sigma$ detection of CMB lensing from DR6, as reported respectively in the DR6 cosmology, NILC, and lensing analyses.**
  - _Reference(s):_ Louis et al. (2025), Calabrese et al. (2025), Qu et al. (2024)
  - _Justification:_ Verification failed with gpt-5: Error code: 400 - {'error': {'message': 'Your input exceeds the context window of this model. Please adjust your input and try again.', 'type': 'invalid_request_error', 'param': 'input', 'code': 'context_length_exceeded'}}


## Mathematical consistency audit

This section audits **symbolic/analytic** mathematical consistency (algebra, derivations, dimensional/unit checks, definition consistency).

**Maths relevance:** light

The paper’s mathematics is limited to defining a normalized residual width for map differences (Eq. 1) and a pixel-scale roughness proxy (Eq. 2), plus qualitative reasoning about why different split comparisons should yield different residual widths. The core definitions are mostly consistent, with the main analytic concern being the null expectation for set$-$coadd comparisons if the coadd includes the tested set (correlated noise case).

### Checked items

1.  ✔ **Inverse-variance combination for a difference** (Eq. (1), Sec. 3.1, p.2)
    
    - **Claim:** Defines the effective inverse-variance weight for the difference $A-B$ as $w_i = \left(1/w_{A,i} + 1/w_{B,i}\right)^{-1}$.
    - **Checks:** algebra, definition consistency
    - **Verdict:** PASS; confidence: high; impact: moderate
    - **Assumptions/inputs:** $w_{A,i}$ and $w_{B,i}$ represent per-pixel inverse variances for maps $A$ and $B$, Noise in $A$ and $B$ is independent at fixed pixel $i$, Noise is Gaussian (for the stated unit-variance $z_i$ distribution)
    - **Notes:** If $\operatorname{Var}(A_i)=1/w_{A,i}$ and $\operatorname{Var}(B_i)=1/w_{B,i}$ and independence holds, then $\operatorname{Var}(A_i-B_i)=1/w_{A,i}+1/w_{B,i}$, so the inverse variance is exactly $w_i$ as defined.

2.  ✔ **Normalized residual is dimensionless** (Eq. (1), Sec. 3.1, p.2)
    
    - **Claim:** Defines $z_i=(A_i-B_i)\sqrt{w_i}$ and asserts $z_i$ should have unit variance under the null.
    - **Checks:** dimensional/units, sanity check
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** $A_i$ and $B_i$ have units of temperature (e.g., $\mu{\rm K}_{\rm CMB}$), $w_i$ has units of $1/({\rm temperature})^2$
    - **Notes:** $(A_i-B_i)$ has temperature units and $\sqrt{w_i}$ has $1/{\rm temperature}$ units, yielding dimensionless $z_i$. Under the stated null, each $z_i$ has $\operatorname{Var}=1$.

3.  ✔ **Null expectation $\sigma_{\rm norm}=1$ for heterogeneous weights** (Text following Eq. (1), Sec. 3.1, p.2)
    
    - **Claim:** Across pixels on common support, std($z$) should be 1 if weights correctly describe independent Gaussian noise.
    - **Checks:** probabilistic consistency, assumption checking
    - **Verdict:** PASS; confidence: medium; impact: minor
    - **Assumptions/inputs:** For each pixel $i$, $z_i$ has mean $0$ and variance $1$, Pixel-to-pixel correlations are negligible for the analytic expectation, The reported std($z$) is computed over a large set of pixels
    - **Notes:** Even with varying $w_i$ across pixels, standardizing each residual by $\sqrt{w_i}$ yields unit variance per pixel; the mixture over pixels preserves unit variance if correlations/mean shifts are negligible. The paper correctly frames this as a null-hypothesis expectation.

4.  ⚠ **Set$-$set vs set$-$coadd qualitative variance argument** (Sec. 4.2, p.3 (paragraph beginning “Panel (b) shows pairwise set$-$set…”))
    
    - **Claim:** Set$-$set $\sigma_{\rm norm}$ is larger than set$-$coadd $\sigma_{\rm norm}$ because both maps in set$-$set carry independent noise, while in set$-$coadd one map is a lower-noise coadd.
    - **Checks:** derivation logic, assumption consistency
    - **Verdict:** UNCERTAIN; confidence: medium; impact: moderate
    - **Assumptions/inputs:** The coadd is lower-noise than any individual set, Noise between different sets is independent, The set map and the coadd are treated as effectively independent for the expectation discussion
    - **Notes:** The argument is directionally reasonable if the coadd is independent of the tested set (e.g., a leave-one-out coadd). If the coadd includes the tested set (usual ‘all-splits’ coadd), then set and coadd share noise and the difference’s variance is reduced by covariance terms, changing the expected $\sigma_{\rm norm}$. The paper does not specify the coadd definition, so the analytic expectation cannot be verified.

5.  ⚠ **Set$-$coadd $\sigma_{\rm norm}$ null calibration** (Sec. 3.3 and Fig. 2 caption ($\sigma_{\rm norm}$ expectation), pp.2–3)
    
    - **Claim:** Uses the same $\sigma_{\rm norm}=1$ expectation for set$-$coadd comparisons as for other independent-map differences.
    - **Checks:** assumption checking, internal consistency
    - **Verdict:** UNCERTAIN; confidence: high; impact: moderate
    - **Assumptions/inputs:** The compared maps in Eq. (1) have independent noise realizations, The ivar maps correspond to those independent variances
    - **Notes:** If the coadd includes the set, independence fails and $\sigma_{\rm norm}=1$ is not the correct analytic expectation for set$-$coadd. This does not affect day$-$night or set$-$set comparisons (which are more plausibly independent), but it does affect interpretation of set$-$coadd widths.

6.  ⚠ **Roughness proxy definition** (Eq. (2), Sec. 3.4, p.2)
    
    - **Claim:** Defines $\sigma_{\rm grad} = \sqrt{\langle(\partial_xT)^2+(\partial_yT)^2\rangle_{\rm core}}$ computed using nearest-neighbor pixel differences.
    - **Checks:** notation consistency, dimensional/units
    - **Verdict:** UNCERTAIN; confidence: high; impact: minor
    - **Assumptions/inputs:** $\partial_xT$ and $\partial_yT$ are implemented as nearest-neighbor differences (as stated), Angle-per-pixel is fixed ($0.5$ arcmin) but may or may not be used in the calculation
    - **Notes:** Using $\partial$ notation suggests division by pixel spacing (yielding units of $\mu{\rm K}$ per arcmin/degree), but the reported units are $\mu{\rm K}$, consistent with using raw differences. Since the text calls it a ‘proxy’, this is not fatal, but the notation is inconsistent with the stated computation unless clarified.

7.  ⚠ **Top-10% ivar core restriction** (Sec. 3.4, p.2)
    
    - **Claim:** Computes weighted statistics in the ‘top-10% inverse-variance pixels’ region.
    - **Checks:** definition clarity, symbol/definition consistency
    - **Verdict:** UNCERTAIN; confidence: medium; impact: minor
    - **Assumptions/inputs:** There is a well-defined ivar map used to rank pixels, The same pixel mask is applied consistently within each comparison
    - **Notes:** The analytic definition of the ‘core’ depends on which ivar is used (standard map ivar, null map ivar, or a combination). The paper does not specify this, so the precise statistic is not fully defined symbolically.

8.  ⚠ **Map depth proxy from median inverse variance** (Sec. 2 and Table 1, p.2)
    
    - **Claim:** Reports map depth ($\mu{\rm K}$ arcmin) derived from median inverse-variance values.
    - **Checks:** dimensional/units, definition completeness
    - **Verdict:** UNCERTAIN; confidence: medium; impact: minor
    - **Assumptions/inputs:** Per-pixel ivar corresponds to inverse variance of the pixel temperature estimate, A conversion from per-pixel variance to $\mu{\rm K}$ arcmin exists and is applied consistently
    - **Notes:** No explicit formula is provided linking median ivar to $\mu{\rm K}$ arcmin depth (pixel-area factor and any conventions). This prevents a symbolic audit of that conversion, though it does not affect Eq. (1) or Eq. (2).

### Limitations

- Only the provided 6-page PDF text was available; no supplementary material or code definitions were available to disambiguate coadd construction or weighting conventions.
- The paper does not provide explicit formulas for the weighted mean/weighted standard deviation used in some reported statistics, limiting symbolic verification to the equations that are explicitly written.
- This audit does not assess numerical correctness of the reported $\sigma_{\rm norm}$ or ratio values, only analytic consistency of definitions and stated expectations.


## Numerical results audit

This section audits **numerical/empirical** consistency: reported metrics, experimental design, baseline comparisons, statistical evidence, leakage risks, and reproducibility.

Eight arithmetic ratio recomputations from printed table values were executed; all passed within the provided absolute/relative tolerances. Several ratios show small second-decimal differences consistent with rounding convention choices rather than substantive arithmetic errors.

### Checked items

1.  ✔ **C1_table1_depth_ratio_pa5_f090** (Page 2, Table 1 (PA5 f090 row))
    
    - **Claim:** Day-to-night depth ratio for PA5 f090 is $1.76$ given Night=$22.8~\mu{\rm K}$ arcmin and Day=$40.1~\mu{\rm K}$ arcmin.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $40.1/22.8=1.7587719298$ ($1.76$ to $2$ dp) vs reported $1.76$.

2.  ✔ **C2_table1_depth_ratio_pa5_f150** (Page 2, Table 1 (PA5 f150 row))
    
    - **Claim:** Day-to-night depth ratio for PA5 f150 is $1.77$ given Night=$26.6~\mu{\rm K}$ arcmin and Day=$47.2~\mu{\rm K}$ arcmin.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $47.2/26.6=1.7744360902$ ($1.77$ to $2$ dp) vs reported $1.77$.

3.  ✔ **C3_table2_ratio_weighted_sigmaT_pa5_f090** (Page 3, Table 2 (PA5 f090; Weighted $\sigma_T$))
    
    - **Claim:** For PA5 f090, null-el1 versus standard weighted $\sigma_T$ ratio is $1.30$ given Std=$125~\mu{\rm K}$ and Null=$162~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $162/125=1.296$ ($1.30$ to $2$ dp) vs reported $1.30$.

4.  ✔ **C4_table2_ratio_weighted_sigmaT_pa5_f150** (Page 3, Table 2 (PA5 f150; Weighted $\sigma_T$))
    
    - **Claim:** For PA5 f150, null-el1 versus standard weighted $\sigma_T$ ratio is $1.39$ given Std=$113~\mu{\rm K}$ and Null=$157~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $157/113=1.3893805310$ ($1.39$ to $2$ dp) vs reported $1.39$.

5.  ✔ **C5_table2_ratio_weighted_sigmaT_pa4_f150** (Page 3, Table 2 (PA4 f150; Weighted $\sigma_T$))
    
    - **Claim:** For PA4 f150, null-el1 versus standard weighted $\sigma_T$ ratio is $1.54$ given Std=$101~\mu{\rm K}$ and Null=$155~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $155/101=1.5346534653$ ($1.53$ to $2$ dp) vs reported $1.54$.

6.  ✔ **C6_table2_ratio_gradRMS_pa5_f090** (Page 3, Table 2 (PA5 f090; Gradient RMS))
    
    - **Claim:** For PA5 f090, null-el1 versus standard gradient RMS ratio is $1.67$ given Std=$53~\mu{\rm K}$ and Null=$89~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $89/53=1.6792452830$ ($1.68$ to $2$ dp) vs reported $1.67$; check allowed looser tolerance.

7.  ✔ **C7_table2_ratio_gradRMS_pa5_f150** (Page 3, Table 2 (PA5 f150; Gradient RMS))
    
    - **Claim:** For PA5 f150, null-el1 versus standard gradient RMS ratio is $1.72$ given Std=$60~\mu{\rm K}$ and Null=$103~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $103/60=1.7166666667$ ($1.72$ to $2$ dp) vs reported $1.72$.

8.  ✔ **C8_table2_ratio_gradRMS_pa4_f150** (Page 3, Table 2 (PA4 f150; Gradient RMS))
    
    - **Claim:** For PA4 f150, null-el1 versus standard gradient RMS ratio is $1.84$ given Std=$80~\mu{\rm K}$ and Null=$146~\mu{\rm K}$.
    - **Checks:** ratio_recompute
    - **Verdict:** PASS
    - **Notes:** Computed $146/80=1.825$ ($1.83$ to $2$ dp) vs reported $1.84$.

### Limitations

- Checks are restricted to arithmetic recomputations from explicitly printed numbers in the provided PDF text (e.g., ratios in tables).
- Values shown only in figures (bar heights, plotted ranges) are not used for fast verification because extracting numeric values from images/plots is out of scope.
- Claims requiring uncertainties, sample sizes, FITS file contents, or external ACT DR6 datasets cannot be verified from the PDF alone.
# SEEA-EA accounting for cocoa systems

Design notes for growing this app from a biodiversity map into a **UN SEEA-EA ecosystem accounting**
tool for cocoa production systems.

The accounting machinery comes from [`SchmidtDSE/unseea`](https://github.com/SchmidtDSE/unseea) — this
app contributes only bindings, parameter sets and vocabulary. Read
[`unseea/ARCHITECTURE.md`](https://github.com/SchmidtDSE/unseea/blob/main/ARCHITECTURE.md) first for the
composition rules, and the
[SEEALand walkthrough](https://schmidtdse.github.io/unseea/seealand.html) for what a completed set of
five accounts actually looks like.

> **Status: notes only.** No bindings or parameter sets yet — blocked on `unseea` Phase 1
> ([unseea#3](https://github.com/SchmidtDSE/unseea/issues/3)), the account library. Nothing here changes
> the deployed app.

## What this app becomes

Today: an agent-driven map over biodiversity layers for Peru and cacao — a good early framing of the
GLEN/geo-agent architecture, exploring base layers, but without the machinery to walk a user through a
SEEA analysis or produce the reports.

Next: the same app, plus the five SEEA accounts (extent → condition → services → monetary → asset) and
the scenario branch that recomputes them under a proposed land-use change. Built by extending and
replacing here rather than starting a new repo — this app already has the deployment, the feedback loop
and the layer selection.

What the app contributes, and nothing else:

| | Held here |
|---|---|
| **Binding manifest** | Which layer answers which ECT class / service / extent role |
| **Parameter set** | Anthropogenic reference levels for cocoa systems, ECT weights, valuation assumptions |
| **Vocabulary** | A sub-classification of IUCN GET T7.3 by production practice |

**No account logic.** If this app needs a change to the account library, the abstraction is wrong and
the fix belongs upstream in `unseea`. A missing *engine* capability goes further upstream into
`mcp-data-server`.

## The comparison to support

Two land-use options on the same parcel of tropical forest:

- **A — shade-grown, rainforest-friendly cocoa.** Retains canopy, native species present, lower yield per
  hectare.
- **B — full-sun intensive cocoa.** Cleared, higher yield per hectare, higher input dependence.

Scope is **physical**: extent, condition, and physical service flows. Monetary services and the asset
account are the partner's side, or joint. That is deliberate — it keeps the
ecosystem-contribution-versus-produced-capital attribution problem off the critical path.

Consequence to accept: with no monetary account there is no single aggregate, so results are a **vector
of physical indicators per ecosystem type**. More defensible anyway, and consistent with the rule that
condition cannot be averaged across ETs.

## Account by account

### 1 · Extent — the classification choice dominates

Is shade cocoa *forest* or a *plantation*? Under IUCN GET it sits in T7.3; under CGLS-LC100 it reads as
open forest (122/126) or cropland (40) depending on canopy density. The choice decides the ledger:

| Filed as | The change posts as |
|---|---|
| Modified forest — same ET, lower condition | **Degradation** |
| Distinct ET — plantation | **Conversion** |

Same ecology, different accounting entry. Full-sun clearing is unambiguous: managed reduction of forest,
managed expansion of cropland. This must be declared prominently — it moves the headline substantially
and is a convention rather than a measurement.

### 2 · Condition — where the case is actually made

**C1 landscape is the differentiator**, and the strongest available tooling. Shade cocoa retains canopy,
so forest area density across the k-ring holds up; full-sun clearing craters it. Measured upstream at
**3.4 s** for a country-scale accounting area at `k=3`, with `k ≤ 10` staying interactive.

The effect is **disproportionate to area lost and depends on siting** — a connected shade mosaic scores
very differently from the same hectares scattered as clearings. That is the actionable planning lever.

| ECT | Variable wanted | Available? |
|---|---|---|
| C1 landscape | Canopy / forest area density over a k-ring | ✅ computable now |
| B1 compositional | Native species presence | ⚠️ GLOBIO MSA is an *observed baseline*, not a practice response |
| B2 structural | Canopy cover, height, strata | ⏳ upstream gap |
| A2 chemical | Soil organic carbon | ⏳ upstream gap (SoilGrids) |
| B3 functional | Productivity | ⏳ upstream gap |
| A1 physical | — | ⏳ upstream gap |

⚠️ **ECT coverage is 1–2 of 6 classes today.** An index built on a subset is not comparable to one built
on a different subset, and unmeasured classes silently reweight the rest. Every result must carry its
coverage, and any comparison must be restated on the intersecting classes.

### 3 · Services (physical)

Cocoa provisioning (yields differ by practice, partner-supplied); global climate regulation from retained
carbon; water regulation and erosion control, both favouring shade systems.

⚠️ **Pollination is a double-counting trap.** Cocoa is midge-pollinated, so pollination feeding the cocoa
crop is an **intermediate** service. Counting pollination *and* cocoa provisioning double counts
(SEEA EA §6.2.3). Pick one and declare it.

## What is a data gap versus a framework limit

This distinction matters more than any other here, and the answer is more encouraging than expected.

### ✅ Compatible — the standard already does this

SEEALand's own **cropland** condition variables are organic-farming share, crop diversity, farmland bird
richness and semi-natural vegetation share, scored against an **anthropogenic** reference level.
Distinguishing a lower-impact production system from an intensive one on canopy retention, native species
and surrounding semi-natural cover is therefore **not a stretch of SEEA — it is the pattern SEEALand
demonstrates**, applied to a different crop.

Also compatible: sub-classifying IUCN GET T7.3 by practice, and **acoustic monitoring or eDNA as B1
compositional variables** — SEEA is method-agnostic about how a variable is measured, requiring only that
it be rescaled against a declared reference. Those would replace the weakest link above, and need no
architectural change: a new binding and a reference level.

### 📊 Data gaps — real, but nothing conceptual in the way

1. **Practice-differentiated land cover.** CGLS-LC100 class 40 is one bucket. Likely partner-supplied.
2. **Anthropogenic reference levels for cocoa systems.** Where review will push hardest.
3. **Practice-response coefficients** — our layers are observed baselines, not response functions.
4. **GLOBIO's underlying land-use MSA coefficients** rather than the baked raster. Would convert B1 from
   a static baseline into a response function in one step. Worth chasing.

### ⚠️ Framework limits — three walls

1. **"Avoided degradation" is not an accounting entry.** The asset account has no avoided-loss line.
   Compile *both* scenarios and present the pair plus their difference; the counterfactual lives in the
   framing, never in a ledger line.
2. **No single landscape condition score.** Condition cannot be averaged across ETs with different
   reference conditions. Report per ET.
3. **An account records an actual period.** Forward-looking comparison is an *application* of the
   machinery — label outputs scenario projections.

## Division of labour

| We provide | Partner provides |
|---|---|
| Spatial baseline: extent, condition, carbon | Practice → condition-variable response coefficients |
| Accounting machinery, recompiling in seconds | Yield trajectories over the asset life |
| Landscape-configuration metrics at any *k* | Input costs, if monetary accounts are wanted |
| Reconciling, auditable, exportable tables | The ET classification call for shade cocoa |

## Limitations to state in any output

- **Resolution.** Res 9 ≈ 10.5 ha; MSA at res 8 ≈ 46 ha; smallholder plots are 1–3 ha. **This is a
  jurisdictional instrument, not a parcel-audit tool.** Matches where certification is heading; does not
  match farm-level verification.
- **Scenario, not account.** Label projections as such.
- **No avoided-loss claim.**
- **Invented reference levels.** Declare them, and show results across the plausible range rather than
  defending one point estimate. Under linear rescaling, moving a reference bound **cannot reorder
  scenarios** — only a non-linear rescaling can — so ranking conclusions are considerably more robust
  than level conclusions.

## ⚠️ Licence: this app currently loads an NC layer

`layers-input.json` includes **`irrecoverable-carbon`, which is CC BY-NC 4.0.**

There is no commercial relationship here, so the question is not "are we commercial" — it is **what
obligation we propagate**. CC BY-NC passes its restriction into derived research, data and tools, so any
account built on that layer is **NC-encumbered** and downstream users inherit the restriction.

Usable and honest; a problem only if unstated. Two acceptable resolutions:

1. Keep it and **label outputs NC-encumbered**, so users know what they would need to license separately.
2. Switch global climate regulation to the **IPCC Tier 1 carbon lookup**, already the plan upstream
   ([unseea#21](https://github.com/SchmidtDSE/unseea/issues/21)) — which removes the NC dependency
   entirely. That issue is a licence fix as much as a methods fix.

## Open questions, ordered by how much depends on them

1. **Is shade cocoa the same ecosystem type as forest, or a different one?** Blocks the extent account
   and everything downstream. Candidate resolution: sub-classify T7.3 by practice, and report the
   alternative filing as a sensitivity.
2. **NC obligation — accept and label, or switch to IPCC Tier 1?** Blocks the climate-regulation binding.
3. **Which reference levels, and from whom?** Blocks the condition account. Preference order:
   partner-supplied → ambient distributions (e.g. ecoregion 95th percentile) → prescribed expert
   judgement, declared as such.
4. **Pollination — counted, or excluded as intermediate?** Blocks the physical services account.
5. **Physical accounts only, or monetary too?** Load-bearing for scope.
6. **What is the accounting area?** Must be jurisdictional, not parcel-level — a constraint to
   communicate early, since partners often want farm-level answers.

## Upstream references

Generic findings live in `unseea` and are deliberately **not** duplicated here:

| Finding | Home |
|---|---|
| Reference levels are not counterfactuals; "avoided degradation" is not an accounting entry | `unseea` DESIGN.md §5.4 |
| SEEALand already scores agricultural practice — the working-lands precedent | `unseea` DESIGN.md §5.4 |
| ECT weight dilution from unmeasured classes | `unseea` DESIGN.md §2.4 |
| Reference-level sensitivity and the linear-rescaling invariance result | `unseea` DESIGN.md §5.3 |
| Resolution floor: jurisdictional yes, parcel no | `unseea` DESIGN.md §5.5 |
| Practice-response coefficients as a general gap class | `unseea` DESIGN.md §5.4 |

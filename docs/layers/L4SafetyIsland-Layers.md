# L4 Safety Island — What Changes in Autoware, in Five Phases

**A progressive series from the published Autoware node diagram to the Safety Island alone.**
Derived from design version 20260918. Nothing here modifies the design document.

The series exists because "what does the Safety Island change?" cannot be answered at one zoom level.
At node level the change is invisible in the noise of ninety boxes; at stack level the change is
obvious but the argument is unsupported. So the same picture is drawn five times, each one zoom step
from the last.

**Colour means the same thing on every phase**: the Autoware stack a node belongs to, taken from the
legend of [ArchitectureMain.pdf](../ArchitectureMain.pdf). Only the zoom changes, and — from phase 3 onward — which ECU each
box runs on.

| Phase | What it shows | Source |
| :-- | :-- | :-- |
| **1** | Autoware today, every node | `L4SafetyIsland-Layer1-AutowareToday.tex` |
| **2** | Autoware today, one box per stack | `L4SafetyIsland-Layer2-AutowareAbstract.tex` |
| **3** | The same picture, after the island | `L4SafetyIsland-Layer3-IslandAbstract.tex` |
| **4** | What changed, at node level | `L4SafetyIsland-Layer4-ChangedNodes.tex` |
| **5** | The Safety Island alone | `L4SafetyIsland-Layer5-IslandNodes.tex` |
| **5 · B1–B4** | Phase 5 once per behaviour | `L4SafetyIsland-Layer6-B1-DegradeADS.tex` … `-B4-EmergencyStop.tex` |
| — | All five on uniform landscape pages | `L4SafetyIsland-Layers.tex` → `.pdf` |

**Phase 1 is redrawn, phase 4 is reused.** Phase 1 restates the published Autoware node graph
([../ArchitectureMain.pdf](../ArchitectureMain.pdf)) in this series' own visual language, because a first page in a different
drawing style breaks the progression — the eye reads the change of style as a change of content.
Phase 4 is the node diagram already built in [L4SafetyIsland-Layer4-ChangedNodes.md](L4SafetyIsland-Layer4-ChangedNodes.md); duplicating it here would fork the
source of truth.

---

## What each phase is for

**Phase 1 — Autoware today, every node.** The starting point, and the thing everyone already has in
their head: the published node graph, coloured by stack and wired by topic, redrawn here so that the
five pages share one visual language. Nothing on it is changed by this design — that is the point of
drawing it. Two condensations are noted in its legend: the per-sensor lidar pre-processing chain is
drawn once rather than once per sensor, and the two behaviour planners' module lists are given inside
their box rather than as separate boxes.

**Phase 2 — one box per stack.** Every node of phase 1 merged into the stack it belongs to, and every
link merged with it. Nine boxes, about twenty arrows. This is the picture the island is going to
change, and it is drawn separately so that phase 3 can be laid over it without anything else moving.

**Phase 3 — the same picture, after the island.** Same boxes, same colours, same level of detail. The
change is then a small number of readable facts:

- **The Control stack and the Vehicle Interface leave the HPC**, drawn as a dashed red migration arrow
  from the ghost they leave behind down to the island band. That is 18 Autoware nodes, unchanged as
  code (§4.1).
- **The island grows reduced versions of the other stacks** — its own sensing, its own localization
  (one node), its own perception, its own planning, its own system.
- **The HPC gains an interface block** (H1–H8) whose only job is to make the link narrow and typed.
- **One link joins the two**, eleven signals in and nine out.

The thing phase 3 is built to show is that the island is *not a monitor bolted on the side*. It runs a
reduced Autoware, and the expensive parts are not duplicated at all.

**Phase 4 — what changed, at node level.** A *selective* zoom, and the reason the series has five
phases rather than three. It opens up **only what the island changes**, and leaves everything else at
the phase-3 level:

- **On the HPC**, node level for the nodes that changed — what was **removed** (the control stack that
  left), what is **re-sourced** (same node, new source or new role: `motion_velocity_optimizer`,
  `external_cmd_selector`, `mrm_comfortable_stop_operator`, `diagnostic_graph_aggregator`,
  `default_adapi_universe`) and what is **new** (the H1–H9 interface block). Every stack the island
  leaves alone — sensing, localization, perception, map, planning, the monitors and checkers — stays
  as a single box.
- **On the island**, node level for the nodes **migrated from the HPC** (the I-nodes). The island's own
  new stacks stay as one box each, exactly as in phase 3; phase 5 is where they open.

A heavy box rule means "a whole stack, unchanged"; a thin rule means "a single node, opened up because
it changed". Its own companion catalogue is [L4SafetyIsland-Layer4-ChangedNodes.md](L4SafetyIsland-Layer4-ChangedNodes.md).

**Phase 5 — the Safety Island alone.** The HPC dropped entirely so that all 37 island nodes (18
migrated + 19 new, plus the optional N20) have room to be read at full size, in [ArchitectureMain.pdf](../ArchitectureMain.pdf)
style. Every edge on it is catalogued in [../messageflow/](../messageflow/). Each node carries the behaviours it
enables, from the §3.3 table: a filled **B1–B4** tag, **SHARED** for I2, I15 and N12 (every behaviour
runs through them), a hollow tag for N15 (on B2's and B3's path by §3.1, absent from §3.3), and ⊘ for
the nodes no behaviour uses (I9–I12, I14, N20).

**Phase 5 · B1–B4 — one page per behaviour.** The phase-5 drawing, unchanged, four times. What the
behaviour runs on (§3.3) is in full colour, what it only passes through — a SHARED node, or a node
§3.1 puts on its path — is half-tone, and everything else is faded; an edge is drawn at the weaker of
its two ends. The lower-left panel lists the behaviour's Detect · Decide · Act · Assure nodes and its
trigger, precondition, response and exit. The drawing lives once, in `islandnodes-body.tex` with its
styles in `islandnodes-style.tex`; phase 5 and the four views only set the title, the panel and which
nodes are lit.

---

## Drawing conventions

Two rules are enforced on every phase drawn here, because both were asked for explicitly:

1. **No label touches an arrow.** Every edge label is placed *beside* its own line — above, below, or
   to one side — never on it, and never masked by a white patch. The clearance is set once, in
   `silayers.tex`, as the `lab` / `labs` inner separation.
2. **Arrow direction is the direction of the data.** Feedback paths (vehicle → interface → control,
   actuator → supervisor) are drawn as their own arrows in their own lane and greyed, rather than
   given a second head on the forward arrow.

`silayers.tex` holds the shared palette and styles, so a box keeps its colour and its size across
phases 1, 2, 3 and 5 — and phase 4 matches them by using the same values.

---

## Building

```sh
cd layers
latexmk -pdf L4SafetyIsland-Layer1-AutowareToday.tex
latexmk -pdf L4SafetyIsland-Layer2-AutowareAbstract.tex
latexmk -pdf L4SafetyIsland-Layer3-IslandAbstract.tex
latexmk -pdf L4SafetyIsland-Layer5-IslandNodes.tex
for f in L4SafetyIsland-Layer6-B*.tex; do latexmk -pdf $f; done
latexmk -pdf L4SafetyIsland-Layers.tex     # collects all five, needs phase 4 built
```

Phase 4 comes from [L4SafetyIsland-Layer4-ChangedNodes.md](L4SafetyIsland-Layer4-ChangedNodes.md); build it there first if it is missing.

---

## Tag vocabulary

All five phases now use the same three words for what happened to a box: **MIGRATED** (the same
Autoware package, now executing on the island), **NEW** (does not exist in Autoware today) and
**RE-SOURCED** (the node stays on the HPC, but its sources or its role change). They are defined once
in `silayers.tex` and repeated in each phase's own legend.

# CircuitTier: Multi-Signal Interpretability for Efficient Model Compression

**Anonymous submission — ACL 2026 Short Paper**

---

## 🔬 Interactive Circuit Explorer

**We strongly encourage reviewers to try our interactive 3D circuit visualization:**

👉 **[Open Circuit Explorer](https://anonymous.4open.science/w/circuit-tier-anon-188F/explorer_final%20(1).html)** *(download and open in any browser — no install required)*

This self-contained HTML file lets you explore the full Gemma-2-2B circuit identified by CircuitTier. Features include:

- **3D layer-by-layer visualization** of all 26 transformer layers with MLP neurons and attention heads
- **Four-tier color coding**: Skeleton (red, 16-bit) → Supporting (orange, 8-bit) → Compressible (blue, 4-bit) → Prunable (gray, 0-bit)
- **Six signal overlays**: Toggle between EAP, Gradient, Magnitude, Weight Δ, Activation Δ, and Edge scores to see how each signal independently identifies different critical components
- **X-RAY mode**: Strip away compressible neurons to reveal the sparse skeleton circuit underneath
- **Guided tour**: Step-by-step walkthrough of key findings (click "Tour")
- **Hover inspection**: Click any neuron or attention head for detailed signal scores

The explorer makes the paper's central claim visually immediate: the six signals are near-orthogonal (mean Jaccard overlap 0.005), and their union identifies a sparse but distributed skeleton that no single signal could find alone.

> **Tip:** Try toggling between signal overlays to see how top neurons shift dramatically across signals — this is why multi-signal fusion matters.

---

## Repository Contents

```
├── circuit_explorer.html    # Interactive 3D visualizer (open in browser)
├── figures/                 # All paper figures
│   ├── figA_correlation.png
│   ├── figA1_signal_overlap.png
│   ├── figA2_reliability.png
│   ├── figA3_layer_distribution.png
│   ├── figA4_signal_ablation.png
│   ├── figA5_gptq_vs_naive.png
│   ├── fig1_pareto_curve.png
│   └── fig2_pareto_curve.png
└── README.md
```

## Key Results

| Method | Avg. Bits | Compression | Retention |
|--------|-----------|-------------|-----------|
| Uniform 4-bit | 4.00 | 4.0× | 75.2% |
| GPTQ 4-bit | 4.00 | 4.0× | 79.0% |
| AWQ 4-bit | 4.00 | 4.0× | 85.1% |
| **CircuitTier c4+a8** | **4.75** | **3.35×** | **96.2%** |
| **CircuitTier 75%@3b+a8** | **4.16** | **3.84×** | **99.0%** |

CircuitTier preserves 96–99% of task performance while compressing 3.35–3.84×, outperforming all baselines by 11–24 percentage points.

## Method Overview

CircuitTier identifies task-critical circuits through six complementary interpretability signals, then assigns mixed-precision bit-widths via a four-tier framework:

1. **Signal Extraction** (~65 min on A100): EAP attribution, gradient sensitivity, magnitude, weight delta, activation delta, edge scores
2. **Tier Assignment**: Neurons meeting ≥3 signal thresholds → Skeleton (16-bit); ≥1 + causal validation → Supporting (8-bit); default → Compressible (4-bit); all-low → Prunable (0-bit)
3. **VulnSplit Quantization** (~7 min): Apply GPTQ per-tier with vulnerability-aware calibration

Total pipeline: ~72 minutes on a single A100 GPU.

# Null models for bipartite community detection

An interactive, single-file web demo comparing two null models for modularity-based community detection in bipartite networks:

- **Newman–Girvan (NG)**: *P<sub>ij</sub> = k<sub>i</sub>k<sub>j</sub> / 2m* for every node pair, including same-type pairs that can never be connected in a bipartite network.
- **Barber (BB)**: *P<sub>ij</sub> = k<sub>i</sub>k<sub>j</sub> / m* for pairs of different types, and 0 for pairs of the same type.

The demo reproduces the model-network experiments (Figs. 2–4) and the language–country network example (Fig. 5) of S. H. Lee, *J. Korean Phys. Soc.* **79**, 1190 (2021). That paper shows that the bipartite-aware null model recovers the planted communities over a wider range of the resolution parameter γ.

*Created by Claude Opus 5.5 (Anthropic).*

## What the demo does

1. **Builds a bipartite network with planted communities.** Three presets follow the paper:
   - **3 vs 3**: three groups per node type. The top and bottom groups pair up across types (*p*<sub>in</sub>), unpaired groups connect sparsely (*p*<sub>out</sub>), and the middle groups connect evenly to everything (*p*<sub>mid</sub>).
   - **3 vs 2**: three circle groups and two square groups. The middle circle group belongs to neither community.
   - **3 vs 2, big–small**: the middle circle group leans toward the lower square group, which produces two communities of different sizes.

   You can adjust the connection probabilities, group size and random seed.

   A fourth option loads a **real network**: languages and the countries where they are spoken, from the KONECT Unicode languages dataset (Ref. [36] in the paper). It has 254 countries and 614 languages, with 1,255 edges. Each edge is weighted by the share of a country's population that reads and writes the language. The demo uses these weights. The 149 zero-weight edges are left out, which doesn't change modularity, so 1,106 weighted edges remain.

2. **Sweeps the resolution γ.** At each γ it runs a randomized Louvain search repeatedly with each null model. The node order is shuffled on every run, so the runs can give different partitions.

3. **Measures consistency.** For each γ it computes:
   - the **partition inconsistency** Ω of the ensemble of partitions (Lee *et al.*, 2021). Ω = 1 when every run agrees.
   - the average number of communities.
   - the fraction of runs that find exactly the planted two communities.

   Similarity between partitions uses **element-centric similarity** (Gates *et al.*, 2019). For the clique-induced element graph, personalized PageRank has a closed form, and the damping factor cancels. As a result, the similarity is computed exactly with one pass over the nodes per pair of partitions.

4. **Visualizes the results.**
   - The charts plot Ω and the community count against γ, in the style of the paper.
   - For model networks, a shaded band marks the γ window where at least 90% of runs recover the planted split.
   - The language–country network has no ground truth. There the band marks the longest stretch of γ with Ω ≤ 1.02, and the panel also reports the sharpest jump in community count for γ ≥ 1.
   - For a γ picked on the chart or the slider, the network drawings show the most likely partition. Node size scales with each node's local inconsistency.
   - For the language–country network, the drawing arranges countries (left) and languages (right) in horizontal bands, one per community. The largest communities are listed by ISO country codes and language codes.

   With the default settings the language–country network reproduces the paper's main observation. With the BB null model, the community count jumps from about 90 to about 125 near γ ≈ 3.8, then stays at low inconsistency up to γ ≈ 5. With NG, the count rises gradually and the low-inconsistency stretch is shorter.

## Implementation notes

- **Null model in Louvain.** The implementation is plain JavaScript. When a node, or a merged super-node, with type-specific degrees (*a*<sub>0</sub>, *a*<sub>1</sub>) moves into a community with type-specific total degrees (*T*<sub>0</sub>, *T*<sub>1</sub>), the null-model penalty is:
  - *γ (a<sub>0</sub>+a<sub>1</sub>)(T<sub>0</sub>+T<sub>1</sub>) / 2m* for NG
  - *γ · 2(a<sub>0</sub>T<sub>1</sub> + a<sub>1</sub>T<sub>0</sub>) / 2m* for BB

  Keeping the two degrees separate makes the BB penalty exact at every aggregation level.
- **Normalization.** The BB term uses Barber's normalization, *k<sub>i</sub>k<sub>j</sub>/m* on pairs of different types. Both null models then expect the same total number of edges, so their γ axes line up, as they do in the published figures.
- **Differences from the paper.** This is not GenLouvain, so the exact γ positions differ a little from the paper. The paper's disease–gene and ecological mutualistic networks are not included.
- **Weighted networks.** For the language–country network, the node degrees *k<sub>i</sub>* in both null models are strengths, meaning the sum of edge weights. The total *2m* is the sum of all strengths.
- **Reproducibility.** Network generation and every Louvain run are seeded, so the same settings always give the same results.

## References

1. S. H. Lee, "Refinement for community structures of bipartite networks," *J. Korean Phys. Soc.* **79**, 1190 (2021). [doi:10.1007/s40042-021-00352-3](https://doi.org/10.1007/s40042-021-00352-3)
2. M. J. Barber, "Modularity and community detection in bipartite networks," *Phys. Rev. E* **76**, 066102 (2007). [doi:10.1103/PhysRevE.76.066102](https://doi.org/10.1103/PhysRevE.76.066102)
3. M. E. J. Newman and M. Girvan, "Finding and evaluating community structure in networks," *Phys. Rev. E* **69**, 026113 (2004).
4. D. Lee, S. H. Lee, B. J. Kim, and H. Kim, "Consistency landscape of network communities," *Phys. Rev. E* **103**, 052306 (2021).
5. A. J. Gates, I. B. Wood, W. P. Hetrick, and Y.-Y. Ahn, "Element-centric clustering comparison unifies overlaps and hierarchy," *Sci. Rep.* **9**, 8574 (2019).
6. J. Kunegis, Unicode languages network dataset, KONECT (2016). [konect.cc/networks/unicodelang](http://konect.cc/networks/unicodelang/). Source data: [Unicode CLDR territory–language information](http://www.unicode.org/cldr/charts/25/supplemental/territory_language_information.html).
7. V. D. Blondel, J.-L. Guillaume, R. Lambiotte, and E. Lefebvre, "Fast unfolding of communities in large networks," *J. Stat. Mech.* P10008 (2008).

## Credits

Created by Claude Opus 5.5 (Anthropic), based on the paper by Sang Hoon Lee.

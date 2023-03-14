# (230215-bioRxiv) A Single-cell Perturbation Landscape of Colonic Stem Cell Polarisation - P1

Cancer cells are regulated by oncogenic mutations and microenvironmental signals, yet these processes are often studied separately😕. To functionally map how **cell-intrinsic** and **cell-extrinsic** cues co-regulate cell-fate in colorectal cancer (CRC), we performed a systematic single-cell analysis of 1,071 colonic organoid cultures regulated by...
<!--more-->

# A Single-cell Perturbation Landscape of Colonic Stem Cell Polarisation
{{< admonition type=warning title="NOTE" open=true >}}
It will take several days to finish this journal club
{{< /admonition >}}

> Qin et.al | bioRxiv | 2023.02.15.528008 | doi: https://doi.org/10.1101/2023.02.15.528008

{{< admonition type=tip title="Brief" open=true >}}
Here we describe a functional single-cell study exploring **how cell-extrinsic and cell-intrinsic cues 
co-regulate colonic epithelial fate**.
{{< /admonition >}}
<hr>

## Author
<hr>

**Leader:** Christopher J. Tape | UCL Cancer Institute

**Focus on:** How the different cell types in cancer communicate with one another to drive tumours

**Work with:**  Organoid | scRNA-Seq | sc-MS

**Website:** The Cell Communication Lab: http://tape-lab.com/

## Background 
<hr>

{{< admonition type=note title="Intestinal cells" open=true >}}
### Cell type:
> **Ref:** <br>
[<u>2021, Nat Rev Mol Cell Biol</u>](https://www.nature.com/articles/s41580-020-0278-0)<br>
[<u>2019, Nat Rev Gastroenterol Hepatol</u>](https://www.nature.com/articles/s41575-018-0081-y)<br>
[<u>2010, Transl Res</u>](https://www.ncbi.nlm.nih.gov/pmc/articles/pmid/20801415/)<br>
[<u>2021, Cell Regen</u>](https://cellregeneration.springeropen.com/articles/10.1186/s13619-020-00061-5)<br>
[<u>2020, Immunology</u>](https://onlinelibrary.wiley.com/doi/10.1111/imm.13191)<br>
[<u>2021, Front Physiol</u>](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8317205/)<br>
[<u>2022, Nat Rev Gastroenterol Hepatol</u>](https://www.nature.com/articles/s41575-021-00553-y)<br>
[<u>2021, Nature</u>](https://www.nature.com/articles/s41586-021-03852-1)<br>
[<u>2021, Cell</u>](https://www.sciencedirect.com/science/article/pii/S009286742031686X)<br>

{{< image src="/img/230215-paper/Intestinal structure.webp" caption="**Intestinal structure**" src_s="/images/230215-paper/Intestinal structure.webp">}}

1. **Intestinal stem cell (ISC):** 
* i. `Crypt base columnar (CBC) cells`: canonical/traditional/rapidly-cycling stem cell **|** generation of multiple lineages and long-term self-renewal **|** bottom of the crypt; interspersed between Paneth cells **|** LGR5+ OLFM4+ 
* ii. `+4 cells/Label-retaining cells (LRC)`: reserved/precursor/quiescent stem cell population [2013, Nature](https://www.nature.com/articles/nature11965) **|** +4 means located at the ~+4 from the crypt base;label-retaining means take advantage of these cells slow-cycling characteristic to take LRC approach (e.g. EdU labeling), a lineage tracing method, and LRCs are cells that retain a DNA synthesis label after a prolonged chase period **|** committed to mature into Paneth cells and EECs, but retain the ability to reacquire stem-cell function. At homeostatic/normal intestinal tissue they do not contribute cell turnover but can repopulate the crypt upon damage.
2. **progenitor cells:**
also named as transit-amplifying cells(TACs) (not accurate) **|** more specific than stem cells and can only be pushed to differentiate into its "target" cell type.
---

<b><center><font color=#5D9C59>intestinal stem cell niche (microenvironment)</font></center></b>

> Adult stem cells usually reside in a specialized niche microenvironment (niche cells and the niche signals they produce), which provides necessary extracellular signals and substances to promote the long-term maintenance and self-renewal of stem cells 

> The epithelium and mesenchyme cooperate to generate the complex dynamic observed in intestinal crypts. Together they provide the key signals that regulate intestinal fate determination: `WNT`, `Notch`, `EGF` and `BMP` signalling

3. **epithelium cell:**
epithelium cell (especially Paneth cell) constitute the epithelial niche
* i. Secretory lineage
  * (1) Paneth cells: 
  * (2) Goblet cells
  * (3) Enteroendocrine cells
  * (4) Tuft cells
* ii. Absorptive lineage
  * (1) Enterocytes
  * (2) M cells

here is epithelium cell specification: <br>
{{< image src="/img/230215-paper/cell specification.webp" caption="**Cell specification**" >}}

4. **Intestinal mesenchymal (stromal) cell (IMC):**
1) Naming ambiguity: mesenchymal cell (MC) or stromal cell **|** mesenchymal stem cell (MSC) or mesenchymal stromal cell😤 <br>
2) cancer-associated transdifferentiation: transdifferentiation of epithelial cells to mesenchymal cells (**EMT**); transdifferentiation of endothelial to mesenchymal cells (**EndMT**); transdifferentiation of mesothelial cells to mesenchymal cells (**MMT**) <br>
3) Most of IMC originates from the mesoderm and differentiates from mesenchymal stem cells, locate on subepithelial compartment, constitute the mesenchymal niche.<br>
`NOTE:` for CRC IMC, here is a review:[2017, Gastroenterology](https://pubmed.ncbi.nlm.nih.gov/28111227/)<br>
4) subtypes: pericyte; myofibroblasts; fibroblast...; (Foxl1+ MCs; Gli1+ MCs...) <br>

5. **Other cell types:**
i. Immune cells: ..<br>
ii. Endothelial cells: <br>
iii. Mesothelial cells: some papers seem classify mesothelial cells as mesenchymal (stromal) cell..?😤 <br>
iv. intestinal neural cells: intestinal has its typical nervous system called intrinsic enteric nervous system (ENS): neuron and enteric glial cells (EGC)... <br>
...

{{< /admonition >}}

---

> Author: Gaux Wang  
> URL: https://dyinboisry4u.github.io/230226/  


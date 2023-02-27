# Load cellbender filtered data to scanpy

# Load cellbender filtered data to scanpy
> Fix cellbender data loading problems
---
## Problems
---
On `cellbender v0.2,2`, here are 2 questions: 
1. Can not load cb output with `sc.read_10_h5`: `scanpy v1.9.1` complains `ValueError: Illegal slicing argument for scalar dataspace`</br>
**Releated issues:** </br>
i. https://github.com/broadinstitute/CellBender/issues/174 </br>
ii. https://github.com/broadinstitute/CellBender/issues/128
2. CB filter data include "cells" with `zero counts` </br>
**Releated issues:** </br>
https://github.com/broadinstitute/CellBender/issues/111 </br>

---
## Fix
---

> 1. Load data

Load data with below function `anndata_from_h5`: </br>
```python
import tables
import numpy as np
import scipy.sparse as sp
import anndata
from typing import Dict, Optional


def anndata_from_h5(file: str,
                    analyzed_barcodes_only: bool = True) -> 'anndata.AnnData':
    """Load an output h5 file into an AnnData object for downstream work.

    Args:
        file: The h5 file
        analyzed_barcodes_only: False to load all barcodes, so that the size of
            the AnnData object will match the size of the input raw count matrix.
            True to load a limited set of barcodes: only those analyzed by the
            algorithm. This allows relevant latent variables to be loaded
            properly into adata.obs and adata.obsm, rather than adata.uns.

    Returns:
        adata: The anndata object, populated with inferred latent variables
            and metadata.

    """

    d = dict_from_h5(file)
    X = sp.csc_matrix((d.pop('data'), d.pop('indices'), d.pop('indptr')),
                      shape=d.pop('shape')).transpose().tocsr()

    # check and see if we have barcode index annotations, and if the file is filtered
    barcode_key = [k for k in d.keys() if (('barcode' in k) and ('ind' in k))]
    if len(barcode_key) > 0:
        max_barcode_ind = d[barcode_key[0]].max()
        filtered_file = (max_barcode_ind >= X.shape[0])
    else:
        filtered_file = True

    if analyzed_barcodes_only:
        if filtered_file:
            # filtered file being read, so we don't need to subset
            print('Assuming we are loading a "filtered" file that contains only cells.')
            pass
        elif 'barcode_indices_for_latents' in d.keys():
            X = X[d['barcode_indices_for_latents'], :]
            d['barcodes'] = d['barcodes'][d['barcode_indices_for_latents']]
        elif 'barcodes_analyzed_inds' in d.keys():
            X = X[d['barcodes_analyzed_inds'], :]
            d['barcodes'] = d['barcodes'][d['barcodes_analyzed_inds']]
        else:
            print('Warning: analyzed_barcodes_only=True, but the key '
                  '"barcodes_analyzed_inds" or "barcode_indices_for_latents" '
                  'is missing from the h5 file. '
                  'Will output all barcodes, and proceed as if '
                  'analyzed_barcodes_only=False')

    # Construct the anndata object.
    adata = anndata.AnnData(X=X,
                            obs={'barcode': d.pop('barcodes').astype(str)},
                            var={'gene_name': (d.pop('gene_names') if 'gene_names' in d.keys()
                                               else d.pop('name')).astype(str)},
                            dtype=X.dtype)
    adata.obs.set_index('barcode', inplace=True)
    adata.var.set_index('gene_name', inplace=True)

    # For CellRanger v2 legacy format, "gene_ids" was called "genes"... rename this
    if 'genes' in d.keys():
        d['id'] = d.pop('genes')

    # For purely aesthetic purposes, rename "id" to "gene_id"
    if 'id' in d.keys():
        d['gene_id'] = d.pop('id')

    # If genomes are empty, try to guess them based on gene_id
    if 'genome' in d.keys():
        if np.array([s.decode() == '' for s in d['genome']]).all():
            if '_' in d['gene_id'][0].decode():
                print('Genome field blank, so attempting to guess genomes based on gene_id prefixes')
                d['genome'] = np.array([s.decode().split('_')[0] for s in d['gene_id']], dtype=str)

    # Add other information to the anndata object in the appropriate slot.
    _fill_adata_slots_automatically(adata, d)

    # Add a special additional field to .var if it exists.
    if 'features_analyzed_inds' in adata.uns.keys():
        adata.var['cellbender_analyzed'] = [True if (i in adata.uns['features_analyzed_inds'])
                                            else False for i in range(adata.shape[1])]

    if analyzed_barcodes_only:
        for col in adata.obs.columns[adata.obs.columns.str.startswith('barcodes_analyzed')
                                     | adata.obs.columns.str.startswith('barcode_indices')]:
            try:
                del adata.obs[col]
            except Exception:
                pass
    else:
        # Add a special additional field to .obs if all barcodes are included.
        if 'barcodes_analyzed_inds' in adata.uns.keys():
            adata.obs['cellbender_analyzed'] = [True if (i in adata.uns['barcodes_analyzed_inds'])
                                                else False for i in range(adata.shape[0])]

    return adata


def dict_from_h5(file: str) -> Dict[str, np.ndarray]:
    """Read in everything from an h5 file and put into a dictionary."""
    d = {}
    with tables.open_file(file) as f:
        # read in everything
        for array in f.walk_nodes("/", "Array"):
            d[array.name] = array.read()
    return d


def _fill_adata_slots_automatically(adata, d):
    """Add other information to the adata object in the appropriate slot."""

    for key, value in d.items():
        try:
            if value is None:
                continue
            value = np.asarray(value)
            if len(value.shape) == 0:
                adata.uns[key] = value
            elif value.shape[0] == adata.shape[0]:
                if (len(value.shape) < 2) or (value.shape[1] < 2):
                    adata.obs[key] = value
                else:
                    adata.obsm[key] = value
            elif value.shape[0] == adata.shape[1]:
                if value.dtype.name.startswith('bytes'):
                    adata.var[key] = value.astype(str)
                else:
                    adata.var[key] = value
            else:
                adata.uns[key] = value
        except Exception:
            print('Unable to load data into AnnData: ', key, value, type(value))
```

**Here is an example:** </br>
```python
# fun dir
sys.path.append(r'./script/')
import re
import os
from anndata_from_h5 import *

# load cb data with anndata_from_h5
rawDataPath = '../cellbenderFilteredMatrix/'
fileRegex = re.compile('[A-Z]*_[1234]_cellbender_filtered.h5')
adata = [anndata_from_h5(file=rawDataPath + file) for file in os.listdir(rawDataPath) if fileRegex.search(file)]
```

> 2. Zero counts

i. find the fake "cells" with zero counts: </br>

```python
import scanpy as sc

# merge adata
adataAll = adata[0].concatenate(adata[1:])
# deal the zero counts fake "cell"
# get each sample fake cell number
adataAll[adataAll.obs['n_genes_by_counts'] == 0].obs['SampleID'].value_counts()
# for one sample
adataAll[adataAll.obs['n_genes_by_counts'] == 0].obs['SampleID'].value_counts()['C001_BL']
# get fake cell barcode
adata_test = adataAll[adataAll.obs['SampleID'] == 'C001_BL']
fake_cell_barcode = adata_test[adata_test.obs['n_genes_by_counts'] == 0].obs.index.to_list()
# check fake cell raw matrix, is empty
adata_test[fake_cell_barcode].X.data
adata_test[fake_cell_barcode].X
```
if you do not filter them, it will cause lots of problem, e.g. `sc.pp.calculate_qc_metrics` get lots of NAN result:

```python
# calculate mt
adataAll.var['mt'] = adataAll.var_names.str.startswith('MT-')
sc.pp.calculate_qc_metrics(adataAll, qc_vars=['mt'], percent_top=None, log1p=False, inplace=True)
# mt gene name
mt_gene = adata_test.var[adata_test.var['mt']].index.to_list()
# get NaN
adata_test[fake_cell_barcode].obs['pct_counts_mt']
# because of these fake "cells" do not include any gene counts
adata_test[fake_cell_barcode, mt_gene].X.data
```
drop them out and then do QC
```python
adataAll[adataAll.obs['n_genes_by_counts'] != 0]
```

---

> Author: Gaux Wang  
> URL: https://dyinboisry4u.github.io/230224/  


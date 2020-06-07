# Slice_Order_Explorer

# This notebook implements a PoC that verify whether the slice ordering / timing in use make sense data-wise.
#
# It is based on the idea that if the slice timing / order is the right one, then the local signal coherency 
# should be maximal (following the slice timing correction idea). To measure local coherency, one can use
# the regional homogeneity (ReHo). Here this was tested with local NN filter of 27 neighbours or, to emphasise
# out-of-slice contributions a 18 option (mask to measure only correlation with neighours on adjacent slices)
# where a reasonable correlation should be restored by correct slice timing (at least in functionally homogenous ROI).
#
# This migth be useful when metadata (e.g dicom header tags) are not encoding this info reliably and
# is not immediately clear from the signals upon visual inspection.
# It allows for consistent automation of the ordering inference to also check whether a dataset made use of 
# uniform slice ordering in all scans.
#
# All six canonical order are being applied to each rs-fMRI dataset with *minimal* pre-processing. 
# Once cleaned, the ReHo is extracted from each dataset and its average value should be highest when using the correct
# slice ordering -> using correct pattern should recover best local signal coherence.
# 
# So even if the slice timing used for scanning is unclear or not reported (e.g. Philips or old GE) with this 
# you might be able to infer the most likely pattern alternatives used in a dataset.
# 
# No warranty of functionality, tested on freely available datasets from OpenfMRI project (http://www.openfmri.org)
# on ds000133, ds000172, ds000201, ds000220, ds000245, ds000256. The approach is functional in all tested cases.
# Note:
# 1 - it was found to always work to automatically detect whether interleaved rather than sequential was used
# 2 - it detects mixed cases of patterns within same dataset
# 3 - inference of direction (ascending / descending) less reliable if data is noisy or with lots of motion ( -5 helps)
# 4 - inference of correct slice order from short TR scans works for main pattern but direction less clear (TR < 1.5 sec) 
#     ...  might be not relevant correction anyway ?
# 5 - improving the stats might be less effective than using context data (ie. what is the scanner/model/slices/...)

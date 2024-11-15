# AISC Fairness Experiments

There are two python scripts, base.py and waterbirds_data_structure.py; the first includes various fairness loss functions, while the second contains a class for data preprocessing, formatting, etc.

`fairness_exp_v0.ipynb` is an older notebook for experiments. Model checkpoints, val and training losses, learning rates, predictions, etc. can be found on mslurm.  

## Waterbirds class
### Functions
` __init__(self, metadata, images_dir, transform=None, split=None, split_ratios=(0.65, 0.15, 0.10, 0.1), pairing_distribution=None, seed=None, preload=False, device='cpu') >`
`split_ratios` sets the training, validation, test, and finetuning proportions. If None, we use the pre-set splits. Otherwise we re-shuffle and split (according to desired proportion and distributions)

`pairing_dist` specifies the distributions of pairings within the dataset; here, 40% of data should be water bird on water, land on land etc. 
if `pairing_distribution` is not specified in call, dataset splits are substantially skewed.
When specified, `pairing_distributions` will up or downsample based on desired distribution of class (bird), background subset pairings.

`__len__(self)` and `__getitem__(self, idx)` are straightforward.

`get_split(self, new_split, **kwargs)`Returns the subset of the dataset for the specified split (e.g., 0: train, 1: val, 2: test).

`check_class_background_pairings(self, split)` returns distributions of pairs (label, background) for specified split

`updating_target` and `shuffle_split` are helper functions for data processing.


Unless pairing_distribution is specified, the data distributions across splits look roughly as follows (this is an example print out):

Class-Background Pairing Distribution (as % of total) for Split 0:
| place |              0              |              1              |
|-------|-----------------------------|-----------------------------|
| y     |                             |                             |
| 0     | 4006 (52.28%)               | 1902 (24.82%)               |
| 1     | 548 (7.15%)                 | 1206 (15.74%)               |

Class-Background Pairing Distribution (as % of total) for Split 1:
| place |              0              |              1              |
|-------|-----------------------------|-----------------------------|
| y     |                             |                             |
| 0     | 959 (54.24%)                | 431 (24.38%)                |
| 1     | 116 (6.56%)                 | 262 (14.82%)                |


Class-Background Pairing Distribution (as % of total) for Split 2:
| place |              0              |              1              |
|-------|-----------------------------|-----------------------------|
| y     |                             |                             |
| 0     | 634 (53.82%)                | 290 (24.62%)                |
| 1     | 81 (6.88%)                  | 173 (14.69%)                |

Class-Background Pairing Distribution (as % of total) for Split 3:
| place |              0              |              1              |
|-------|-----------------------------|-----------------------------|
| y     |                             |                             |
| 0     | 621 (52.63%)                | 282 (23.90%)                |
| 1     | 86 (7.29%)                  | 191 (16.19%)                |

> Note that these are the proportions used in the initial batch of experiments.

## Base.py
This file contains the following fairness loss functions:
`equalized_loss`,
`parity_loss`,
`affirmative_action_modified_loss`,
`equalized_fpr_loss`,
`equalized_tpr_loss`, all of which have the following params `(predictions, labels, group_labels, device="cuda", alpha=0.1)`.

It also contains `validate_and_save_predictions(model, fairness, val_loader, criterion, file_info=None)`, which is used during the training loops. 


# MultiWOZ 2.2+
This dataset is modified based on the [MultiWOZ 2.2](https://github.com/budzianowski/multiwoz/tree/master/data/MultiWOZ_2.2) version. The data in the `train/dev/test` folders follows 2.2 version format, while `data.json` (compressed as `data.zip`) is in the original 2.0/2.1 version format.

Our modification is mainly about annotation inconsistancy and entity bias. More details of the modification can be found in our paper[[pdf]](https://arxiv.org/abs/2105.14150).


## Annotation Inconsistancy
The dialogs have been annotated inconsistently with respect to the slot type "Name". Figure below shows two dialogs in the *"Attraction"* domain with similar context.
In one dialog the attraction name is annotated while not in the other one. This inconsistency leads to a fundamental confusion for dialog state modeling whether to predict the attraction name or not in similar scenarios.
<img width="897" alt="inconsist" src="https://user-images.githubusercontent.com/27875179/153943369-75b336c8-0918-4eef-8924-5a7a973b7a2f.png">

We propose an automated correction for this issue, which is present in 70% of the dialogs. The modification brings an improvement of the joint goal accuracy (JGA) by around 10%:

|Model                                  | 2.1       | 2.2       | 2.2+(Ours)  |
| --- | --- | --- | --- |
|TRADE (Wu et al., 2019)                | 44.4±0.3  | 45.6±0.5  | 55.2±0.2    |
|SimpleTOD (Hosseini-Asl et al., 2020)  |54.7± 0.5  | 53.6±1.0  | 62.1±0.2    |
|DST-BART (Lewis et al., 2020)          |57.9±0.5   | 56.0±0.7  | 67.4±0.5    |


## Entity Bias
We further find the distribution of the slot value in the dataset is highly imbalanced. In table below, we observe that "cambridge" appears as the train destination in 50% of the dialogs in train domain while there are 13 different destinations. As a result, a dialog system trained on this imbalanced data might be more likely to generate "cambridge" as the slot value even though ``cambridge" might not even be mentioned in dialog history.

|Slot Value | Count Num. |
|---|---|
|**cambridge**            | **8,086** |
|london liverpool street | 760   |
|leicester               | 746   |
|stansted airport        | 711   |
|stevenage               | 710   |
|ely                     | 695   |
|...|...|
|total                   | 16,138 |


We present a new test set([link](https://github.com/qbetterk/ParlAI/blob/master/data/multiwoz_v22/modified_test.json)) with all entities replaced with ones never seen during training. We benchmark the state-of-the-art dialog state tracking models on these new versions of data and observe a 29% drop in JGA.

## Scripts
Our modification is conducted based on rules and regular experssion, therefore, it can be automatically applied on the recent MultiWOZ versions ([2.3](https://arxiv.org/abs/2010.05594) and [2.4](https://arxiv.org/abs/2104.00773)). The script locates at [link](https://github.com/qbetterk/ParlAI/blob/master/utils_inconsistancy/analyze_multiwoz.py) and can run by simply calling ``python analyze_multiwoz.py``. You may want to change the path to the data in the [``init()``](https://github.com/qbetterk/ParlAI/blob/a63851ae89774f431661a7ecf98c04262758cac4/utils_inconsistancy/analyze_multiwoz.py#L43) function. The modification is conducted on our own dialog state tracking format, so you may want to run the [script](https://github.com/qbetterk/ParlAI/blob/master/parlai/tasks/multiwoz_dst/utils/reformat.py) first by calling ``python reformat.py``.

## Contact

If you have any questions, please feel free to contact us by (kq2157@columbia.edu).

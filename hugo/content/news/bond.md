---
title: "BOND is Accepted by KDD 2020"
date: 2020-05-15T22:54:07-04:00
draft: true
tags: [nlp,kdd,bert,ner,distant supervision,weak supervision]
Description: "Our recent work on distantly supervised NER is accepted by KDD2020. We are closing the GAP between full supervision and distant supervision!"
---

> Our recent work on distantly supervised NER is accepted by KDD2020.
> We are closing the GAP between full supervision and no supervision!


The reuslts (F1 score) are summerized as follows:

| Method | CoNLL03 | Tweet | OntoNote5.0 | Webpage | Wikigold |
| ------ | ------- | ----- | ----------- | ------- | -------- |
| Full Supervision | 91.21 | 52.19 | 86.20 | 72.39 | 86.43 |
| Previous SOTA | 76.00 | 26.10 | 67.69 | 51.39 | 47.54 |
| BOND | 81.48 | 48.01 | 68.35 | 65.74 | 60.07 |

- *Full Supervision*: Roberta Finetuning/BiLSTM CRF
- *Previous SOTA*: BiLSTM-CRF/AutoNER/LR-CRF/KALM/CONNET

### **Reference**

- {{<paper
title="BOND: Bert-Assisted Open-Domain Named Entity Recognition with Distant Supervision"
year="2020"
author="Chen Liang*, Yue Yu*, Haoming Jiang*, Siawpeng Er, Ruijia Wang, Tuo Zhao and Chao Zhang (* Equal Contribution)"
proce="The 26th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD)"
>}}
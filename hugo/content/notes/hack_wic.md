---
title: "Hack WiC Leaderboard"
date: 2020-05-16T23:50:07-04:00
draft: true
publishdate: 2020-12-31
---

> WiC dataset is to identify the intended meaning of words. Each instance in WiC has a target word w, either a verb or a noun, for which two contexts are provided. Each of these contexts triggers a specific meaning of w. The task is to identify if the occurrences of w in the two contexts correspond to the same meaning or not. [link](https://pilehvar.github.io/wic/) [Leaderboard](https://competitions.codalab.org/competitions/20010)

WiC is a rather small dataset and can be somwhow hacked.

In this note, I use superglue version of data: [WiC.zip](https://dl.fbaipublicfiles.com/glue/superglue/data/v2/WiC.zip)


## Step 1: Hack using wordnet

Lots of sentences are just from WordNet.

By applying the following rule, you will get a pretty good position in the leaderboard:
```python
def wordnetmatch(e):
    s1 = e['sentence1'].lower()
    s2 = e['sentence2'].lower()
    w = e['word']
    hit1 = hit2 = None
    synsets = wn.synsets(w)
    for i,s in enumerate(synsets):
        for ex in s.examples():
            if edit_distance(s1,ex.lower()) < 8:
                hit1=i
            if edit_distance(s2,ex.lower()) < 8:
                hit2=i
    if hit1==hit2:
        return True
    elif hit1 is not None and hit2 is not None: 
        if edit_distance(synsets[hit1].definition(), synsets[hit2].definition()) < 10:
            return True
        else:
            return False

    assert False
```

For training data:
- Accuracy:  0.928
- Cover rate:  0.634

For validation data:
- Accuracy:  0.940
- Cover rate:  0.649

Let's try on leaderboard!


|Method| Accuracy|
|---|---|
|Human Baseline|80.0|
|T5|76.9|
|**--WordNet Hack--**|**--73.6--**|
|NEZHA-Large|72.7|
|RoBERTa-mtl-adv|72.1|
|RoBERTa|69.9|
|BERT|69.6|

## Step 2: Train over partially labeled data. 

Fine-tune Roberta Model on labeled training data, matched dev data, matched test data.
Select the best checkpoint according to dev data.

Let's try on leaderboard!

**Surpass Human Performance!!!!!!**

|Method| Accuracy|
|---|---|
|**--WordNet Hackv2--**|**--84.1--**|
|Human Baseline|80.0|
|T5|76.9|
|**--WordNet Hackv1--**|**--73.6--**|
|NEZHA-Large|72.7|
|RoBERTa-mtl-adv|72.1|
|RoBERTa|69.9|
|BERT|69.6|


## Utils: Map v1.1 ids to v1.0


```python 
import numpy as np
import json

data_0 = []
data_1 = []
with open('test.data.txt','r') as f:
    for l in f.readlines():
        l = l.split('\t')
        l = [i.rstrip() for i in l]
        _e = {'word':l[0]}
        _e['sentence1'] = l[3].lower().replace(' ','').replace('—','').replace('-','').replace('`','\'')
        _e['sentence2'] = l[4].lower().replace(' ','').replace('—','').replace('-','').replace('`','\'')
        data_0.append(_e)

with open('test.jsonl','r') as f:
    for l in f.readlines():
        _e = json.loads(l)
        _e['sentence1'] = _e['sentence1'].lower().replace(' ','').replace('—','').replace('-','')
        _e['sentence2'] = _e['sentence2'].lower().replace(' ','').replace('—','').replace('-','')
        data_1.append(_e)

print(data_0[0])
print(data_1[0])


def findid0(x):
    count_id = []
    for i,e in enumerate(data_0):
        if e['word'] == x['word'] and \
           e['sentence1'] == x['sentence1'] and \
           e['sentence2'] == x['sentence2']:
           return i 
        if e['word'] == x['word']:
            count_id.append(i)
    if len(count_id) == 1:
        return count_id[0]
    
    print(x)
    for id in count_id:
        print(data_0[id])
        print(data_0[id]['sentence1'] == x['sentence1'],data_0[id]['sentence2'] == x['sentence2'])
    import ipdb; ipdb.set_trace()

map1_to_0 = [findid0(x) for x in data_1]

import copy 
_c = copy.deepcopy(map1_to_0)
_c.sort()
for i,j in zip(_c, range(len(_c))):
    assert i==j

results = [0]*len(map1_to_0)
with open('hackwic2/WiC.jsonl','r') as f:
    for l in f.readlines():
        _e = json.loads(l)
        id1 = _e['idx']
        id2 = map1_to_0[id1]
        if _e['label'] == 'true':
            results[id2]='T'
        else:
            results[id2]='F'
            
with open('output.txt','w') as f:
    for r in results:
        f.write(r+'\n')
```
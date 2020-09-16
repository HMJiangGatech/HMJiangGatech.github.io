---
title: "Hack WiC Leaderboard"
date: 2020-09-16T23:50:07-04:00
draft: true
publishdate: 2021-12-31
---

> Some tricks for reproduce WMT EN-DE

WMT EN-DE
```
CUDA_VISIBLE_DEVICES=$GPUDEV fairseq-generate ../data-bin/wmt16_en_de_bpe32k \
--path $MODELDIR \
--batch-size 128 --beam 10 --lenpen 0.6 --remove-bpe \
--user-dir ../radam_fairseq > ${GEN}

#GEN=raw_result

SYS=$GEN.sys
REF=$GEN.ref

grep ^H $GEN | cut -f3- | perl -ple 's{(\S)-(\S)}{$1 ##AT##-##AT## $2}g' > $SYS
grep ^T $GEN | cut -f2- | perl -ple 's{(\S)-(\S)}{$1 ##AT##-##AT## $2}g' > $REF
fairseq-score --sys $SYS --ref $REF
```

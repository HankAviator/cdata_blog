---
layout: post
title: Pearson correlation test
---
This week is much easier, no post-hoc test is needed, thus the code is much simpler.

```
ods html close;
ods html;
libname worklib 'e:\Course\'; 
proc import
	datafile="E:\SPM\Coursera_datasets\nesarc_pds.csv"
	out=worklib.nesarc_pds;
	delimiter=',';
run;
data worklib.working;
	set worklib.nesarc_pds;
run;
proc sort; 	by idnum;

proc corr;
	var S1Q24FT S1Q24LB;
run;
```
[Here](../../pages/pearson.htm)'s the output. Two variables are tested and statistically significant.
---
layout: post
title: Testing a moderator
---
I picked `BLOOD/NATURAL FATHER EVER AN ALCOHOLIC OR PROBLEM DRINKER` (`S2DQ1` as the coding) as a candidate moderator. By common sense it's not related with tobacco dependence. Let's test it.

The source code is here, I hardcoded all unknown responses as missing. `s3aq3b1` and `tab12mdx` are both category, but `tab12mdx` has only two levels, thus post-hoc test is not needed.

```
ods html close;
ods html;
libname worklib 'e:\Course\'; 
proc import
	datafile="E:\SPM\Coursera_datasets\nesarc_pds.csv"
	out=worklib.nesarc_pds;
	delimiter=',';
run;
data work;
	set worklib.nesarc_pds;
	select;
		when (s3aq3b1=9) s3aq3b1=.;
		otherwise;
	end;
run;
proc sort;
	by S2DQ1;
run;
proc freq;
	tables tab12mdx*s3aq3b1/chisq;
	by s2dq1;
run;
```
Here is the [output](../../pages/moderator.htm). We should be more focused on the cases where `BLOOD/NATURAL FATHER EVER AN ALCOHOLIC OR PROBLEM DRINKER` is not missing. When `S2DQ1`=1, `col pct` is positively correlated between `S3AQ3B1` and `TAB12MDX`=0, while `S2DQ1`=2, the same situation, both with p<0.0001. Thus, `BLOOD/NATURAL FATHER EVER AN ALCOHOLIC OR PROBLEM DRINKER` is not a moderator for this study.
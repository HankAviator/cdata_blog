---
layout: post
title: Drug vs ethnicity
tag: [Coursera]
---
This week starts easy, and I chose to investigate if ethnicity is a factor which affects the youngest age of cannabis. The reason I used *cannabis* instead of *amphetamines* is on account of sample size. 
Here's the code:

```
LIBNAME mydata "/courses/d1406ae5ba27fe300 " access=readonly;

*** Make sure to establish your own lib, 'work' is temporary;
libname worklib '~/worklib'; 


data worklib.working;
	set mydata.nesarc_pds;
	** Strip unknown drug usage age;
	if S3BD5Q2F	=99 then S3BD5Q2F = .;
	** Make code interpretable by human;
	label 	S3BD5Q2F = 'Age when began using cannabis most';
run;
proc anova plots=none;
	class ETHRACE2A;
	model S3BD5Q2F = ETHRACE2A;
	mean ETHRACE2A / duncan;
run;
```

And [here's the output](/cdata_blog/pages/s2w1_output/).
With *p* value less than .0001, we reject H<sub>0</sub>, and we cannot infer that the means of Black and American Indian are significantly different. But we do see Hispanic cannabis users take it earlier than all of other 4 ethnicities.
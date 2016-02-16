---
layout: post
title: Tiny tiny edit
tag: [Coursera]
---

So this week is going to be quite easy, only modifying a bit will do the work. Let's take a look at my code.

```SAS
LIBNAME mydata "/courses/d1406ae5ba27fe300 " access=readonly;

*** Make sure to establish your own lib, 'work' is temporary;
libname worklib '~/worklib'; 


ods html close;
ods html;
proc format;
*** So we want the output look nicer with these custom formats;

	value AMP	1='Yes'
				2='No'
				9='Unknown';
	value inc	0=' $0 (No personal income)'
				1= '$1 to $4,999'
				2= '$5,000 to $7,999'
				3= '$8,000 to $9,999'
				4= '$10,000 to $12,999'
				5= '$13,000 to $14,999'
				6= '$15,000 to $19,999'
				7= '$20,000 to $24,999'
				8= '$25,000 to $29,999'
				9= '$30,000 to $34,999'
				10='$35,000 to $39,999'
				11='$40,000 to $49,999'
				12='$50,000 to $59,999'
				13='$60,000 to $69,999'
				14='$70,000 to $79,999'
				15='$80,000 to $89,999'
				16='$90,000 to $99,999'
				17='$100,000 or more';
	value ofn	1= 'Every day'
				2= 'Nearly every day'
				3= '3 to 4 times a week'
				4= '1 to 2 times a week'
				5= '2 to 3 times a month'
				6= 'Once a month'
				7= '7 to 11 times a year'
				8= '3 to 6 times a year'
				9= '2 times a year'
				10= 'Once a year'
				99= 'Unknown';
run;

data worklib.working;
	set mydata.nesarc_pds;
		
		** Count who have tried amphetamines using a secondary var;
		if S3BQ1A4 = 1 then amped+1;
	** Another effort to make code interpretable by human;
	label 	S3BQ1A4= 'Ever used amphetamines'
			S1Q10B = 'Total personal income in last 12 months: category'
			S3BD4Q2E = 'How often used amphetamines when using the most';
		** Setting Unknown values as missing;
		select;
			** Tell me if you're REALLY unsure, not you don't use drugs;
			when (S3BD4Q2E = 99 and S3BQ1A4=9) S3BD4Q2E= .;
			when (S3BQ1A4 = 9) S3BQ1A4 = .;			
		otherwise;
		end;
	** Dangerous! Only do when you are sure!;
	format S3BQ1A4 AMP. S1Q10B inc. S3BD4Q2E ofn.;
run;
proc sort data=worklib.working;
	by S3BQ1A4 S3BD4Q2E S1Q10B;
proc freq;
	tables S3BQ1A4 S3BD4Q2E S1Q10B;
run;
```

Frequency output is [here](/cdata_blog/pages/week3_output/) .

I created a secondary variable `amped` but it's not printed though. By a simple subset in `select` clause, I figured out who is not taking drugs, but coded as 'Unknown'. They are dismissed. Oh, not only from drug take frequency distribution but also set to missing (`.`) in 'Do you take amphetamines?'
So all 'Unknown' values are regarded as missing for variable `S3BQ1A4`, that's 328 of them. No changes spotted on analysis of variable `S1Q10B`. The subsetting was intended to contribute to clarification of variable `S3BD4Q2E`, those who really didn't know how often they take amphetamines. But apparently, original dataset is clean enough that those who really took amphetamines and didn't know the frequency are coded as 'Unknown', while those who never took amphetamines as missing. So the analysis for `S3BD4Q2E` is also unchanged.

A clean raw data source is always a time-saving prerequisite for data managers.


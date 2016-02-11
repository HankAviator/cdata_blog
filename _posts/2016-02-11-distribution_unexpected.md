---
layout: post
title: Distribution unexpected
tag: [Coursera]
---

So I am not expecting this to happen.. I thought they would distribute like normal distribution. Here's the code:

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
	** Another effort to make code interpretable by human;
	label 	S3BQ1A4= 'Ever used amphetamines'
			S1Q10B = 'Total personal income in last 12 months: category'
			S3BD4Q2E = 'How often used amphetamines when using the most';
	**Dangerous! Only do when you are sure!;
	format S3BQ1A4 AMP. S1Q10B inc. S3BD4Q2E ofn.;
run;
proc sort data=worklib.working;
	by S3BQ1A4 S3BD4Q2E S1Q10B;
proc freq;
	tables S3BQ1A4 S3BD4Q2E S1Q10B /plots =freqplot;
run;
```

Some notes:

*  `~` in `'~/worklib'` means home directory - as SAS Ondemand is based on Linux servers.
* `ods html close; ods html;` is especially useful when you're running instances locally - it clears html ods output so that your latest output will always be the only result.
* `format S3BQ1A4 AMP. S1Q10B inc. S3BD4Q2E ofn.;`, hardcoding like this typically breaks the code of conduct in your industry. Use this approach on a separate dataset. **NEVER BE TOO CONFIDENT ON ORIGINAL DATASET!** You may have ruined everything with a line of code - `drop table [dataset]` for example.

Check my [output](/cdata_blog/pages/week2_output/).

Apparently, there are only missing values in "How often used amphetamines when using the most", known values range from 'every day' to 'once a year'. Note, 'Unknown' is an essential option here. **Missing ≠ 0 ≠ Unknown**. By 'using the most', a lot of surveyees chose "everyday", contributing 18.29% of population. The runner-up is "1 - 2 times a week". This can be reasonable for common people as they can't spend endlessly on drugs. One interesting fact is the 3rd place, "once a year". That would be someone like me, tried only once and never again.

Moving to income category, it's sad but **50% of population owns only 20% of all fortune**. ($0 - $19999 compared to maxima $20000 - $100000). Not normal distribution though - I was expecting it to be. Was it because of financial crisis? I don't know exactly, but it does look shocking to me.

And the last variable needs no analysis. Only ~4% of population has taken amphetamines before (even counting in "Unknown" individuals). Well that's just .. plausible ;) .
# CARUSO METRIC

## Socials
- ✖️ [Twitter](https://twitter.com/FHuzjan) 
- 💼 [LinkedIn](https://www.linkedin.com/in/franhuzjan/)

## Original distributions

**NBA Fantasy:** A source of pure joy and stress for most of us. If you're reading this, it means you're deep into it, and you probably know that there are two types of NBA Fantasy leagues: points and categories. Categories typically require more strategy and planning, making them the more popular choice, with the most popular being 9CAT league.

The categories are
- Points
- Three pointers
- Rebounds
- Assists
- Steals
- Blocks
- Turnovers
- Field goal percentage
- Free throw percentage

For the past 30 years or so, the golden standard for NBA fantasy category rankings has been the Z-score. Every year, we draft, rank, and evaluate players based on their Z-score ranking. But have you ever questioned how that rank is calculated? Probably not, just like myself.

I was listening to [Josh Lloyd's](https://twitter.com/redrock_bball) podcast, where he was discussing how the Z-score is calculated. It essentially assumes that the data distribution is Gaussian or Normal. I thought to myself, well, almost everything becomes normally distributed when the sample size is large enough, and in the NBA, we're dealing with thousands and thousands of shots, steals, blocks, and more.

<p style="text-align: center;">
<img src="Images/Gaussian.png" style="width:50%; height:50%"/>
</p>

Z-score is being calculated with the following formula:

Z = (X - μ) / σ

Where:
- Z is the Z-score,
- X is the value you want to standardize,
- μ is the mean (average) of the data,
- σ is the standard deviation of the data.

and the data is the values from all players for a specific category.


Okay, sounds good for now, but Josh said that the data is not normally distributed. I can't trust him blindly, so I decided to plot the main 9 categories for all top 180 players based on the traditional Z-score from the 2013/14 to the 2022/23 season, along with their distributions.

![Distribution Plot](Images/org_dists.png)

Well, well, it looks like he was right all along. None of this data resembles a Gaussian distribution. Maybe the Z-score isn't the best metric available. Perhaps that's why players like Delon Wright, Al Horford, and De'Anthony Melton had a higher rank than the 2x MVP Giannis Antetokounmpo in his highest-scoring season last year.

By the way, there's a really interesting tweet by Josh:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">JJJ had a z score of 4.64 for blocks category last season. In a normally distributed set of data, someone being that far above is a 1 in about half a million proposition.</p>&mdash; Josh Lloyd (@redrock_bball) <a href="https://twitter.com/redrock_bball/status/1690974327620227072?ref_src=twsrc%5Etfw">August 14, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

A player that averages 3.0 blocks per game should be THAT rare. There have been plenty of players in the past who have averaged even more. This could be the biggest inspiration for CARUSO.


## Idea for CARUSO

Inspired by one of my favorite, and also one of the most underrated players in the league, I've coined the name CARUSO metric, which stands for:

- **C** - Customized
- **A** - Adaptive
- **R** - Resourceful
- **U** - Unique
- **S** - Statistical
- **O** - Optimized

CARUSO is based on the H-score (instead of the Z-score). Similar to the classical approach, it calculates a score for each category and then takes the average across all categories, yielding the final CARUSO value. CARUSO also employs a machine learning algorithm to determine the best distribution and its parameters, aiming to minimize the mean squared error on the data.

<p style="text-align: center;">
	<img src="Images/real_vs_fitted.png", style="width: 25%, height: 25%;"/>
</p>

Once the optimal distribution is identified, the H-score can be calculated for every data point within it, involving basic statistics and mathematics.


## CARUSO Interpretation

I've provided a lot of information, but I've shown very few results. In the graphs below, you can see a comparison of H-score and Z-score.

Some of the main issues that CARUSO addresses include not overvaluing high values in sparse categories like steals and blocks. This is because, as the distribution itself shows (see the image with distributions above), these categories are not as rare as the traditional Z-score suggests.

![H-score vs. Z-score](Images/h_vs_z_score.png)

### 3-Pointers

Let's delve into a specific case: 3-pointers. The unique aspect here is that it doesn't resemble a univariate distribution; instead, it exhibits **2** spikes. As a result, it can't be adequately fitted with the classic distribution and requires a Gaussian mixture model for accurate and logical predictions. You can see the fitted and real distributions on the left plot below.

![3-Pointers](Images/3_g.png)

The comparison of H and Z-score for 3-pointers is particularly fascinating. Because it's a multivariate distribution, and we have players who either take 3-pointers or don't take them at all, CARUSO penalizes players with low 3-pointers and also rewards the most extreme cases more than the traditional approach. This finding piqued my interest, because in my mind the players that don't score 3s at all should be punished more than players that score small volume of it. This finding will have a significant impact on the final player rankings.


### Percentages categories

The thing that gave me the most headache while trying to figure the CARUSO ranking were the percentages categories (FG% and FT%). Now I will use the FT% as an example, but it can be also applied to FG%. 
The issuse with them is that there is no clear interpretability which player is better:

- Player A that shoots FTs with 80% on 20 attemps
- Player B that shoots FTs with 87% on 3 attemps

Both players shoot above average FT%, but which one should score more? Let's turn to distributions once again (wohoo). 

First that that I did was took both the FT% and FT attemps per game, and tried to score both at the same time. Makes since right? Except it doesn't. In this approach the higher the FT% was, the higher the first part of the H-score will be (which is logical and it should work like that) and also the more the attemps the players has the second part of the H-score will be higher (which is pretty dumb, because of the next example). For example if a player A shoots 100 attemps/game on 50%, he will have a higher score than player B which shoots 70% on 2 attemps/game, which is crazy and not logical and wrong. Let's go to the next approach. 


The second thing I tried was inspired by the GREAT [u/nwsy96](https://www.reddit.com/user/nwsy96/) and his comment on reddit - this thing called *impact*. The impact effecitively takes both percentage and volume into account, but it also takes into account the mean FT% of all players. Therefore if you're worse than the average shooter and also shoot a lot of free throws you will be punished! And in the other case you will be rewarded - great it makes sense now. Hopefully...

![FT%](Images/ft_perc.png)

CARUSO punishes much less the worser percentage shooters than the traditional methods, but it also rewards less the finest FT shooters (as well as FG). Why you might ask? Because the distribution said so!! <3 MATHS <3

### Rankings

Here are the top30 players from last season (2022/23) based on CARUSO. Their CARUSO Rank is shown, traditional rank, changes in the rank and H-score for each category, as well as their value for the categories in question. 

What is really nice that Giannis jumped up a whooping 82 spots, while JJJ dropped for 12 spots, because of the blocks distribution. Some of the other biggest movers are:

- Walker Kessler - 43 &#8594; 110 (-65)
- Chris Paul - 31 &#8594; 65 (-35)
- Mitchell Robinson - 52 &#8594; 100 (-48)
- Aaron Gordon - 122 &#8594; 63 (+59)
- Steven Adams - 173 &#8594; 129 (+43)
- Kelly Olynyk - 109 &#8594; 69 (+40)

Pretty much all of it makes sense, I would biasly say :)


<html xmlns:v="urn:schemas-microsoft-com:vml"
xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns:x="urn:schemas-microsoft-com:office:excel"
xmlns="http://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/html; charset=utf-8">
<meta name=ProgId content=Excel.Sheet>
<meta name=Generator content="Microsoft Excel 15">
<link id=Main-File rel=Main-File href="../caruso_html.htm">
<link rel=File-List href=filelist.xml>
<link rel=Stylesheet href=stylesheet.css>
<style>
<!--table
  {mso-displayed-decimal-separator:"\,";
  mso-displayed-thousand-separator:"\.";}
@page
  {margin:.75in .7in .75in .7in;
  mso-header-margin:.3in;
  mso-footer-margin:.3in;}
-->
</style>
<script language="JavaScript">
<!--
function fnUpdateTabs()
 {
  if (parent.window.g_iIEVer>=4) {
   if (parent.document.readyState=="complete"
    && parent.frames['frTabs'].document.readyState=="complete")
   parent.fnSetActiveSheet(0);
  else
   window.setTimeout("fnUpdateTabs();",150);
 }
}

if (window.name!="frSheet")
 window.location.replace("../caruso_html.htm");
else
 fnUpdateTabs();
//-->
</script>

</head>

<body link="#0563C1" vlink="#954F72">

<table border=0 cellpadding=0 cellspacing=0 width=2409 style='border-collapse:
 collapse;table-layout:fixed;width:1801pt'>
 <col width=100 style='mso-width-source:userset;mso-width-alt:3200;width:75pt'>
 <col width=87 span=2 style='width:65pt'>
 <col width=128 style='mso-width-source:userset;mso-width-alt:4096;width:96pt'>
 <col width=87 style='width:65pt'>
 <col width=180 style='mso-width-source:userset;mso-width-alt:5760;width:135pt'>
 <col width=87 span=16 style='width:65pt'>
 <col class=xl71 width=87 span=2 style='width:65pt'>
 <col width=87 span=2 style='width:65pt'>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl65 width=100 style='height:16.0pt;width:75pt'>CARUSO
  Rank</td>
  <td class=xl66 width=87 style='width:65pt'>Rank</td>
  <td class=xl66 width=87 style='border-left:none;width:65pt'>Rank Change</td>
  <td class=xl73 width=128 style='border-left:none;width:96pt'>Traditional
  Value</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>CARUSO</td>
  <td class=xl72 width=180 style='border-left:none;width:135pt'>Name</td>
  <td class=xl73 width=87 style='width:65pt'>p/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>3/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>r/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>a/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>s/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>b/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>to/g_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>ft%_hV</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>fg%_hV</td>
  <td class=xl74 width=87 style='width:65pt'>p/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>3/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>r/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>a/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>s/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>b/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>to/g</td>
  <td class=xl75 width=87 style='border-left:none;width:65pt'>ft%</td>
  <td class=xl75 width=87 style='border-left:none;width:65pt'>fg%</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>fta/g</td>
  <td class=xl73 width=87 style='border-left:none;width:65pt'>fga/g</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt'>1</td>
  <td align=right>6</td>
  <td align=right style='background:#F6FAFA;mso-pattern:black none'>7</td>
  <td class=xl67 align=right>0,59</td>
  <td class=xl67 align=right>1,04</td>
  <td>Stephen Curry</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#A1D7B0;
  mso-pattern:black none'>2,07</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>4,59</td>
  <td class=xl67 align=right style='border:none;background:#FBECEF;mso-pattern:
  black none'>0,38</td>
  <td class=xl67 align=right style='border:none;background:#D9EEE1;mso-pattern:
  black none'>1,33</td>
  <td class=xl67 align=right style='border:none;background:#FAD6D9;mso-pattern:
  black none'>-0,01</td>
  <td class=xl67 align=right style='border:none;background:#F88D8F;mso-pattern:
  black none'>-0,41</td>
  <td class=xl67 align=right style='border:none;background:#FAC7C9;mso-pattern:
  black none'>-1,55</td>
  <td class=xl67 align=right style='border:none;background:#C3E5CE;mso-pattern:
  black none'>1,47</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBFAFD;mso-pattern:black none'>1,53</td>
  <td class=xl70 align=right>29,4</td>
  <td class=xl70 align=right>4,9</td>
  <td class=xl70 align=right>6,1</td>
  <td class=xl70 align=right>6,3</td>
  <td class=xl70 align=right>0,9</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>3,2</td>
  <td class=xl71 align=right>91,5%</td>
  <td class=xl71 align=right>49,3%</td>
  <td class=xl70 align=right>5,0</td>
  <td class=xl82 align=right>20,2</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>2</td>
  <td align=right>2</td>
  <td align=right style='background:#FBEDF0;mso-pattern:black none'>2</td>
  <td class=xl67 align=right>0,81</td>
  <td class=xl67 align=right>1,00</td>
  <td>Joel Embiid</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#63BE7B;
  mso-pattern:black none'>2,43</td>
  <td class=xl67 align=right style='border:none;background:#F88A8C;mso-pattern:
  black none'>-0,49</td>
  <td class=xl67 align=right style='border:none;background:#95D2A6;mso-pattern:
  black none'>1,61</td>
  <td class=xl67 align=right style='border:none;background:#FBE5E7;mso-pattern:
  black none'>0,70</td>
  <td class=xl67 align=right style='border:none;background:#FBEEF1;mso-pattern:
  black none'>0,18</td>
  <td class=xl67 align=right style='border:none;background:#99D4AA;mso-pattern:
  black none'>1,66</td>
  <td class=xl67 align=right style='border:none;background:#F9A7A9;mso-pattern:
  black none'>-1,71</td>
  <td class=xl67 align=right style='border:none;background:#9ED6AE;mso-pattern:
  black none'>1,61</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#96D3A7;mso-pattern:black none'>3,05</td>
  <td class=xl70 align=right>33,1</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>10,2</td>
  <td class=xl70 align=right>4,2</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>1,7</td>
  <td class=xl70 align=right>3,4</td>
  <td class=xl71 align=right>85,7%</td>
  <td class=xl71 align=right>54,8%</td>
  <td class=xl70 align=right>11,7</td>
  <td class=xl82 align=right>20,1</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>3</td>
  <td align=right>1</td>
  <td align=right style='background:#FBDADD;mso-pattern:black none'>0</td>
  <td class=xl67 align=right>0,83</td>
  <td class=xl67 align=right>0,99</td>
  <td>Nikola Jokic</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBFBFE;
  mso-pattern:black none'>1,53</td>
  <td class=xl67 align=right style='border:none;background:#F87E80;mso-pattern:
  black none'>-0,62</td>
  <td class=xl67 align=right style='border:none;background:#71C487;mso-pattern:
  black none'>1,98</td>
  <td class=xl67 align=right style='border:none;background:#76C68B;mso-pattern:
  black none'>2,02</td>
  <td class=xl67 align=right style='border:none;background:#CCE9D5;mso-pattern:
  black none'>0,80</td>
  <td class=xl67 align=right style='border:none;background:#ECF6F1;mso-pattern:
  black none'>0,41</td>
  <td class=xl67 align=right style='border:none;background:#F99294;mso-pattern:
  black none'>-1,82</td>
  <td class=xl67 align=right style='border:none;background:#FBE8EB;mso-pattern:
  black none'>0,84</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#63BE7B;mso-pattern:black none'>3,77</td>
  <td class=xl70 align=right>24,5</td>
  <td class=xl70 align=right>0,8</td>
  <td class=xl70 align=right>11,8</td>
  <td class=xl70 align=right>9,8</td>
  <td class=xl70 align=right>1,3</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>3,6</td>
  <td class=xl71 align=right>82,2%</td>
  <td class=xl71 align=right>63,2%</td>
  <td class=xl70 align=right>6,0</td>
  <td class=xl82 align=right>14,8</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>4</td>
  <td align=right>9</td>
  <td align=right style='background:#F6FAFA;mso-pattern:black none'>7</td>
  <td class=xl67 align=right>0,54</td>
  <td class=xl67 align=right>0,93</td>
  <td>Kyrie Irving</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#CCE9D5;
  mso-pattern:black none'>1,82</td>
  <td class=xl67 align=right style='border:none;background:#C5E6CF;mso-pattern:
  black none'>2,16</td>
  <td class=xl67 align=right style='border:none;background:#FABCBE;mso-pattern:
  black none'>-0,05</td>
  <td class=xl67 align=right style='border:none;background:#F6FAFA;mso-pattern:
  black none'>1,13</td>
  <td class=xl67 align=right style='border:none;background:#EFF7F4;mso-pattern:
  black none'>0,43</td>
  <td class=xl67 align=right style='border:none;background:#E3F2EA;mso-pattern:
  black none'>0,54</td>
  <td class=xl67 align=right style='border:none;background:#AFDDBD;mso-pattern:
  black none'>-0,61</td>
  <td class=xl67 align=right style='border:none;background:#DDF0E5;mso-pattern:
  black none'>1,38</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBFAFD;mso-pattern:black none'>1,54</td>
  <td class=xl70 align=right>27,1</td>
  <td class=xl70 align=right>3,1</td>
  <td class=xl70 align=right>5,1</td>
  <td class=xl70 align=right>5,5</td>
  <td class=xl70 align=right>1,1</td>
  <td class=xl70 align=right>0,8</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl71 align=right>90,5%</td>
  <td class=xl71 align=right>49,4%</td>
  <td class=xl70 align=right>4,6</td>
  <td class=xl82 align=right>20,1</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>5</td>
  <td align=right>5</td>
  <td align=right style='background:#FBEDF0;mso-pattern:black none'>2</td>
  <td class=xl67 align=right>0,66</td>
  <td class=xl67 align=right>0,91</td>
  <td>Kevin Durant</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#A7DAB6;
  mso-pattern:black none'>2,03</td>
  <td class=xl67 align=right style='border:none;background:#FBEDF0;mso-pattern:
  black none'>0,60</td>
  <td class=xl67 align=right style='border:none;background:#F6FAFA;mso-pattern:
  black none'>0,59</td>
  <td class=xl67 align=right style='border:none;background:#FBF5F8;mso-pattern:
  black none'>0,98</td>
  <td class=xl67 align=right style='border:none;background:#F88C8E;mso-pattern:
  black none'>-0,62</td>
  <td class=xl67 align=right style='border:none;background:#A9DBB8;mso-pattern:
  black none'>1,42</td>
  <td class=xl67 align=right style='border:none;background:#FAB5B8;mso-pattern:
  black none'>-1,64</td>
  <td class=xl67 align=right style='border:none;background:#8DCF9F;mso-pattern:
  black none'>1,67</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#90D0A2;mso-pattern:black none'>3,14</td>
  <td class=xl70 align=right>29,1</td>
  <td class=xl70 align=right>2,0</td>
  <td class=xl70 align=right>6,7</td>
  <td class=xl70 align=right>5,0</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>1,4</td>
  <td class=xl70 align=right>3,3</td>
  <td class=xl71 align=right>91,9%</td>
  <td class=xl71 align=right>56,0%</td>
  <td class=xl70 align=right>7,1</td>
  <td class=xl82 align=right>18,3</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>6</td>
  <td align=right>3</td>
  <td align=right style='background:#FAD1D4;mso-pattern:black none'>-1</td>
  <td class=xl67 align=right>0,74</td>
  <td class=xl67 align=right>0,86</td>
  <td>Shai Gilgeous-Alexander</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#7FCA93;
  mso-pattern:black none'>2,27</td>
  <td class=xl67 align=right style='border:none;background:#F88082;mso-pattern:
  black none'>-0,60</td>
  <td class=xl67 align=right style='border:none;background:#F9B0B2;mso-pattern:
  black none'>-0,16</td>
  <td class=xl67 align=right style='border:none;background:#F9FBFC;mso-pattern:
  black none'>1,11</td>
  <td class=xl67 align=right style='border:none;background:#82CB96;mso-pattern:
  black none'>1,59</td>
  <td class=xl67 align=right style='border:none;background:#CEE9D7;mso-pattern:
  black none'>0,87</td>
  <td class=xl67 align=right style='border:none;background:#FAFBFD;mso-pattern:
  black none'>-1,25</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>1,82</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#D5EDDE;mso-pattern:black none'>2,14</td>
  <td class=xl70 align=right>31,4</td>
  <td class=xl70 align=right>0,9</td>
  <td class=xl70 align=right>4,8</td>
  <td class=xl70 align=right>5,5</td>
  <td class=xl70 align=right>1,6</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>2,8</td>
  <td class=xl71 align=right>90,5%</td>
  <td class=xl71 align=right>51,0%</td>
  <td class=xl70 align=right>10,9</td>
  <td class=xl82 align=right>20,3</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>7</td>
  <td align=right>12</td>
  <td align=right style='background:#F6FAFA;mso-pattern:black none'>7</td>
  <td class=xl67 align=right>0,47</td>
  <td class=xl67 align=right>0,84</td>
  <td>Kawhi Leonard</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBEEF1;
  mso-pattern:black none'>1,45</td>
  <td class=xl67 align=right style='border:none;background:#FBF0F3;mso-pattern:
  black none'>0,63</td>
  <td class=xl67 align=right style='border:none;background:#FBFCFE;mso-pattern:
  black none'>0,53</td>
  <td class=xl67 align=right style='border:none;background:#FBE0E2;mso-pattern:
  black none'>0,61</td>
  <td class=xl67 align=right style='border:none;background:#B2DEC0;mso-pattern:
  black none'>1,07</td>
  <td class=xl67 align=right style='border:none;background:#FBF0F3;mso-pattern:
  black none'>0,10</td>
  <td class=xl67 align=right style='border:none;background:#75C58A;mso-pattern:
  black none'>-0,10</td>
  <td class=xl67 align=right style='border:none;background:#F7FAFB;mso-pattern:
  black none'>1,29</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#E0F1E7;mso-pattern:black none'>1,98</td>
  <td class=xl70 align=right>23,8</td>
  <td class=xl70 align=right>2,0</td>
  <td class=xl70 align=right>6,5</td>
  <td class=xl70 align=right>3,9</td>
  <td class=xl70 align=right>1,4</td>
  <td class=xl70 align=right>0,5</td>
  <td class=xl70 align=right>1,7</td>
  <td class=xl71 align=right>87,1%</td>
  <td class=xl71 align=right>51,2%</td>
  <td class=xl70 align=right>5,4</td>
  <td class=xl82 align=right>16,8</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>8</td>
  <td align=right>4</td>
  <td align=right style='background:#FAC7CA;mso-pattern:black none'>-2</td>
  <td class=xl67 align=right>0,69</td>
  <td class=xl67 align=right>0,82</td>
  <td>Anthony Davis</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#E1F1E8;
  mso-pattern:black none'>1,69</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-0,86</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>2,13</td>
  <td class=xl67 align=right style='border:none;background:#FABDC0;mso-pattern:
  black none'>0,02</td>
  <td class=xl67 align=right style='border:none;background:#FAFBFD;mso-pattern:
  black none'>0,31</td>
  <td class=xl67 align=right style='border:none;background:#88CD9B;mso-pattern:
  black none'>1,92</td>
  <td class=xl67 align=right style='border:none;background:#B5DFC2;mso-pattern:
  black none'>-0,66</td>
  <td class=xl67 align=right style='border:none;background:#FAB8BA;mso-pattern:
  black none'>-0,22</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#93D2A5;mso-pattern:black none'>3,09</td>
  <td class=xl70 align=right>25,9</td>
  <td class=xl70 align=right>0,3</td>
  <td class=xl70 align=right>12,5</td>
  <td class=xl70 align=right>2,6</td>
  <td class=xl70 align=right>1,1</td>
  <td class=xl70 align=right>2,0</td>
  <td class=xl70 align=right>2,2</td>
  <td class=xl71 align=right>78,4%</td>
  <td class=xl71 align=right>56,3%</td>
  <td class=xl70 align=right>7,9</td>
  <td class=xl82 align=right>17,2</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>9</td>
  <td align=right>15</td>
  <td align=right style='background:#F4F9F8;mso-pattern:black none'>8</td>
  <td class=xl67 align=right>0,42</td>
  <td class=xl67 align=right>0,80</td>
  <td>Donovan Mitchell</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#B6E0C2;
  mso-pattern:black none'>1,95</td>
  <td class=xl67 align=right style='border:none;background:#ABDBB9;mso-pattern:
  black none'>2,81</td>
  <td class=xl67 align=right style='border:none;background:#F98E90;mso-pattern:
  black none'>-0,45</td>
  <td class=xl67 align=right style='border:none;background:#FBEAED;mso-pattern:
  black none'>0,79</td>
  <td class=xl67 align=right style='border:none;background:#A5D9B4;mso-pattern:
  black none'>1,22</td>
  <td class=xl67 align=right style='border:none;background:#F9A6A8;mso-pattern:
  black none'>-0,28</td>
  <td class=xl67 align=right style='border:none;background:#E8F4EE;mso-pattern:
  black none'>-1,10</td>
  <td class=xl67 align=right style='border:none;background:#FBFBFE;mso-pattern:
  black none'>1,26</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBE9EB;mso-pattern:black none'>1,05</td>
  <td class=xl70 align=right>28,3</td>
  <td class=xl70 align=right>3,6</td>
  <td class=xl70 align=right>4,3</td>
  <td class=xl70 align=right>4,4</td>
  <td class=xl70 align=right>1,5</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>2,6</td>
  <td class=xl71 align=right>86,7%</td>
  <td class=xl71 align=right>48,4%</td>
  <td class=xl70 align=right>5,4</td>
  <td class=xl82 align=right>20,6</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>10</td>
  <td align=right>10</td>
  <td align=right style='background:#FBEDF0;mso-pattern:black none'>2</td>
  <td class=xl67 align=right>0,53</td>
  <td class=xl67 align=right>0,79</td>
  <td>Jimmy Butler</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBDCDF;
  mso-pattern:black none'>1,34</td>
  <td class=xl67 align=right style='border:none;background:#F87072;mso-pattern:
  black none'>-0,77</td>
  <td class=xl67 align=right style='border:none;background:#FBE2E5;mso-pattern:
  black none'>0,29</td>
  <td class=xl67 align=right style='border:none;background:#FBFBFE;mso-pattern:
  black none'>1,07</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>1,91</td>
  <td class=xl67 align=right style='border:none;background:#F8797B;mso-pattern:
  black none'>-0,52</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>0,05</td>
  <td class=xl67 align=right style='border:none;background:#D8EEE0;mso-pattern:
  black none'>1,40</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#C5E6CF;mso-pattern:black none'>2,37</td>
  <td class=xl70 align=right>22,9</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>5,9</td>
  <td class=xl70 align=right>5,3</td>
  <td class=xl70 align=right>1,8</td>
  <td class=xl70 align=right>0,3</td>
  <td class=xl70 align=right>1,6</td>
  <td class=xl71 align=right>85,0%</td>
  <td class=xl71 align=right>53,9%</td>
  <td class=xl70 align=right>8,7</td>
  <td class=xl82 align=right>13,9</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>11</td>
  <td align=right>14</td>
  <td align=right style='background:#FAFBFD;mso-pattern:black none'>5</td>
  <td class=xl67 align=right>0,45</td>
  <td class=xl67 align=right>0,76</td>
  <td>Kristaps Porzingis</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBE1E4;
  mso-pattern:black none'>1,37</td>
  <td class=xl67 align=right style='border:none;background:#FCFCFF;mso-pattern:
  black none'>0,77</td>
  <td class=xl67 align=right style='border:none;background:#C1E4CC;mso-pattern:
  black none'>1,15</td>
  <td class=xl67 align=right style='border:none;background:#FABEC1;mso-pattern:
  black none'>0,04</td>
  <td class=xl67 align=right style='border:none;background:#FACACD;mso-pattern:
  black none'>-0,11</td>
  <td class=xl67 align=right style='border:none;background:#A2D8B1;mso-pattern:
  black none'>1,52</td>
  <td class=xl67 align=right style='border:none;background:#ACDCBA;mso-pattern:
  black none'>-0,58</td>
  <td class=xl67 align=right style='border:none;background:#FBFAFD;mso-pattern:
  black none'>1,23</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBF8FB;mso-pattern:black none'>1,47</td>
  <td class=xl70 align=right>23,2</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl70 align=right>8,4</td>
  <td class=xl70 align=right>2,7</td>
  <td class=xl70 align=right>0,9</td>
  <td class=xl70 align=right>1,5</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl71 align=right>85,1%</td>
  <td class=xl71 align=right>49,8%</td>
  <td class=xl70 align=right>6,4</td>
  <td class=xl82 align=right>15,7</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>12</td>
  <td align=right>11</td>
  <td align=right style='background:#FBE4E7;mso-pattern:black none'>1</td>
  <td class=xl67 align=right>0,50</td>
  <td class=xl67 align=right>0,76</td>
  <td>Jayson Tatum</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#96D3A7;
  mso-pattern:black none'>2,14</td>
  <td class=xl67 align=right style='border:none;background:#BFE3CA;mso-pattern:
  black none'>2,31</td>
  <td class=xl67 align=right style='border:none;background:#B7E0C3;mso-pattern:
  black none'>1,25</td>
  <td class=xl67 align=right style='border:none;background:#FBEEF1;mso-pattern:
  black none'>0,86</td>
  <td class=xl67 align=right style='border:none;background:#FAFBFD;mso-pattern:
  black none'>0,31</td>
  <td class=xl67 align=right style='border:none;background:#EBF5F0;mso-pattern:
  black none'>0,43</td>
  <td class=xl67 align=right style='border:none;background:#FBF7FA;mso-pattern:
  black none'>-1,30</td>
  <td class=xl67 align=right style='border:none;background:#D5EDDE;mso-pattern:
  black none'>1,41</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F9ADAF;mso-pattern:black none'>-0,59</td>
  <td class=xl70 align=right>30,1</td>
  <td class=xl70 align=right>3,2</td>
  <td class=xl70 align=right>8,8</td>
  <td class=xl70 align=right>4,6</td>
  <td class=xl70 align=right>1,1</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>2,9</td>
  <td class=xl71 align=right>85,4%</td>
  <td class=xl71 align=right>46,6%</td>
  <td class=xl70 align=right>8,4</td>
  <td class=xl82 align=right>21,1</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>13</td>
  <td align=right>8</td>
  <td align=right style='background:#FABEC0;mso-pattern:black none'>-3</td>
  <td class=xl67 align=right>0,55</td>
  <td class=xl67 align=right>0,75</td>
  <td>Tyrese Haliburton</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F9AFB1;
  mso-pattern:black none'>1,05</td>
  <td class=xl67 align=right style='border:none;background:#D3ECDC;mso-pattern:
  black none'>1,80</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-0,79</td>
  <td class=xl67 align=right style='border:none;background:#68C07F;mso-pattern:
  black none'>2,12</td>
  <td class=xl67 align=right style='border:none;background:#86CC99;mso-pattern:
  black none'>1,55</td>
  <td class=xl67 align=right style='border:none;background:#FAC2C5;mso-pattern:
  black none'>-0,14</td>
  <td class=xl67 align=right style='border:none;background:#DBEFE3;mso-pattern:
  black none'>-0,99</td>
  <td class=xl67 align=right style='border:none;background:#FBF2F4;mso-pattern:
  black none'>1,05</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBEAED;mso-pattern:black none'>1,09</td>
  <td class=xl70 align=right>20,7</td>
  <td class=xl70 align=right>2,9</td>
  <td class=xl70 align=right>3,7</td>
  <td class=xl70 align=right>10,4</td>
  <td class=xl70 align=right>1,6</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>2,5</td>
  <td class=xl71 align=right>87,1%</td>
  <td class=xl71 align=right>49,0%</td>
  <td class=xl70 align=right>3,6</td>
  <td class=xl82 align=right>15,0</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>14</td>
  <td align=right>16</td>
  <td align=right style='background:#FCFCFF;mso-pattern:black none'>4</td>
  <td class=xl67 align=right>0,40</td>
  <td class=xl67 align=right>0,73</td>
  <td>Luka Doncic</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#6FC385;
  mso-pattern:black none'>2,37</td>
  <td class=xl67 align=right style='border:none;background:#D7EDDF;mso-pattern:
  black none'>1,70</td>
  <td class=xl67 align=right style='border:none;background:#BBE2C7;mso-pattern:
  black none'>1,21</td>
  <td class=xl67 align=right style='border:none;background:#A3D8B3;mso-pattern:
  black none'>1,71</td>
  <td class=xl67 align=right style='border:none;background:#B6E0C3;mso-pattern:
  black none'>1,03</td>
  <td class=xl67 align=right style='border:none;background:#FBDEE1;mso-pattern:
  black none'>0,01</td>
  <td class=xl67 align=right style='border:none;background:#F99294;mso-pattern:
  black none'>-1,82</td>
  <td class=xl67 align=right style='border:none;background:#F88487;mso-pattern:
  black none'>-1,34</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F1F8F6;mso-pattern:black none'>1,73</td>
  <td class=xl70 align=right>32,4</td>
  <td class=xl70 align=right>2,8</td>
  <td class=xl70 align=right>8,6</td>
  <td class=xl70 align=right>8,0</td>
  <td class=xl70 align=right>1,4</td>
  <td class=xl70 align=right>0,5</td>
  <td class=xl70 align=right>3,6</td>
  <td class=xl71 align=right>74,2%</td>
  <td class=xl71 align=right>49,6%</td>
  <td class=xl70 align=right>10,5</td>
  <td class=xl82 align=right>22,0</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>15</td>
  <td align=right>19</td>
  <td align=right style='background:#F8FBFB;mso-pattern:black none'>6</td>
  <td class=xl67 align=right>0,33</td>
  <td class=xl67 align=right>0,70</td>
  <td>Lauri Markkanen</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#E7F4ED;
  mso-pattern:black none'>1,66</td>
  <td class=xl67 align=right style='border:none;background:#CAE8D4;mso-pattern:
  black none'>2,01</td>
  <td class=xl67 align=right style='border:none;background:#BAE2C6;mso-pattern:
  black none'>1,21</td>
  <td class=xl67 align=right style='border:none;background:#F99FA1;mso-pattern:
  black none'>-0,49</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-0,90</td>
  <td class=xl67 align=right style='border:none;background:#FBFCFE;mso-pattern:
  black none'>0,19</td>
  <td class=xl67 align=right style='border:none;background:#95D3A6;mso-pattern:
  black none'>-0,38</td>
  <td class=xl67 align=right style='border:none;background:#DEF0E5;mso-pattern:
  black none'>1,38</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBFCFE;mso-pattern:black none'>1,59</td>
  <td class=xl70 align=right>25,6</td>
  <td class=xl70 align=right>3,0</td>
  <td class=xl70 align=right>8,6</td>
  <td class=xl70 align=right>1,9</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>1,9</td>
  <td class=xl71 align=right>87,5%</td>
  <td class=xl71 align=right>49,9%</td>
  <td class=xl70 align=right>6,0</td>
  <td class=xl82 align=right>17,3</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>16</td>
  <td align=right>7</td>
  <td align=right style='background:#F9989A;mso-pattern:black none'>-7</td>
  <td class=xl67 align=right>0,57</td>
  <td class=xl67 align=right>0,66</td>
  <td>Damian Lillard</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#72C488;
  mso-pattern:black none'>2,35</td>
  <td class=xl67 align=right style='border:none;background:#89CE9C;mso-pattern:
  black none'>3,65</td>
  <td class=xl67 align=right style='border:none;background:#F9ACAF;mso-pattern:
  black none'>-0,19</td>
  <td class=xl67 align=right style='border:none;background:#B7E0C4;mso-pattern:
  black none'>1,57</td>
  <td class=xl67 align=right style='border:none;background:#FABFC2;mso-pattern:
  black none'>-0,20</td>
  <td class=xl67 align=right style='border:none;background:#F86C6E;mso-pattern:
  black none'>-0,58</td>
  <td class=xl67 align=right style='border:none;background:#FAB9BB;mso-pattern:
  black none'>-1,62</td>
  <td class=xl67 align=right style='border:none;background:#6AC181;mso-pattern:
  black none'>1,79</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F9A4A6;mso-pattern:black none'>-0,84</td>
  <td class=xl70 align=right>32,2</td>
  <td class=xl70 align=right>4,2</td>
  <td class=xl70 align=right>4,8</td>
  <td class=xl70 align=right>7,3</td>
  <td class=xl70 align=right>0,9</td>
  <td class=xl70 align=right>0,3</td>
  <td class=xl70 align=right>3,3</td>
  <td class=xl71 align=right>91,4%</td>
  <td class=xl71 align=right>46,3%</td>
  <td class=xl70 align=right>9,6</td>
  <td class=xl82 align=right>20,7</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>17</td>
  <td align=right>22</td>
  <td align=right style='background:#F6FAFA;mso-pattern:black none'>7</td>
  <td class=xl67 align=right>0,26</td>
  <td class=xl67 align=right>0,59</td>
  <td>LeBron James</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#AADBB8;
  mso-pattern:black none'>2,02</td>
  <td class=xl67 align=right style='border:none;background:#F7FAFB;mso-pattern:
  black none'>0,89</td>
  <td class=xl67 align=right style='border:none;background:#C3E5CE;mso-pattern:
  black none'>1,12</td>
  <td class=xl67 align=right style='border:none;background:#C7E7D2;mso-pattern:
  black none'>1,46</td>
  <td class=xl67 align=right style='border:none;background:#FAD0D2;mso-pattern:
  black none'>-0,07</td>
  <td class=xl67 align=right style='border:none;background:#FAFBFD;mso-pattern:
  black none'>0,21</td>
  <td class=xl67 align=right style='border:none;background:#FAC1C4;mso-pattern:
  black none'>-1,58</td>
  <td class=xl67 align=right style='border:none;background:#F9A4A7;mso-pattern:
  black none'>-0,65</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#E7F4ED;mso-pattern:black none'>1,88</td>
  <td class=xl70 align=right>28,9</td>
  <td class=xl70 align=right>2,2</td>
  <td class=xl70 align=right>8,3</td>
  <td class=xl70 align=right>6,8</td>
  <td class=xl70 align=right>0,9</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>3,2</td>
  <td class=xl71 align=right>76,8%</td>
  <td class=xl71 align=right>50,0%</td>
  <td class=xl70 align=right>5,9</td>
  <td class=xl82 align=right>22,2</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>18</td>
  <td align=right>26</td>
  <td align=right style='background:#F0F7F5;mso-pattern:black none'>10</td>
  <td class=xl67 align=right>0,23</td>
  <td class=xl67 align=right>0,54</td>
  <td>Nikola Vucevic</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F8696B;
  mso-pattern:black none'>0,62</td>
  <td class=xl67 align=right style='border:none;background:#FAB5B8;mso-pattern:
  black none'>-0,01</td>
  <td class=xl67 align=right style='border:none;background:#82CB96;mso-pattern:
  black none'>1,81</td>
  <td class=xl67 align=right style='border:none;background:#FACFD1;mso-pattern:
  black none'>0,32</td>
  <td class=xl67 align=right style='border:none;background:#F98F91;mso-pattern:
  black none'>-0,59</td>
  <td class=xl67 align=right style='border:none;background:#EAF5F0;mso-pattern:
  black none'>0,44</td>
  <td class=xl67 align=right style='border:none;background:#75C68B;mso-pattern:
  black none'>-0,10</td>
  <td class=xl67 align=right style='border:none;background:#FAD3D6;mso-pattern:
  black none'>0,39</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#E0F1E7;mso-pattern:black none'>1,98</td>
  <td class=xl70 align=right>17,6</td>
  <td class=xl70 align=right>1,5</td>
  <td class=xl70 align=right>11,0</td>
  <td class=xl70 align=right>3,2</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>1,7</td>
  <td class=xl71 align=right>83,5%</td>
  <td class=xl71 align=right>52,0%</td>
  <td class=xl70 align=right>1,9</td>
  <td class=xl82 align=right>14,0</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>19</td>
  <td align=right>29</td>
  <td align=right style='background:#ECF6F1;mso-pattern:black none'>12</td>
  <td class=xl67 align=right>0,17</td>
  <td class=xl67 align=right>0,53</td>
  <td>Devin Booker</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#BFE4CA;
  mso-pattern:black none'>1,89</td>
  <td class=xl67 align=right style='border:none;background:#FBFBFE;mso-pattern:
  black none'>0,75</td>
  <td class=xl67 align=right style='border:none;background:#F99EA1;mso-pattern:
  black none'>-0,31</td>
  <td class=xl67 align=right style='border:none;background:#F6FAFA;mso-pattern:
  black none'>1,13</td>
  <td class=xl67 align=right style='border:none;background:#FBE2E4;mso-pattern:
  black none'>0,08</td>
  <td class=xl67 align=right style='border:none;background:#F88183;mso-pattern:
  black none'>-0,48</td>
  <td class=xl67 align=right style='border:none;background:#F1F8F6;mso-pattern:
  black none'>-1,18</td>
  <td class=xl67 align=right style='border:none;background:#F4F9F8;mso-pattern:
  black none'>1,30</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBFBFE;mso-pattern:black none'>1,55</td>
  <td class=xl70 align=right>27,8</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl70 align=right>4,5</td>
  <td class=xl70 align=right>5,5</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>0,3</td>
  <td class=xl70 align=right>2,7</td>
  <td class=xl71 align=right>85,5%</td>
  <td class=xl71 align=right>49,4%</td>
  <td class=xl70 align=right>6,8</td>
  <td class=xl82 align=right>20,1</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>20</td>
  <td align=right>28</td>
  <td align=right style='background:#F0F7F5;mso-pattern:black none'>10</td>
  <td class=xl67 align=right>0,19</td>
  <td class=xl67 align=right>0,52</td>
  <td>DeMar DeRozan</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FCFCFF;
  mso-pattern:black none'>1,53</td>
  <td class=xl67 align=right style='border:none;background:#F87375;mso-pattern:
  black none'>-0,75</td>
  <td class=xl67 align=right style='border:none;background:#F9A4A7;mso-pattern:
  black none'>-0,26</td>
  <td class=xl67 align=right style='border:none;background:#FBF7FA;mso-pattern:
  black none'>1,01</td>
  <td class=xl67 align=right style='border:none;background:#EAF5F0;mso-pattern:
  black none'>0,48</td>
  <td class=xl67 align=right style='border:none;background:#FBD7DA;mso-pattern:
  black none'>-0,03</td>
  <td class=xl67 align=right style='border:none;background:#A7DAB6;mso-pattern:
  black none'>-0,54</td>
  <td class=xl67 align=right style='border:none;background:#CCE9D5;mso-pattern:
  black none'>1,44</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#EDF6F2;mso-pattern:black none'>1,80</td>
  <td class=xl70 align=right>24,5</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>4,6</td>
  <td class=xl70 align=right>5,1</td>
  <td class=xl70 align=right>1,1</td>
  <td class=xl70 align=right>0,5</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl71 align=right>87,2%</td>
  <td class=xl71 align=right>50,4%</td>
  <td class=xl70 align=right>7,1</td>
  <td class=xl82 align=right>17,6</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>21</td>
  <td align=right>39</td>
  <td align=right style='background:#DCEFE4;mso-pattern:black none'>20</td>
  <td class=xl67 align=right>0,11</td>
  <td class=xl67 align=right>0,49</td>
  <td>Karl-Anthony Towns</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F9B0B2;
  mso-pattern:black none'>1,06</td>
  <td class=xl67 align=right style='border:none;background:#FBF8FB;mso-pattern:
  black none'>0,72</td>
  <td class=xl67 align=right style='border:none;background:#C8E7D2;mso-pattern:
  black none'>1,07</td>
  <td class=xl67 align=right style='border:none;background:#FBF2F5;mso-pattern:
  black none'>0,93</td>
  <td class=xl67 align=right style='border:none;background:#F87E81;mso-pattern:
  black none'>-0,73</td>
  <td class=xl67 align=right style='border:none;background:#F9FBFC;mso-pattern:
  black none'>0,22</td>
  <td class=xl67 align=right style='border:none;background:#FBE9EC;mso-pattern:
  black none'>-1,37</td>
  <td class=xl67 align=right style='border:none;background:#FBFAFD;mso-pattern:
  black none'>1,23</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBF2F5;mso-pattern:black none'>1,31</td>
  <td class=xl70 align=right>20,8</td>
  <td class=xl70 align=right>2,1</td>
  <td class=xl70 align=right>8,1</td>
  <td class=xl70 align=right>4,8</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>3,0</td>
  <td class=xl71 align=right>87,4%</td>
  <td class=xl71 align=right>49,5%</td>
  <td class=xl70 align=right>4,7</td>
  <td class=xl82 align=right>14,8</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>22</td>
  <td align=right>35</td>
  <td align=right style='background:#E6F3EC;mso-pattern:black none'>15</td>
  <td class=xl67 align=right>0,14</td>
  <td class=xl67 align=right>0,49</td>
  <td>Desmond Bane</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FABFC2;
  mso-pattern:black none'>1,16</td>
  <td class=xl67 align=right style='border:none;background:#D4ECDC;mso-pattern:
  black none'>1,78</td>
  <td class=xl67 align=right style='border:none;background:#FAB9BC;mso-pattern:
  black none'>-0,07</td>
  <td class=xl67 align=right style='border:none;background:#FBE9EC;mso-pattern:
  black none'>0,78</td>
  <td class=xl67 align=right style='border:none;background:#FBE3E6;mso-pattern:
  black none'>0,09</td>
  <td class=xl67 align=right style='border:none;background:#F99B9D;mso-pattern:
  black none'>-0,34</td>
  <td class=xl67 align=right style='border:none;background:#B4DFC1;mso-pattern:
  black none'>-0,65</td>
  <td class=xl67 align=right style='border:none;background:#FBF5F8;mso-pattern:
  black none'>1,12</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FAD5D8;mso-pattern:black none'>0,51</td>
  <td class=xl70 align=right>21,5</td>
  <td class=xl70 align=right>2,9</td>
  <td class=xl70 align=right>5,0</td>
  <td class=xl70 align=right>4,4</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>2,2</td>
  <td class=xl71 align=right>88,3%</td>
  <td class=xl71 align=right>47,9%</td>
  <td class=xl70 align=right>3,5</td>
  <td class=xl82 align=right>16,2</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>23</td>
  <td align=right>17</td>
  <td align=right style='background:#FAB4B7;mso-pattern:black none'>-4</td>
  <td class=xl67 align=right>0,39</td>
  <td class=xl67 align=right>0,48</td>
  <td>James Harden</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FAB4B6;
  mso-pattern:black none'>1,09</td>
  <td class=xl67 align=right style='border:none;background:#D8EEE0;mso-pattern:
  black none'>1,66</td>
  <td class=xl67 align=right style='border:none;background:#FBECEF;mso-pattern:
  black none'>0,38</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>2,15</td>
  <td class=xl67 align=right style='border:none;background:#D3ECDC;mso-pattern:
  black none'>0,72</td>
  <td class=xl67 align=right style='border:none;background:#FBEFF1;mso-pattern:
  black none'>0,10</td>
  <td class=xl67 align=right style='border:none;background:#F9AFB2;mso-pattern:
  black none'>-1,67</td>
  <td class=xl67 align=right style='border:none;background:#E8F4ED;mso-pattern:
  black none'>1,34</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F98E90;mso-pattern:black none'>-1,44</td>
  <td class=xl70 align=right>21,0</td>
  <td class=xl70 align=right>2,8</td>
  <td class=xl70 align=right>6,1</td>
  <td class=xl70 align=right>10,7</td>
  <td class=xl70 align=right>1,2</td>
  <td class=xl70 align=right>0,5</td>
  <td class=xl70 align=right>3,4</td>
  <td class=xl71 align=right>86,7%</td>
  <td class=xl71 align=right>44,1%</td>
  <td class=xl70 align=right>6,2</td>
  <td class=xl82 align=right>14,5</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>24</td>
  <td align=right>104</td>
  <td align=right style='background:#63BE7B;mso-pattern:black none'>82</td>
  <td class=xl67 align=right>-0,17</td>
  <td class=xl67 align=right>0,47</td>
  <td>Giannis Antetokounmpo</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#84CC98;
  mso-pattern:black none'>2,24</td>
  <td class=xl67 align=right style='border:none;background:#F8797B;mso-pattern:
  black none'>-0,67</td>
  <td class=xl67 align=right style='border:none;background:#72C488;mso-pattern:
  black none'>1,97</td>
  <td class=xl67 align=right style='border:none;background:#EFF7F4;mso-pattern:
  black none'>1,18</td>
  <td class=xl67 align=right style='border:none;background:#FAB2B5;mso-pattern:
  black none'>-0,31</td>
  <td class=xl67 align=right style='border:none;background:#DDF0E4;mso-pattern:
  black none'>0,64</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-2,03</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-1,95</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#8ED0A0;mso-pattern:black none'>3,16</td>
  <td class=xl70 align=right>31,1</td>
  <td class=xl70 align=right>0,7</td>
  <td class=xl70 align=right>11,8</td>
  <td class=xl70 align=right>5,7</td>
  <td class=xl70 align=right>0,8</td>
  <td class=xl70 align=right>0,8</td>
  <td class=xl70 align=right>3,9</td>
  <td class=xl71 align=right>64,5%</td>
  <td class=xl71 align=right>55,3%</td>
  <td class=xl70 align=right>12,3</td>
  <td class=xl82 align=right>20,3</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>25</td>
  <td align=right>21</td>
  <td align=right style='background:#FAC7CA;mso-pattern:black none'>-2</td>
  <td class=xl67 align=right>0,27</td>
  <td class=xl67 align=right>0,46</td>
  <td>Domantas Sabonis</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F88B8D;
  mso-pattern:black none'>0,84</td>
  <td class=xl67 align=right style='border:none;background:#F86A6C;mso-pattern:
  black none'>-0,84</td>
  <td class=xl67 align=right style='border:none;background:#68C07F;mso-pattern:
  black none'>2,08</td>
  <td class=xl67 align=right style='border:none;background:#BAE1C6;mso-pattern:
  black none'>1,55</td>
  <td class=xl67 align=right style='border:none;background:#F9B1B4;mso-pattern:
  black none'>-0,31</td>
  <td class=xl67 align=right style='border:none;background:#FBDBDE;mso-pattern:
  black none'>-0,01</td>
  <td class=xl67 align=right style='border:none;background:#FBF2F5;mso-pattern:
  black none'>-1,33</td>
  <td class=xl67 align=right style='border:none;background:#F99395;mso-pattern:
  black none'>-1,02</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#8BCE9D;mso-pattern:black none'>3,21</td>
  <td class=xl70 align=right>19,1</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>12,3</td>
  <td class=xl70 align=right>7,3</td>
  <td class=xl70 align=right>0,8</td>
  <td class=xl70 align=right>0,5</td>
  <td class=xl70 align=right>2,9</td>
  <td class=xl71 align=right>74,2%</td>
  <td class=xl71 align=right>61,5%</td>
  <td class=xl70 align=right>5,5</td>
  <td class=xl82 align=right>11,9</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>26</td>
  <td align=right>25</td>
  <td align=right style='background:#FBE4E7;mso-pattern:black none'>1</td>
  <td class=xl67 align=right>0,25</td>
  <td class=xl67 align=right>0,46</td>
  <td>Paul George</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBEDF0;
  mso-pattern:black none'>1,44</td>
  <td class=xl67 align=right style='border:none;background:#D6EDDE;mso-pattern:
  black none'>1,73</td>
  <td class=xl67 align=right style='border:none;background:#FBEDF0;mso-pattern:
  black none'>0,39</td>
  <td class=xl67 align=right style='border:none;background:#FBF8FB;mso-pattern:
  black none'>1,02</td>
  <td class=xl67 align=right style='border:none;background:#A0D7AF;mso-pattern:
  black none'>1,27</td>
  <td class=xl67 align=right style='border:none;background:#F88D8F;mso-pattern:
  black none'>-0,41</td>
  <td class=xl67 align=right style='border:none;background:#FACFD1;mso-pattern:
  black none'>-1,51</td>
  <td class=xl67 align=right style='border:none;background:#FBFCFE;mso-pattern:
  black none'>1,27</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F99C9F;mso-pattern:black none'>-1,04</td>
  <td class=xl70 align=right>23,8</td>
  <td class=xl70 align=right>2,8</td>
  <td class=xl70 align=right>6,1</td>
  <td class=xl70 align=right>5,1</td>
  <td class=xl70 align=right>1,5</td>
  <td class=xl70 align=right>0,4</td>
  <td class=xl70 align=right>3,1</td>
  <td class=xl71 align=right>87,1%</td>
  <td class=xl71 align=right>45,7%</td>
  <td class=xl70 align=right>5,3</td>
  <td class=xl82 align=right>17,9</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>27</td>
  <td align=right>13</td>
  <td align=right style='background:#F8696B;mso-pattern:black none'>-12</td>
  <td class=xl67 align=right>0,47</td>
  <td class=xl67 align=right>0,46</td>
  <td>Jaren Jackson Jr.</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F88082;
  mso-pattern:black none'>0,77</td>
  <td class=xl67 align=right style='border:none;background:#FAC1C4;mso-pattern:
  black none'>0,12</td>
  <td class=xl67 align=right style='border:none;background:#F2F8F7;mso-pattern:
  black none'>0,63</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-1,43</td>
  <td class=xl67 align=right style='border:none;background:#FBF8FB;mso-pattern:
  black none'>0,26</td>
  <td class=xl67 align=right style='border:none;background:#63BE7B;mso-pattern:
  black none'>2,46</td>
  <td class=xl67 align=right style='border:none;background:#76C68B;mso-pattern:
  black none'>-0,11</td>
  <td class=xl67 align=right style='border:none;background:#FABEC0;mso-pattern:
  black none'>-0,09</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#FBFBFE;mso-pattern:black none'>1,55</td>
  <td class=xl70 align=right>18,6</td>
  <td class=xl70 align=right>1,6</td>
  <td class=xl70 align=right>6,8</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>1,0</td>
  <td class=xl70 align=right>3,0</td>
  <td class=xl70 align=right>1,7</td>
  <td class=xl71 align=right>78,8%</td>
  <td class=xl71 align=right>50,6%</td>
  <td class=xl70 align=right>4,9</td>
  <td class=xl82 align=right>13,0</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>28</td>
  <td align=right>18</td>
  <td align=right style='background:#F98E91;mso-pattern:black none'>-8</td>
  <td class=xl67 align=right>0,34</td>
  <td class=xl67 align=right>0,46</td>
  <td>Fred VanVleet</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#F99093;
  mso-pattern:black none'>0,87</td>
  <td class=xl67 align=right style='border:none;background:#CCE9D6;mso-pattern:
  black none'>1,97</td>
  <td class=xl67 align=right style='border:none;background:#F88285;mso-pattern:
  black none'>-0,56</td>
  <td class=xl67 align=right style='border:none;background:#BCE2C8;mso-pattern:
  black none'>1,54</td>
  <td class=xl67 align=right style='border:none;background:#6BC182;mso-pattern:
  black none'>1,83</td>
  <td class=xl67 align=right style='border:none;background:#FBF6F9;mso-pattern:
  black none'>0,13</td>
  <td class=xl67 align=right style='border:none;background:#A2D8B2;mso-pattern:
  black none'>-0,50</td>
  <td class=xl67 align=right style='border:none;background:#F7FAFB;mso-pattern:
  black none'>1,29</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F8696B;mso-pattern:black none'>-2,46</td>
  <td class=xl70 align=right>19,3</td>
  <td class=xl70 align=right>3,0</td>
  <td class=xl70 align=right>4,1</td>
  <td class=xl70 align=right>7,2</td>
  <td class=xl70 align=right>1,8</td>
  <td class=xl70 align=right>0,6</td>
  <td class=xl70 align=right>2,0</td>
  <td class=xl71 align=right>89,8%</td>
  <td class=xl71 align=right>39,3%</td>
  <td class=xl70 align=right>4,1</td>
  <td class=xl82 align=right>16,1</td>
 </tr>
 <tr height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>29</td>
  <td align=right>30</td>
  <td align=right style='background:#FBF7FA;mso-pattern:black none'>3</td>
  <td class=xl67 align=right>0,17</td>
  <td class=xl67 align=right>0,41</td>
  <td>LaMelo Ball</td>
  <td class=xl68 align=right style='border-top:none;border-right:none;
  border-bottom:none;border-left:.5pt solid windowtext;background:#FBE3E6;
  mso-pattern:black none'>1,38</td>
  <td class=xl67 align=right style='border:none;background:#95D2A6;mso-pattern:
  black none'>3,36</td>
  <td class=xl67 align=right style='border:none;background:#FBFAFD;mso-pattern:
  black none'>0,50</td>
  <td class=xl67 align=right style='border:none;background:#98D4A8;mso-pattern:
  black none'>1,79</td>
  <td class=xl67 align=right style='border:none;background:#C8E7D2;mso-pattern:
  black none'>0,84</td>
  <td class=xl67 align=right style='border:none;background:#F8696B;mso-pattern:
  black none'>-0,60</td>
  <td class=xl67 align=right style='border:none;background:#F99193;mso-pattern:
  black none'>-1,82</td>
  <td class=xl67 align=right style='border:none;background:#FBE1E4;mso-pattern:
  black none'>0,70</td>
  <td class=xl69 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:none;border-left:none;background:#F86A6C;mso-pattern:black none'>-2,42</td>
  <td class=xl70 align=right>23,3</td>
  <td class=xl70 align=right>4,0</td>
  <td class=xl70 align=right>6,4</td>
  <td class=xl70 align=right>8,4</td>
  <td class=xl70 align=right>1,3</td>
  <td class=xl70 align=right>0,3</td>
  <td class=xl70 align=right>3,6</td>
  <td class=xl71 align=right>83,6%</td>
  <td class=xl71 align=right>41,1%</td>
  <td class=xl70 align=right>3,4</td>
  <td class=xl82 align=right>20,0</td>
 </tr>
 <tr class=xl76 height=21 style='height:16.0pt'>
  <td height=21 class=xl66 style='height:16.0pt;border-top:none'>30</td>
  <td class=xl76 align=right>55</td>
  <td class=xl76 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#CFEAD8;
  mso-pattern:black none'>27</td>
  <td class=xl77 align=right>0,02</td>
  <td class=xl77 align=right>0,41</td>
  <td class=xl76>Bradley Beal</td>
  <td class=xl78 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:.5pt solid windowtext;
  background:#FBE2E5;mso-pattern:black none'>1,37</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#FAC3C5;
  mso-pattern:black none'>0,13</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#F87A7C;
  mso-pattern:black none'>-0,64</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#FAFCFE;
  mso-pattern:black none'>1,10</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#FACDCF;
  mso-pattern:black none'>-0,09</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#EFF7F4;
  mso-pattern:black none'>0,37</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#FBF4F7;
  mso-pattern:black none'>-1,32</td>
  <td class=xl77 align=right style='border-top:none;border-right:none;
  border-bottom:.5pt solid windowtext;border-left:none;background:#FBEDF0;
  mso-pattern:black none'>0,95</td>
  <td class=xl79 align=right style='border-top:none;border-right:.5pt solid windowtext;
  border-bottom:.5pt solid windowtext;border-left:none;background:#E9F5EF;
  mso-pattern:black none'>1,84</td>
  <td class=xl80 align=right>23,2</td>
  <td class=xl80 align=right>1,6</td>
  <td class=xl80 align=right>3,9</td>
  <td class=xl80 align=right>5,4</td>
  <td class=xl80 align=right>0,9</td>
  <td class=xl80 align=right>0,7</td>
  <td class=xl80 align=right>2,9</td>
  <td class=xl81 align=right>84,2%</td>
  <td class=xl81 align=right>50,6%</td>
  <td class=xl80 align=right>4,6</td>
  <td class=xl83 align=right>17,6</td>
 </tr>
 <![if supportMisalignedColumns]>
 <tr height=0 style='display:none'>
  <td width=100 style='width:75pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=128 style='width:96pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=180 style='width:135pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
  <td width=87 style='width:65pt'></td>
 </tr>
 <![endif]>
</table>

</body>

</html>



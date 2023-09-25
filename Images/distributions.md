```python
from nba_api.stats.endpoints import leagueleaders
import pandas as pd
import matplotlib.pyplot as plt
import plotly.express as px
from nba_api.stats.endpoints import leaguedashplayerstats, synergyplaytypes
from nba_api.stats.endpoints import teamyearbyyearstats, playercareerstats
from nba_api.stats.static.players import find_players_by_full_name, find_player_by_id

from matplotlib.offsetbox import OffsetImage, AnnotationBbox, DrawingArea, TextArea
from matplotlib.patches import Circle, FancyArrowPatch, FancyArrow, ArrowStyle, Arrow
import matplotlib.ticker as mtick
import time
from tqdm import tqdm
from sklearn.cluster import KMeans
import numpy as np
import functools
import seaborn as sns
import glob
from sklearn import preprocessing
from scipy import stats
from fitter import Fitter, get_common_distributions, get_distributions
import sklearn
from sklearn.mixture import GaussianMixture, BayesianGaussianMixture
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import GridSearchCV
from scipy.stats import gamma, exponpow, lognorm, cauchy, genhyperbolic

from IPython.core.display import display, HTML
display(HTML("<style>.container { width:100% !important; }</style>"))

plt.style.use('fivethirtyeight')

import warnings
warnings.filterwarnings('ignore')
```


<style>.container { width:100% !important; }</style>



```python
files = glob.glob('/Users/franhuzjan/Downloads/Rankings/*')

df = pd.DataFrame()

for file in files:
    excel_df = pd.read_excel(file)
    df = pd.concat([df, excel_df])
```


```python
cats_dict = {'p/g':1.5,
             '3/g':0.25,
             'r/g':1,
             'a/g':0.5,
             's/g':0.1,
             'b/g':0.2,
             'fg%':0.02,
             'ft%':0.05,
             'to/g':0.25}



plt.figure(figsize=(20, 20))


for i, (cat, binwidth) in enumerate(cats_dict.items()):
    ax = plt.subplot(3, 3, i + 1)
    
    range_ = (max(df[cat]) - min(df[cat]))
    bins = int(range_ / binwidth)
    if bins == 0:
        bins = 15
    sns.distplot(df[cat], hist = True, kde = True, 
                 kde_kws = {'linewidth': 3}, hist_kws={'edgecolor': 'black'}, bins=bins)

    ax.set_title(f'{cat} distribution')
    
plt.savefig('Graphs/9cat_dist.png', bbox_inches='tight')
```

    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/2377420911.py:23: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df[cat], hist = True, kde = True,



    
![png](output_2_1.png)
    



```python
# blks = stats.gamma.rvs(2, loc=1.5, scale=2, size=180)

for cat, _ in tqdm(cats_dict.items()):
    
    cat_values = df[cat].values

    f = Fitter(cat_values, distributions=get_common_distributions(), timeout=60)

    f.fit()
    f.summary()
    
    dist_dict = f.get_best(method = 'sumsquare_error')
    print(f'{cat} - {dist_dict} with error {f.df_errors["sumsquare_error"].sort_values().iloc[0]}')
```

     11%|█████                                        | 1/9 [00:57<07:39, 57.45s/it]

    p/g - {'gamma': {'a': 4.474866700072994, 'loc': 2.576286526573442, 'scale': 2.722283794409324}} with error 0.006741839285026984


    SKIPPED exponpow distribution (taking more than 60 seconds)
     22%|██████████                                   | 2/9 [02:07<07:32, 64.58s/it]

    3/g - {'chi2': {'df': 1.7333863656626733, 'loc': -4.460847278236644e-28, 'scale': 1.4498543229415843}} with error 4.797296235809877


    SKIPPED exponpow distribution (taking more than 60 seconds)
     33%|███████████████                              | 3/9 [03:14<06:36, 66.07s/it]

    r/g - {'lognorm': {'s': 0.3862009862188922, 'loc': -0.5986090915769504, 'scale': 5.779270754014119}} with error 0.03437984237367929


    SKIPPED exponpow distribution (taking more than 60 seconds)
     44%|████████████████████                         | 4/9 [04:24<05:37, 67.48s/it]

    a/g - {'lognorm': {'s': 0.6302460839803639, 'loc': -0.1835003121322039, 'scale': 2.795648938226954}} with error 0.112381684677191


     56%|█████████████████████████                    | 5/9 [05:20<04:13, 63.48s/it]

    s/g - {'lognorm': {'s': 0.24520049248739775, 'loc': -0.565078308412178, 'scale': 1.4988970021409869}} with error 1.5701426198072586


    SKIPPED exponpow distribution (taking more than 60 seconds)
     67%|██████████████████████████████               | 6/9 [06:29<03:15, 65.23s/it]

    b/g - {'lognorm': {'s': 0.6896790847211158, 'loc': -0.06410516298402036, 'scale': 0.5605022257237455}} with error 0.48202615501204704


     78%|███████████████████████████████████          | 7/9 [07:26<02:05, 62.62s/it]

    fg% - {'lognorm': {'s': 0.20633677191396105, 'loc': 0.1761214117074489, 'scale': 0.2953893125460005}} with error 65.3815213311839


    SKIPPED lognorm distribution (taking more than 60 seconds)
     89%|████████████████████████████████████████     | 8/9 [08:35<01:04, 64.53s/it]

    ft% - {'exponpow': {'b': 9.461593215915713, 'loc': -0.30949915878758133, 'scale': 1.1792090403192481}} with error 18.677365479121615


    100%|█████████████████████████████████████████████| 9/9 [09:37<00:00, 63.83s/it]

    to/g - {'lognorm': {'s': 0.36358490809266253, 'loc': -0.4784958930123663, 'scale': 2.093607216487146}} with error 0.2735678263373912


    100%|█████████████████████████████████████████████| 9/9 [09:37<00:00, 64.22s/it]



    
![png](output_3_19.png)
    



```python
clf = BayesianGaussianMixture(n_components=2, init_params='k-means++', max_iter=1000, covariance_type='full', tol=1e-6)

covariances = clf.fit(df['3/g'].values.reshape(-1, 1)).covariances_

mse = mean_squared_error(covariances[0], covariances[1])
print(mse)

draw_pdf(clf, df['3/g'].values)
```

    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,


    ERROR! Session/line number was not unique in database. History logging moved to new session 201
    0.4661083724654315


    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /var/folders/wg/8c_xwvyn2tl18ply9w25rmgm0000gn/T/ipykernel_39201/705228794.py:15: UserWarning: 
    
    `distplot` is a deprecated function and will be removed in seaborn v0.14.0.
    
    Please adapt your code to use either `displot` (a figure-level function with
    similar flexibility) or `histplot` (an axes-level function for histograms).
    
    For a guide to updating your code to use the new functions, please see
    https://gist.github.com/mwaskom/de44147ed2974457ad6372750bbe5751
    
      sns.distplot(df['3/g'].values, label='Real')
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,



    
![png](output_4_3.png)
    



```python
def z(data):
    mean = np.mean(data)
    std = np.std(data)
    
    return (data - mean) / std

def mix_norm_cdf(x, weights, means, covars):
    mcdf = 0.0
    for i in range(len(weights)):
        mcdf += weights[i] * stats.norm.cdf(x, loc=means[i], scale=covars[i])
    return mcdf

def calc_h_score_3(data, clf):
    probs = mix_norm_cdf(data, means=clf.means_.flatten(), covars=clf.covariances_.flatten(), weights=clf.weights_)
    h_scores = stats.norm.ppf(probs)
    return h_scores
```


```python
threes = df['3/g'].values

plt.scatter(threes, h_score_3(threes, clf))

print(f'Means: {clf.means_.flatten()}, Covars: {clf.covariances_.flatten()} Weights: {clf.weights_}')
```

    Means: [1.66894348 0.07495573], Covars: [0.7040092 0.0212879] Weights: [0.82865659 0.17134341]



    
![png](output_6_1.png)
    



```python
df.iloc[0]
```




    Own         Other Active
    Round                  1
    Rank                   1
    Y!Adp                7.0
    Y!%                  100
    Adv%                 NaN
    Adv+-                NaN
    Act%                 NaN
    Value           0.888303
    PosV            0.871766
    Name        Nikola Jokic
    Team                 DEN
    Pos                 PF/C
    Inj                  NaN
    Status               NaN
    g                     72
    Playoff                8
    Analysts             NaN
    m/g            34.556481
    p/g            26.361111
    3/g             1.277778
    r/g            10.833333
    a/g             8.319444
    s/g             1.319444
    b/g             0.680556
    fg%             0.566125
    fga/g          17.958333
    ft%              0.86802
    fta/g           5.472222
    to/g            3.083333
    USG            28.875655
    pV              1.641661
    3V             -0.449077
    rV              1.981495
    aV              2.035465
    sV              0.913449
    bV             -0.002951
    fg%V            2.237842
    ft%V            1.036192
    toV            -1.399351
    Name: 0, dtype: object




```python
print(clf.means_.flatten())
print(clf.covariances_.flatten())
print(clf.weights_)
    
probs = mix_norm_cdf(df['3/g'].values, means=clf.means_.flatten(), covars=clf.covariances_.flatten(), weights=clf.weights_)

h_scores = stats.norm.ppf(probs)

plt.scatter(df['3/g'].values, h_scores)
plt.xlabel('3/g')
plt.ylabel('H-score')
plt.show()

plt.scatter(df['3/g'].values, z(df['3/g'].values))
plt.xlabel('3/g')
plt.ylabel('Z-score')
plt.show()

print(len(df['3/g']))

plt.scatter(z(df['3/g'].values), h_scores)
plt.ylabel('H-score')
plt.xlabel('Z-score')
plt.show()
```

    [1.66894348 0.07495573]
    [0.7040092 0.0212879]
    [0.82865659 0.17134341]



    
![png](output_8_1.png)
    



    
![png](output_8_2.png)
    


    1620



    
![png](output_8_4.png)
    



```python
len(df['3/g'])
```




    1620




```python

```


```python
# School of thoughts
# Get player1 with ppg_x, apg_x, etc
# Calculate probabilty p(ppg_x) = distribution_p(ppg_x)
# Take that prob and return it to classical z_score with st.norm.pff(.95) = z_score = st.norm.ppf(p(ppg_x))
```


```python
def draw_pdf(clf, real_data):

    mus = clf.means_
    sigmas = clf.covariances_
    weights = clf.weights_

    fig = plt.figure()
    ax = fig.add_subplot(111)
    x = np.linspace(-1, 6, 1000).reshape(1000,1)
    logprob = clf.score_samples(x)
    pdf = np.exp(logprob)

    ax.plot(x, pdf, '-k', label='Fitted')

    sns.distplot(df['3/g'].values, label='Real')
    plt.legend()
    plt.show()
```


```python
df['fg%']
```




    0      0.566125
    1      0.482051
    2      0.505525
    3      0.512115
    4      0.496608
             ...   
    175    0.363190
    176    0.445629
    177    0.484761
    178    0.431562
    179    0.459459
    Name: fg%, Length: 1620, dtype: float64




```python
for cat in tqdm(['fg%', 'ft%']):
    
    cat_values = df[cat].values

    f = Fitter(cat_values, distributions=get_distributions().extend(['beta']), timeout=60)

    f.fit()
    f.summary()
    
    dist_dict = f.get_best(method = 'sumsquare_error')
    print(f'{cat} - {dist_dict} with error {f.df_errors["sumsquare_error"].sort_values().iloc[0]}')
```

      0%|                                                     | 0/2 [00:00<?, ?it/s]SKIPPED _fit distribution (taking more than 60 seconds)
    SKIPPED burr distribution (taking more than 60 seconds)
    SKIPPED exponweib distribution (taking more than 60 seconds)
    SKIPPED fisk distribution (taking more than 60 seconds)
    SKIPPED gausshyper distribution (taking more than 60 seconds)
    SKIPPED genexpon distribution (taking more than 60 seconds)
    SKIPPED genextreme distribution (taking more than 60 seconds)
    SKIPPED gengamma distribution (taking more than 60 seconds)
    SKIPPED invweibull distribution (taking more than 60 seconds)
    SKIPPED rv_continuous distribution (taking more than 60 seconds)
    SKIPPED rv_histogram distribution (taking more than 60 seconds)
    SKIPPED johnsonsb distribution (taking more than 60 seconds)
    SKIPPED kappa4 distribution (taking more than 60 seconds)
    SKIPPED ksone distribution (taking more than 60 seconds)
    SKIPPED levy_stable distribution (taking more than 60 seconds)
    SKIPPED mielke distribution (taking more than 60 seconds)
    SKIPPED nct distribution (taking more than 60 seconds)
    SKIPPED ncx2 distribution (taking more than 60 seconds)
    SKIPPED powerlognorm distribution (taking more than 60 seconds)
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    SKIPPED studentized_range distribution (taking more than 60 seconds)
    SKIPPED t distribution (taking more than 60 seconds)
    SKIPPED truncweibull_min distribution (taking more than 60 seconds)
    SKIPPED tukeylambda distribution (taking more than 60 seconds)
    SKIPPED vonmises distribution (taking more than 60 seconds)
    SKIPPED weibull_min distribution (taking more than 60 seconds)
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The integral is probably divergent, or slowly convergent.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,


    fg% - {'norminvgauss': {'a': 2.0137487883063487, 'b': 1.468577983688307, 'loc': 0.41926376729638615, 'scale': 0.05515614001375889}} with error 13.3733757349594

     50%|██████████████████████                      | 1/2 [03:56<03:56, 236.88s/it]

    


    SKIPPED _fit distribution (taking more than 60 seconds)
    SKIPPED betaprime distribution (taking more than 60 seconds)
    SKIPPED burr distribution (taking more than 60 seconds)
    SKIPPED burr12 distribution (taking more than 60 seconds)
    SKIPPED crystalball distribution (taking more than 60 seconds)
    SKIPPED f distribution (taking more than 60 seconds)
    SKIPPED exponweib distribution (taking more than 60 seconds)
    SKIPPED fatiguelife distribution (taking more than 60 seconds)
    SKIPPED fisk distribution (taking more than 60 seconds)
    SKIPPED foldnorm distribution (taking more than 60 seconds)
    SKIPPED gausshyper distribution (taking more than 60 seconds)
    SKIPPED genexpon distribution (taking more than 60 seconds)
    SKIPPED genextreme distribution (taking more than 60 seconds)
    SKIPPED gengamma distribution (taking more than 60 seconds)
    SKIPPED genpareto distribution (taking more than 60 seconds)
    SKIPPED invgauss distribution (taking more than 60 seconds)
    SKIPPED invweibull distribution (taking more than 60 seconds)
    SKIPPED johnsonsb distribution (taking more than 60 seconds)
    SKIPPED kappa3 distribution (taking more than 60 seconds)
    SKIPPED kappa4 distribution (taking more than 60 seconds)
    SKIPPED ksone distribution (taking more than 60 seconds)
    SKIPPED levy_stable distribution (taking more than 60 seconds)
    SKIPPED loglaplace distribution (taking more than 60 seconds)
    SKIPPED rv_continuous distribution (taking more than 60 seconds)
    SKIPPED rv_histogram distribution (taking more than 60 seconds)
    SKIPPED lognorm distribution (taking more than 60 seconds)
    SKIPPED mielke distribution (taking more than 60 seconds)
    SKIPPED ncf distribution (taking more than 60 seconds)
    SKIPPED nct distribution (taking more than 60 seconds)
    SKIPPED ncx2 distribution (taking more than 60 seconds)
    SKIPPED norminvgauss distribution (taking more than 60 seconds)
    SKIPPED pearson3 distribution (taking more than 60 seconds)
    SKIPPED powerlognorm distribution (taking more than 60 seconds)
    SKIPPED powernorm distribution (taking more than 60 seconds)
    SKIPPED rdist distribution (taking more than 60 seconds)
    SKIPPED recipinvgauss distribution (taking more than 60 seconds)
    SKIPPED rice distribution (taking more than 60 seconds)
    SKIPPED skewnorm distribution (taking more than 60 seconds)
    SKIPPED studentized_range distribution (taking more than 60 seconds)
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    SKIPPED t distribution (taking more than 60 seconds)
    SKIPPED truncnorm distribution (taking more than 60 seconds)
    SKIPPED truncpareto distribution (taking more than 60 seconds)
    SKIPPED truncweibull_min distribution (taking more than 60 seconds)
    SKIPPED vonmises distribution (taking more than 60 seconds)
    SKIPPED weibull_min distribution (taking more than 60 seconds)
    100%|████████████████████████████████████████████| 2/2 [08:51<00:00, 270.80s/it]

    ft% - {'johnsonsu': {'a': 1.5392683748297058, 'b': 1.643414386447413, 'loc': 0.8939740268254857, 'scale': 0.0903260844441375}} with error 9.569554054279369


    100%|████████████████████████████████████████████| 2/2 [08:51<00:00, 265.98s/it]
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The integral is probably divergent, or slowly convergent.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,



    
![png](output_14_7.png)
    


    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The occurrence of roundoff error is detected, which prevents 
      the requested tolerance from being achieved.  The error may be 
      underestimated.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,
    /Users/franhuzjan/Documents/NBA/random/venv/lib/python3.8/site-packages/scipy/integrate/_quadpack_py.py:1225: IntegrationWarning: The maximum number of subdivisions (50) has been achieved.
      If increasing the limit yields no improvement it is advised to analyze 
      the integrand in order to determine the difficulties.  If the position of a 
      local difficulty can be determined (singularity, discontinuity) one will 
      probably gain from splitting up the interval and calling the integrator 
      on the subranges.  Perhaps a special-purpose integrator should be used.
      quad_r = quad(f, low, high, args=args, full_output=self.full_output,



```python
from scipy.stats import gamma

a = 4.474866700072994
loc= 2.576286526573442
scale= 2.722283794409324

x = np.linspace(0, 40, 1000)

rv = gamma(a, loc = loc, scale = scale)

plt.plot(x, rv.pdf(x), label='Fitted')
sns.distplot(df['p/g'].values, label='Real')

plt.legend()
plt.show()

# ppg_prob = rv.pdf(df['p/g'])
```


    
![png](output_15_0.png)
    



```python
probs = rv.cdf(df['p/g'].values)

z_scores = stats.norm.ppf(probs)
print(z_scores)

plt.scatter(df['p/g'].values, z_scores)
plt.xlabel('PPG')
plt.ylabel('H-score')
plt.show()

mean = np.mean(df['p/g'].values)
std_dev = np.std(df['p/g'].values)

z = lambda x: (x - mean) / std_dev

plt.scatter(z(df['p/g'].values), z_scores)
plt.xlabel('Z-score')
plt.ylabel('H-score')
plt.show()
```

    [ 1.74118433  2.32870348  1.79776371 ... -0.62499183 -0.14373626
     -0.99392145]



    
![png](output_16_1.png)
    



    
![png](output_16_2.png)
    



```python
cat = 'r/g' 

a = 0.3862009862188922
loc = -0.5986090915769504
scale = 5.779270754014119

from scipy.stats import lognorm


x = np.linspace(0, 20, 1000)

rv = lognorm(a, loc = loc, scale = scale)

plt.plot(x, rv.pdf(x), label='Fitted')
sns.distplot(df[cat].values, label='Real')

plt.legend()
plt.show()


probs = rv.cdf(df[cat].values)

z_scores = stats.norm.ppf(probs)
print(z_scores)

plt.scatter(df[cat].values, z_scores)
plt.xlabel('RPG')
plt.ylabel('H-score')
plt.show()

mean = np.mean(df[cat].values)
std_dev = np.std(df[cat].values)

z = lambda x: (x - mean) / std_dev

plt.scatter(z(df[cat].values), z_scores)
plt.xlabel('Z-score')
plt.ylabel('H-score')
plt.show()

plt.scatter(df[cat].values, z(df[cat].values))
plt.xlabel('RPG')
plt.ylabel('Z-score')
plt.show()
```


    
![png](output_17_0.png)
    


    [ 1.76626658  0.13589041 -0.19604121 ...  0.02404003 -0.52103268
     -0.47379158]



    
![png](output_17_2.png)
    



    
![png](output_17_3.png)
    



    
![png](output_17_4.png)
    



```python
cat = 's/g'
a = 0.24520049248739775
loc= -0.565078308412178
scale = 1.4988970021409869


plt.figure(figsize=(20, 20))

ax = plt.subplot(2, 2, 1)

x = np.linspace(0, 5, 1000)

rv = lognorm(a, loc = loc, scale = scale)
plt.plot(x, rv.pdf(x), label='Fitted')
sns.distplot(df[cat].values, label='Real')

plt.legend()
# plt.show()


probs = rv.cdf(df[cat].values)

h_scores = stats.norm.ppf(probs)
print(h_scores)

ax = plt.subplot(2, 2, 2)

plt.scatter(df[cat].values, h_scores)
plt.xlabel(cat)
plt.ylabel('H-score')
# plt.show()

mean = np.mean(df[cat].values)
std_dev = np.std(df[cat].values)

# z = lambda x: (x - mean) / std_dev

ax = plt.subplot(2, 2, 3)


plt.scatter(z(df[cat].values), h_scores)
plt.xlabel('Z-score')
plt.ylabel('H-score')
# plt.show()

ax = plt.subplot(2, 2, 4)

plt.scatter(df[cat].values, z(df[cat].values))
plt.xlabel(cat)
plt.ylabel('Z-score')
plt.show()
```

    [ 0.93370572  0.68130459  1.11975724 ... -0.7598869   0.22331353
     -0.55578846]



    
![png](output_18_1.png)
    



```python
# to/g - {'lognorm': {'s': 0.36358490809266253, 'loc': -0.4784958930123663, 'scale': 2.093607216487146}} - 0.2735678263373912
import json

dist_dict = {'gamma':gamma,
            'lognorm':lognorm,
             'exponpow': exponpow,
             'cauchy': cauchy,
             'genhyperbolic': genhyperbolic
            }

cat_dist_dict = {}

player = {}

with open('9cat_parameters.txt', 'r') as file:
    lines = file.readlines()
    for line in lines:
        if line.startswith('#'):
            continue
        else:
            cat, str_dict, error = line.replace(' ', '').split('_')
            str_dict = str_dict.replace("'", '"')
            par_dict = json.loads(str_dict)
            dist = list(par_dict.keys())[0]
            
            # print(list(par_dict[dist].values()))
            # print('cauchy' in line)
            # if 'cauchy' in line:
            #     loc, scale = list(par_dict[dist].values())
            # if 'genhyperbolic' in line:
            #     p, a, b, loc, scale = list(par_dict[dist].values())
            # else:
            #     a, loc, scale = list(par_dict[dist].values())
        # if cat == 'ft%':
        #     cat_dist_dict[cat] = dist_dict[dist](loc=loc, scale=scale)
        # if cat == 'fg%':
        #     cat_dist_dict[cat] = dist_dict[dist](p, a, b, loc=loc, scale=scale)
        # else:
        #     cat_dist_dict[cat] = dist_dict[dist](a, loc=loc, scale=scale)
            cat_dist_dict[cat] = dist_dict[dist](*list(par_dict[dist].values()))
        
        if '%' in cat:
            cat_a = cat.replace('%', 'a/g')
            made = df[cat] * df[cat_a]

            perc_avg = made.sum() / df[cat_a].sum()

            impact = (df[cat] - perc_avg) * df[cat_a]

            probs = cat_dist_dict[cat].cdf(impact)
            h_scores = stats.norm.ppf(probs)
            plt.figure(figsize=(20, 20))

            ax = plt.subplot(2, 2, 1)
            x = np.linspace(np.min(impact), np.max(impact), 100)

            plt.plot(x, cat_dist_dict[cat].pdf(x), label='Fitted')
            sns.distplot(impact, label='Real')
            plt.legend()

            ax = plt.subplot(2, 2, 2)

            plt.scatter(impact, h_scores, label='H-score')
            plt.scatter(impact, z(impact), label='Z-score')
            plt.xlabel(f'{cat} impact')
            plt.ylabel('Score')
            plt.legend()


        else:
            probs = cat_dist_dict[cat].cdf(df[cat].values)
            h_scores = stats.norm.ppf(probs)
            
            plt.figure(figsize=(20, 20))

            ax = plt.subplot(2, 2, 1)
            x = np.linspace(np.min(df[cat].values), np.max(df[cat].values), 1000)

            plt.plot(x, cat_dist_dict[cat].pdf(x), label='Fitted')
            sns.distplot(df[cat].values, label='Real')
            plt.legend()

            fig.suptitle(f'{cat} information', fontsize=16)


            ax = plt.subplot(2, 2, 2)

            plt.scatter(df[cat].values, h_scores, label='H-score')
            plt.scatter(df[cat].values, z(df[cat].values), label='Z-score')
            plt.xlabel(cat)
            plt.ylabel('Score')
            plt.legend()

        plt.show()

```

    0.024849596691108797 0.13957296822068735



    
![png](output_19_1.png)
    


    -0.11998665219113525 0.34923589005462696 -0.05315085676652663 0.041124344641495794 0.1514962478968511



    
![png](output_19_3.png)
    


    4.474866700072994 2.576286526573442 2.722283794409324



    
![png](output_19_5.png)
    


    0.3862009862188922 -0.5986090915769504 5.779270754014119



    
![png](output_19_7.png)
    


    0.6302460839803639 -0.1835003121322039 2.795648938226954



    
![png](output_19_9.png)
    


    0.24520049248739775 -0.565078308412178 1.4988970021409869



    
![png](output_19_11.png)
    


    0.6896790847211158 -0.06410516298402036 0.5605022257237455



    
![png](output_19_13.png)
    


    0.36358490809266253 -0.4784958930123663 2.093607216487146



    
![png](output_19_15.png)
    



```python
mus = clf.means_
sigmas = clf.covariances_
weights = clf.weights_

plt.figure(figsize=(20, 20))
fig.suptitle(f'{cat} information', fontsize=16)

x = np.linspace(np.min(threes) - 2, np.max(threes), 1000).reshape(1000,1)
logprob = clf.score_samples(x)
pdf = np.exp(logprob)

ax = plt.subplot(2, 2, 1)


ax.plot(x, pdf, '-k', label='Fitted')
sns.distplot(df['3/g'].values, label='Real')
plt.legend()
# plt.show()

probs = mix_norm_cdf(df['3/g'].values, means=clf.means_.flatten(), covars=clf.covariances_.flatten(), weights=clf.weights_)

h_scores = stats.norm.ppf(probs)

ax = plt.subplot(2, 2, 2)

plt.scatter(threes, h_scores, label='H-score')
plt.scatter(threes, z(threes), label='Z-score')
plt.xlabel('3/g')
plt.ylabel('Score')
plt.legend()
plt.show()

# ax = plt.subplot(2, 2, 3)

# plt.scatter(z(threes), h_scores)
# plt.ylabel('H-score')
# plt.xlabel('Z-score')

# ax = plt.subplot(2, 2, 4)


# plt.scatter(threes, z(df['3/g'].values))
# plt.xlabel('3/g')
# plt.ylabel('Z-score')
# plt.show()
```


    
![png](output_20_0.png)
    



```python
plt.figure(figsize=(14, 14))

cat = 'a/g'

probs = cat_dist_dict[cat].cdf(df[cat].values)
h_scores = stats.norm.ppf(probs)


plt.scatter(df[cat].values, h_scores, color='b', label='H-score')
plt.scatter(df[cat].values, z(df[cat].values), color='r', label='Z-score')
plt.scatter(df[cat].values, h_scores - z(df[cat].values), color='g', label='Diff')

plt.xlabel(cat)
plt.ylabel('Score')

plt.legend()
plt.show()
```


    
![png](output_21_0.png)
    



```python
def calc_h_score(data, cat):        
    probs = cat_dist_dict[cat].cdf(data[cat].values)
    h_scores = stats.norm.ppf(probs)
    if cat == 'to/g':
        return -h_scores
    return h_scores
```


```python
for cat in ['ft%', 'fg%']:
    cat_a = cat.replace('%', 'a/g')
    plt.scatter(df[cat_a], df[cat])
    plt.show()

    data = pd.concat([df[cat_a], df[cat]], axis=1)

    # print(data)

    dataplot = sns.heatmap(data.corr(), annot=True)
    plt.show()
    
    data = df[cat_a] * df[cat]
    
    sns.distplot(data)
    plt.show()
```


    
![png](output_23_0.png)
    



    
![png](output_23_1.png)
    



    
![png](output_23_2.png)
    



    
![png](output_23_3.png)
    



    
![png](output_23_4.png)
    



    
![png](output_23_5.png)
    



```python
f = Fitter(cat_values, distributions=get_common_distributions(), timeout=60)

f.fit()
f.summary()

dist_dict = f.get_best(method = 'sumsquare_error')
print(f'{cat} - {dist_dict} with error {f.df_errors["sumsquare_error"].sort_values().iloc[0]}')
```


```python
def calc_perc_h_score(data, cat):
    cat_a = cat.replace('%', 'a/g')
    shots_made = df[cat].values * df[cat_a].values
    perc_avg = shots_made.sum() / df[cat_a].sum()
    impact = (data[cat] - perc_avg) * h_score_df[cat_a]
    
    probs = cat_dist_dict[cat].cdf(impact)
    
    h_scores = stats.norm.ppf(probs)

    return h_scores


df_2022 = pd.read_excel('/Users/franhuzjan/Downloads/Rankings/BBM_PlayerRankings.xls')

df_2022.head()

h_score_df = df_2022.copy()

# print(calc_h_score(df['p/g'], 'p/g'))
cats = ['p/g', '3/g', 'r/g', 'a/g', 's/g', 'b/g', 'to/g', 'ft%', 'fg%']

for cat in cats:
    if '%' in cat:
        h_score_df[f'{cat}_hV'] = calc_perc_h_score(h_score_df, cat)
    elif '3' in cat:
        h_score_df['3/g_hV'] = calc_h_score_3(h_score_df['3/g'], clf)
    else:
        h_score_df[f'{cat}_hV'] = calc_h_score(h_score_df, cat)

# h_score_df['3/g_hV'] = calc_h_score_3(h_score_df['3/g'], clf)

h_scores_columns = []

for column_name in cats:
    column_name = f'{column_name}_hV'
    h_scores_columns.append(column_name)
            
h_score_df['CARUSO'] = h_score_df[h_scores_columns].mean(axis=1)


# print(h_score_df[h_score_df['Name'] == 'Nicolas Claxton'][h_scores_columns])

columns_to_keep = ['Rank', 
                   'Value',
                   'CARUSO',
                   'Name']

columns_to_keep.extend(h_scores_columns)
columns_to_keep.extend(cats)
columns_to_keep.extend(['fta/g', 'fga/g'])

# columns_to_keep.extend(['ft%_hV', 'ft%', 'fta/g', 'fg%_hV', 'fg%', 'fga/g'])

h_score_df = h_score_df[columns_to_keep]

h_score_df = h_score_df.sort_values(by=['CARUSO'], ascending=False)


# for name in ['Walker Kessler', 'Nicolas Claxton', 'Jaren Jackson Jr.']:
#     print(h_score_df.index[h_score_df['Name'] == name].tolist()[0], name, h_score_df[h_score_df['Name'] == name][h_scores_columns])

# print((np.array(list(h_score_df.index)) + 1))
h_score_df = h_score_df.reset_index(drop=True)

h_score_df['Rank Change'] = h_score_df['Rank'] - (h_score_df.index + 1)

columns = list(h_score_df.columns)

rk_chg = columns.pop()
columns.insert(1, rk_chg)

h_score_df = h_score_df[columns]

# h_score_df = h_score_df.sort_values(by=['ft%_hV'], ascending=False)

h_score_df.head(50)

# h_score_df.to_excel("caruso.xlsx")
# h_score_df.to_csv('bratula_csv.csv')

h_score_df[h_score_df['Name'] == 'Nicolas Claxton']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Rank</th>
      <th>Rank Change</th>
      <th>Value</th>
      <th>CARUSO</th>
      <th>Name</th>
      <th>p/g_hV</th>
      <th>3/g_hV</th>
      <th>r/g_hV</th>
      <th>a/g_hV</th>
      <th>s/g_hV</th>
      <th>...</th>
      <th>3/g</th>
      <th>r/g</th>
      <th>a/g</th>
      <th>s/g</th>
      <th>b/g</th>
      <th>to/g</th>
      <th>ft%</th>
      <th>fg%</th>
      <th>fta/g</th>
      <th>fga/g</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>51</th>
      <td>24</td>
      <td>-28</td>
      <td>0.251256</td>
      <td>0.268269</td>
      <td>Nicolas Claxton</td>
      <td>-0.229993</td>
      <td>-2.437508</td>
      <td>1.376785</td>
      <td>-0.470521</td>
      <td>-0.219544</td>
      <td>...</td>
      <td>0.0</td>
      <td>9.236842</td>
      <td>1.894737</td>
      <td>0.855263</td>
      <td>2.486842</td>
      <td>1.263158</td>
      <td>0.54065</td>
      <td>0.705281</td>
      <td>3.236842</td>
      <td>7.723684</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 25 columns</p>
</div>




```python
df_2023 = pd.read_excel('/Users/franhuzjan/Downloads/BBM_Projections_2023.xls')

df_2023.head()

h_score_df = df_2023.copy()

cats = ['p/g', '3/g', 'r/g', 'a/g', 's/g', 'b/g', 'to/g', 'ft%', 'fg%']

for cat in cats:
    if '%' in cat:
        h_score_df[f'{cat}_hV'] = calc_perc_h_score(h_score_df, cat)
    elif '3' in cat:
        h_score_df['3/g_hV'] = calc_h_score_3(h_score_df['3/g'], clf)
    else:
        h_score_df[f'{cat}_hV'] = calc_h_score(h_score_df, cat)

# h_score_df['3/g_hV'] = calc_h_score_3(h_score_df['3/g'], clf)

h_scores_columns = []

for column_name in cats:
    column_name = f'{column_name}_hV'
    h_scores_columns.append(column_name)
        
h_score_df['CARUSO'] = h_score_df[h_scores_columns].mean(axis=1)


# print(h_score_df[h_score_df['Name'] == 'Nicolas Claxton'][h_scores_columns])

columns_to_keep = ['Rank', 
                   'Value',
                   'CARUSO',
                   'Name']

columns_to_keep.extend(h_scores_columns)
columns_to_keep.extend(cats)
columns_to_keep.extend(['fta/g', 'fga/g'])

# columns_to_keep.extend(['ft%_hV', 'ft%', 'fta/g', 'fg%_hV', 'fg%', 'fga/g'])

h_score_df = h_score_df[columns_to_keep]

h_score_df = h_score_df.sort_values(by=['CARUSO'], ascending=False)


# for name in ['Walker Kessler', 'Nicolas Claxton', 'Jaren Jackson Jr.']:
#     print(h_score_df.index[h_score_df['Name'] == name].tolist()[0], name, h_score_df[h_score_df['Name'] == name][h_scores_columns])

# print((np.array(list(h_score_df.index)) + 1))
h_score_df = h_score_df.reset_index(drop=True)

h_score_df['Rank Change'] = h_score_df['Rank'] - (h_score_df.index + 1)

columns = list(h_score_df.columns)

rk_chg = columns.pop()
columns.insert(1, rk_chg)

h_score_df = h_score_df[columns]

h_score_df.head(50)

# print(h_score_df.index[h_score_df['Name'] == 'Mark Williams'].tolist())
```


```python
cat = 'fg%'

cat_a = cat.replace('%', 'a/g')

made = df[cat] * df[cat_a]
perc_avg = made.sum() / df[cat_a].sum()
impact = (df[cat] - perc_avg) * df[cat_a]

# for _ in tqdm([1]):
#     f = Fitter(fg_impact, distributions=get_distributions(), timeout=5)

#     f.fit()
#     f.summary()

#     dist_dict = f.get_best(method = 'sumsquare_error')
#     for i in range(5):
#         print(f'{cat} - {dist_dict} with error {f.df_errors["sumsquare_error"].sort_values().iloc[i]}')

# fg_impact 'lognorm': {'s': 0.18866707190848425, 'loc': -3.3452100643007268, 'scale': 3.513180752614178}}
# ft% _ {'cauchy': {'loc': 0.024849596691108797, 'scale': 0.13957296822068735}} _ 0.5244414716442263


import scipy
fg_impact_dist = scipy.stats.laplace(loc=0.01790596676844252, scale=0.22993564193541663)
probs = fg_impact_dist.cdf(fg_impact)
h_scores = stats.norm.ppf(probs)
plt.figure(figsize=(10, 10))

ax = plt.subplot(2, 2, 1)
x = np.linspace(np.min(fg_impact), np.max(fg_impact), 100)

plt.plot(x, fg_impact_dist.pdf(x), label='Fitted')
sns.distplot(fg_impact, label='Real')
plt.legend()

ax = plt.subplot(2, 2, 2)

plt.scatter(fg_impact, h_scores, label='H-score')
plt.scatter(fg_impact, z(fg_impact), label='Z-score')
plt.xlabel('FG% impact')
plt.ylabel('Score')
plt.legend()

plt.show()

```


    
![png](output_27_0.png)
    



```python
# fga/g, fta/g


made = df[cat] * df[cat_a]
perc_avg = made.sum() / df[cat_a].sum()
impact = (df[cat] - perc_avg) * df[cat_a]

probs = cat_dist_dict['ft%'].cdf(impact)
probs_a = cat_dist_dict['fta/g'].cdf(5)

tot_probs = probs * probs_a
h_scores_multi = stats.norm.ppf(probs * probs_a)

h_scores = stats.norm.ppf(probs)
h_scores_a = stats.norm.ppf(probs_a)
mean_h_scores = np.mean([h_scores, h_scores_a])

print(h_scores, h_scores_a, h_scores_multi, mean_h_scores)
# print(mean_h_scores)

probs = cat_dist_dict['ft%'].cdf(0.95)
probs_a = cat_dist_dict['fta/g'].cdf(10)

tot_probs = probs * probs_a
h_scores_multi = stats.norm.ppf(probs * probs_a)

h_scores = stats.norm.ppf(probs)
h_scores_a = stats.norm.ppf(probs_a)
mean_h_scores = np.mean([h_scores, h_scores_a])

print(h_scores, h_scores_a, h_scores_multi, mean_h_scores)

```

    0.07393207269683302 1.0354068500075206 -0.12586139440905536 0.5546694613521769
    2.6297731176823467 2.2824843218697817 2.1582423472606846 2.456128719776064



```python
cat = 'ft%'

made = df[cat] * df[cat_a]
perc_avg = made.sum() / df[cat_a].sum()
impact = (0.8 - perc_avg) * 20

probs = cat_dist_dict[cat].cdf(impact)

h_scores = stats.norm.ppf(probs)

print(h_scores)

impact = (0.85 - perc_avg) * 5
probs = cat_dist_dict[cat].cdf(impact)

h_scores = stats.norm.ppf(probs)

print(h_scores)
```

    0.8088452656751289
    1.0416973121362014


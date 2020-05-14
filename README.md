# Retirement-nest-egg-calculator-
Calculates and shows retirement nest egg 
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Thu Feb 20 11:30:25 2020

@author: bobbyverchota
"""
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib as mpl

#from Money import Money # for currency string formatting

# ++++++++++++++++++++++++++++++++++++++++++++ #
def roundPartial (value, resolution):
    return round (value / resolution) * resolution
# ++++++++++++++++++++++++++++++++++++++++++++ #
def estimate_longevity(w, M, g):
    anl_w_pct = (w/M) # withdrawal rate as a percentage
    EL = .81/g * np.log( anl_w_pct/(anl_w_pct - g) )
    EL = roundPartial(EL, .3)
    return EL

# ++++++++++++++++++++++++++++++++++++++++++++ #
def create_cash_spend_range(cash_spend_est, est_err, N_samples=1):
    min_cash_spend = cash_spend_est * (1-est_err)
    max_cash_spend = cash_spend_est * (1+est_err)
    cash_spends = np.linspace(min_cash_spend, max_cash_spend, 
                              num=N_samples, endpoint=True)
    return cash_spends
# ++++++++++++++++++++++++++++++++++++++++++++ #
    
# ++++++++++++++++++++++++++++++++++++++++++++ #
def create_money_str(money):
    money_str = ('%f' % money)
    return money_str.format('en_US')  
# ++++++++++++++++++++++++++++++++++++++++++++ #
def create_sensitivity_table(gs, cash_spend_list, nest_egg):
    idx = ['{:.2%}'.format(g) for g in gs]
    cols = [create_money_str(cash) for cash in cash_spend_list]
    
    table = pd.DataFrame(columns=cols, index=idx)
    for cash, col in zip(cash_spend_list, cols):
        for g, g_idx in zip(gs, idx):
            el = estimate_longevity(cash, nest_egg, g)
            table.loc[g_idx, col] = el
    return table      
# ++++++++++++++++++++++++++++++++++++++++++++ #
    
# ++++++++++++++++++++++++++++++++++++++++++++ #
def plot_sensitivity_table(nest_egg):
    fig = plt.figure()
    mpl.rcParams['font.family'] = 'CamingoCode'
    _ = tbl.plot(figsize=(7,7), lw=2, marker='o')
    plt.axhline(y=estimated_personal_longevity, color='k', ls='--', lw=2)
    plt.title('Mr. Johnsons Retirement \n Nest Egg = %s' 
              % create_money_str(nest_egg),
              fontsize=15, fontweight='0')
    plt.xlabel('Rate of Return')
    plt.ylabel('Estimated NestEgg Retirement $(Years)$')
    plt.legend(title='Estimated Annual\nRetirement Expenditures', 
               loc='upper left', 
               fontsize=11, 
               frameon=True).get_frame().set_edgecolor('blue')


est_err = .15575 # +/- 25% this is was down in the input section
cash_spend_est = 100e3 # $100,000 
# ++++++++++++++++++++++++++++++++++++++++++++ #
"""def plot_longevity_gap(tbl, estimated_personal_longevity):
    gap_in_years = tbl - estimated_personal_longevity
    fig = plt.figure()
    mpl.rcParams['font.family'] = 'CamingoCode'
    _ = gap_in_years.plot(figsize=(10,10), lw=2, marker='o')
    plt.axhline(y=0, color='k', ls='--', lw=2)
    plt.title('Estimated Portfolio Longevity Shortfall Gap\nPortfolio Nest Egg = %s' 
              % create_money_str(nest_egg),
              fontsize=17, fontweight='demi')
    plt.xlabel('Real Annual Growth Rate $(G)$')
    plt.ylabel('Estimated Portfolio Longevity Gap $(Years)$')
    plt.legend(title='Real After-Tax Anl.\nCash Withdrawal', 
               loc='upper left', 
               fontsize=11, 
               frameon=True).get_frame().set_edgecolor('blue')
    """
# ++++++++++++++++++++++++++++++++++++++++++++ #
"""def plot_longevity_gap_bar(tbl, estimated_personal_longevity):
    gap_in_years = tbl - estimated_personal_longevity
    fig = plt.figure()
    mpl.rcParams['font.family'] = 'CamingoCode'
    _ = gap_in_years.plot(kind='barh', figsize=(10,10))
    plt.axvline(x=0, color='k', lw=2)
    plt.title('Estimated Portfolio Longevity Shortfall\nPortfolio Nest Egg = %s' 
              % create_money_str(nest_egg),
              fontsize=17, fontweight='demi')
    plt.ylabel('Real Annual Growth Rate $(G)$')
    plt.xlabel('Estimated Portfolio Longevity Gap $(Years)$')
    plt.legend(title='Real After-Tax Anl.\nCash Withdrawal', 
               loc='best', 
               fontsize=11, 
               frameon=True).get_frame().set_edgecolor('blue')       
 """   
    
 # ++++++++++++++++++++++++++++++ #
# Inputs
 #secind 1089603.37
 #first 908001.51
 
annualexpenditure = 84425
estimated_personal_longevity = 13 # years
gs = np.arange(0.5,6.0,0.5)/100 # growth rates
cash_spends = create_cash_spend_range(cash_spend_est, est_err)
nest_egg = 908001.51 # $900thousand nest egg

# ++++++++++++++++++++++++++++++
# create sensitivity table
tbl = create_sensitivity_table(gs, cash_spends, nest_egg)

# ++++++++++++++++++++++++++++++
# plot table output
plot_sensitivity_table(nest_egg)

# ++++++++++++++++++++++++++++++
# plot longevity gaps
#plot_longevity_gap(tbl, estimated_personal_longevity)

#plot_longevity_gap_bar(tbl, estimated_personal_longevity)
# ++++++++++++++++++++++++++++++
# print table
print(tbl[tbl>=estimated_personal_longevity].fillna('Portfolio Exhausted'))












import matplotlib.pyplot as plt
from matplotlib.widgets import Slider, Button, RadioButtons


fig = plt.figure()
plt.subplots_adjust(left=0.25, right=.8, bottom=0.4)
ax = fig.add_subplot(1,1,1)

axcolor = 'White'
axyears   = plt.axes([0.25, 0.1, 0.5, 0.03], facecolor=axcolor)
axcontrib = plt.axes([0.25, 0.15, 0.5, 0.03], facecolor=axcolor)
axmarket  = plt.axes([0.25, 0.2, 0.5, 0.03], facecolor=axcolor)
axsalary  = plt.axes([0.25, 0.25, 0.5, 0.03], facecolor=axcolor)
#resetax = plt.axes([0.25, 0.3, 0.1, 0.03])

years = Slider(axyears, 'Years of Investment', 1, 50, valinit=40, valstep=1)
monthly_contribution = Slider(axcontrib, 'Contribution per Month', 0.0, 1.0, valinit=0.10, valstep=0.001)
market = Slider(axmarket, 'Rate of Return', 1.0, 1.5, valinit=1.05)
salary = Slider(axsalary, 'Annual Salary', 40000, 100000, valinit=60000, valstep=500)
button = Button(resetax, 'Reset', color=axcolor, hovercolor='0.975')

sliders = [years, monthly_contribution, market, salary]

#-13694.2 second
#-11411.9 first
def get_balance(
    years=40,
    salary=60000 / 12,
    monthly_contribuyion_pc = .10,
    market_growth_pc = 1.05,
    initial_balance = 0
    ):
    total = [initial_balance]
    yearly_salary_increase = 1.02
    for m in range(1, int(years) * 12 + 1):
        contribution = salary * monthly_contribuyion_pc
        market_growth = total[-1] * (market_growth_pc**(1/12)-1)
        total.append(total[-1] + contribution + market_growth)
        if m % 12 == 0:
            salary *= yearly_salary_increase

    return total

def update(val=None):
    balance = get_balance(
        years=years.val,
        salary=salary.val / 12,
        monthly_contribuyion_pc = monthly_contribution.val,
        market_growth_pc = market.val,
        initial_balance = -11411.9
    )
    ax.clear()
    ax.plot([x/12 for x in range(len(balance))], balance)
    ax.get_yaxis().set_major_formatter(plt.FuncFormatter(
        lambda x, loc: "${:,}".format(int(x))))
    ax.set_ylabel('Nest Egg balance')
    ax.set_xlabel('Years')
    fig.suptitle(f'Mr. Johnsons Nest Egg: ${balance[-1]:,.2f}')

def reset(event):
    for s in sliders:
        s.reset()

years.on_changed(update)
market.on_changed(update)
monthly_contribution.on_changed(update)
salary.on_changed(update)
button.on_clicked(reset)
update()
plt.show()

#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Sun May 26 18:36:43 2019

@author: trang
"""
import pandas as pd
import itertools

conversion = [0,2,0,2,2,0,1]
channels_subset = ['Fb', 'GoogleAd', 'Email', 'Email,Fb', 'Fb,GoogleAd', 
                   'Email,GoogleAd', 'Email,Fb,GoogleAd']

subset_conversion = pd.DataFrame(conversion, channels_subset)
subset_conversion.columns = ['Conversion']
subset_conversion.index.names = ['Subset_channels']
subset_conversion.reset_index(level = 0, inplace = True)

#------------------------------------------------------------------------------
#------------------------------subset function---------------------------------
#------------------------------------------------------------------------------
def subsets(s):
    '''
    This function returns all the possible subsets of a set of channels.
    input :
            - s: a set of channels.
    '''
    if len(s)==1:
        return s
    else:
        sub_channels=[]
        for i in range(1,len(s)+1):
            sub_channels.extend(map(list,itertools.combinations(s, i)))
    return map(",".join,map(sorted,sub_channels))
    

#------------------------------------------------------------------------------
#-------function to calculate attribution value for each colliation A----------
#------------------------------------------------------------------------------
def attribution(A,C_values):
    '''
    This function computes the worth of each coalition.
    inputs:
            - A : a coalition of channels.
            - C_values : A dictionnary containing the number of conversions that each subset of channels has yielded.
    '''
    subsets_of_A = subsets(A.split(","))
    worth_of_A=0
    for subset in subsets_of_A:
        if subset in C_values:
            worth_of_A += C_values[subset]
    return worth_of_A

#------------------------------------------------------------------------------
#------------------------------attribution moedelling--------------------------
#------------------------------------------------------------------------------
from math import factorial
from collections import defaultdict

C_values = subset_conversion.set_index("Subset_channels").to_dict()["Conversion"]
channels = ['Fb', 'GoogleAd', 'Email']

v_values = {}
for A in subsets(channels):
    v_values[A] = attribution(A,C_values)

n=len(channels)
shapley_values = defaultdict(int)

for channel in channels:
    for A in v_values.keys():
        if channel not in A.split(","):
            cardinal_A=len(A.split(","))
            A_with_channel = A.split(",")
            A_with_channel.append(channel)            
            A_with_channel=",".join(sorted(A_with_channel))
            shapley_values[channel] += (v_values[A_with_channel]-v_values[A])*(factorial(cardinal_A)*factorial(n-cardinal_A-1)/factorial(n))
    # Add the term corresponding to the empty set
    shapley_values[channel]+= v_values[channel]/n 

import matplotlib.pyplot as plt
pd.DataFrame.from_dict(shapley_values, orient = 'index').plot(kind = 'bar', color = 'mediumpurple')
plt.xticks(rotation = 45)
plt.legend('')
plt.ylabel('number of conversion')
plt.title('channels attribution')
plt.show()



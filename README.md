# Jupyter Notebook
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**This tutorial is meant to be a companion to my R equivalent.** See the same script in R [here](https://github.com/mjtat/Plotting-event-related-potentials-in-R/blob/master/Plotting%20Data%20for%20the%20Cognision%20EEG%20System%20in%20R.ipynb)."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**The following script will take data files output by the *COGNISION(tm)* EEG system developed by Neuronetrix.** The script find the grand average, and plot the resulting event-related potentials (ERPs). The Cognision system is a 7-electrode system used for both clinical and research purposes. In this example, we take the grand average of all the midline electrodes (FZ, CZ, PZ) from five individuals.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**First, import all the important libraries you need.** Specifically, pandas, numpy, glob2 (to get file names), os (to set working directory), and matplotlib."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "%matplotlib inline\n",
    "import pandas as pd\n",
    "import numpy as np\n",
    "import glob2\n",
    "import os\n",
    "import matplotlib.pyplot as plt"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Next, set the working directory** (just so it's easier to import files)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "os.chdir('/media/mihcelle/ChelleUSB/Cognision')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**We need to find all the files in the directory.** We do this using the `glob` library in python. It will search through all files in a directory matching some string input (in this case, .xlsx)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "['2490_1572_average.xlsx',\n",
       " '2504_2098_average.xlsx',\n",
       " '2514_1748_average.xlsx',\n",
       " '2536_1455_average.xlsx',\n",
       " '3097_1981_average.xlsx']"
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "files = glob2.glob('*.xlsx')\n",
    "files"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**R has a list object to store multiple items (like dataframes). Python, in turn, uses `dict`s to achieve something similar.** In the line below, I'm telling python to read in the excel files in the folder. The keys are the file names and the values are the dataframes themselves. The dict object is labeled `x`.\n",
    "\n",
    "Just to check, I print out the first ten lines of the first data frame in the x dict."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Time</th>\n",
       "      <th>FZ</th>\n",
       "      <th>CZ</th>\n",
       "      <th>PZ</th>\n",
       "      <th>F3</th>\n",
       "      <th>P3</th>\n",
       "      <th>F4</th>\n",
       "      <th>P4</th>\n",
       "      <th>Stimulus</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>-240</td>\n",
       "      <td>0.913900</td>\n",
       "      <td>0.251298</td>\n",
       "      <td>0.125067</td>\n",
       "      <td>0.603175</td>\n",
       "      <td>-0.285071</td>\n",
       "      <td>1.029497</td>\n",
       "      <td>1.770241</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>-232</td>\n",
       "      <td>1.150180</td>\n",
       "      <td>0.523645</td>\n",
       "      <td>0.429780</td>\n",
       "      <td>0.901415</td>\n",
       "      <td>0.111195</td>\n",
       "      <td>1.087295</td>\n",
       "      <td>1.880752</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>-224</td>\n",
       "      <td>1.969993</td>\n",
       "      <td>1.346232</td>\n",
       "      <td>1.329586</td>\n",
       "      <td>1.810007</td>\n",
       "      <td>1.216764</td>\n",
       "      <td>1.929303</td>\n",
       "      <td>2.901241</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>-216</td>\n",
       "      <td>2.290890</td>\n",
       "      <td>1.546908</td>\n",
       "      <td>1.630600</td>\n",
       "      <td>2.060621</td>\n",
       "      <td>1.399869</td>\n",
       "      <td>2.166045</td>\n",
       "      <td>3.213815</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>-208</td>\n",
       "      <td>1.505756</td>\n",
       "      <td>0.776108</td>\n",
       "      <td>0.904190</td>\n",
       "      <td>1.318026</td>\n",
       "      <td>0.368282</td>\n",
       "      <td>0.954127</td>\n",
       "      <td>2.228467</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>-200</td>\n",
       "      <td>0.902340</td>\n",
       "      <td>0.260546</td>\n",
       "      <td>0.458910</td>\n",
       "      <td>1.014700</td>\n",
       "      <td>0.036751</td>\n",
       "      <td>0.130615</td>\n",
       "      <td>1.590835</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>-192</td>\n",
       "      <td>1.204742</td>\n",
       "      <td>0.694728</td>\n",
       "      <td>1.002678</td>\n",
       "      <td>1.677764</td>\n",
       "      <td>0.818648</td>\n",
       "      <td>0.825584</td>\n",
       "      <td>2.166508</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>-184</td>\n",
       "      <td>1.381836</td>\n",
       "      <td>1.091919</td>\n",
       "      <td>1.400794</td>\n",
       "      <td>1.988488</td>\n",
       "      <td>1.361028</td>\n",
       "      <td>1.371201</td>\n",
       "      <td>2.532719</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>-176</td>\n",
       "      <td>0.717847</td>\n",
       "      <td>0.470008</td>\n",
       "      <td>0.866274</td>\n",
       "      <td>1.297681</td>\n",
       "      <td>0.790442</td>\n",
       "      <td>0.538441</td>\n",
       "      <td>1.737874</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>-168</td>\n",
       "      <td>0.095936</td>\n",
       "      <td>-0.156065</td>\n",
       "      <td>0.465846</td>\n",
       "      <td>0.876909</td>\n",
       "      <td>0.382616</td>\n",
       "      <td>-0.442745</td>\n",
       "      <td>1.268089</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Time        FZ        CZ        PZ        F3        P3        F4        P4  \\\n",
       "0  -240  0.913900  0.251298  0.125067  0.603175 -0.285071  1.029497  1.770241   \n",
       "1  -232  1.150180  0.523645  0.429780  0.901415  0.111195  1.087295  1.880752   \n",
       "2  -224  1.969993  1.346232  1.329586  1.810007  1.216764  1.929303  2.901241   \n",
       "3  -216  2.290890  1.546908  1.630600  2.060621  1.399869  2.166045  3.213815   \n",
       "4  -208  1.505756  0.776108  0.904190  1.318026  0.368282  0.954127  2.228467   \n",
       "5  -200  0.902340  0.260546  0.458910  1.014700  0.036751  0.130615  1.590835   \n",
       "6  -192  1.204742  0.694728  1.002678  1.677764  0.818648  0.825584  2.166508   \n",
       "7  -184  1.381836  1.091919  1.400794  1.988488  1.361028  1.371201  2.532719   \n",
       "8  -176  0.717847  0.470008  0.866274  1.297681  0.790442  0.538441  1.737874   \n",
       "9  -168  0.095936 -0.156065  0.465846  0.876909  0.382616 -0.442745  1.268089   \n",
       "\n",
       "   Stimulus  \n",
       "0         1  \n",
       "1         1  \n",
       "2         1  \n",
       "3         1  \n",
       "4         1  \n",
       "5         1  \n",
       "6         1  \n",
       "7         1  \n",
       "8         1  \n",
       "9         1  "
      ]
     },
     "execution_count": 4,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "x = {file: pd.read_excel(file, sheetname = 2) for file in files}\n",
    "x.values()[1].head(10)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**The names of each key is a bit much.** Let's do a for loop to change the names of each key into something manageable.\n",
    "\n",
    "Basically, for the length of `x`, assign the ith key name to the variable `name`, convert `i` to a string, print `i` to check it's working, then remove the old key name and replace it with the new key name."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "0\n",
      "1\n",
      "2\n",
      "3\n",
      "4\n"
     ]
    }
   ],
   "source": [
    "for i in range(len(x)):\n",
    "    name = x.keys()[i]\n",
    "    i = str(i)\n",
    "    print i\n",
    "    x[i] = x.pop(name)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Ultimately, we want to populate a new data frame with all the values in our x dict.** First we create an empty dictionary named `EEG_data`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "EEG_data = pd.DataFrame()     "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In the following for loop, for the ith item in the range of length(x), convert i into a string, then append x[i] to the empty `EEG_data` data frame. Loop until there are no more items to go through in x."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "for i in range(len(x)):\n",
    "    i = str(i)\n",
    "    EEG_data = EEG_data.append(x[i])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**From here, we need to gather the grand mean for all elements.** Or in other words, find the mean for each individual cell across ALL subjects.\n",
    "\n",
    "This is achieved by simply aggregating `EEG_data` by it's index, and finding the mean.\n",
    "\n",
    "We end up with the grand means when calling `means = row_index.mean()`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Time</th>\n",
       "      <th>FZ</th>\n",
       "      <th>CZ</th>\n",
       "      <th>PZ</th>\n",
       "      <th>F3</th>\n",
       "      <th>P3</th>\n",
       "      <th>F4</th>\n",
       "      <th>P4</th>\n",
       "      <th>Stimulus</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>-240</td>\n",
       "      <td>-0.039821</td>\n",
       "      <td>0.349694</td>\n",
       "      <td>0.668002</td>\n",
       "      <td>0.007620</td>\n",
       "      <td>0.441710</td>\n",
       "      <td>-0.047589</td>\n",
       "      <td>0.558231</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>-232</td>\n",
       "      <td>0.277747</td>\n",
       "      <td>0.723581</td>\n",
       "      <td>0.991673</td>\n",
       "      <td>0.498491</td>\n",
       "      <td>0.835016</td>\n",
       "      <td>0.218099</td>\n",
       "      <td>0.802094</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>-224</td>\n",
       "      <td>0.469915</td>\n",
       "      <td>0.984368</td>\n",
       "      <td>1.188373</td>\n",
       "      <td>0.690752</td>\n",
       "      <td>1.095063</td>\n",
       "      <td>0.325650</td>\n",
       "      <td>0.928789</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>-216</td>\n",
       "      <td>0.316125</td>\n",
       "      <td>0.895682</td>\n",
       "      <td>1.082024</td>\n",
       "      <td>0.389830</td>\n",
       "      <td>0.843432</td>\n",
       "      <td>0.118593</td>\n",
       "      <td>0.787853</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>-208</td>\n",
       "      <td>0.081047</td>\n",
       "      <td>0.730794</td>\n",
       "      <td>0.900028</td>\n",
       "      <td>0.180923</td>\n",
       "      <td>0.498306</td>\n",
       "      <td>-0.141546</td>\n",
       "      <td>0.632768</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>-200</td>\n",
       "      <td>0.061627</td>\n",
       "      <td>0.777218</td>\n",
       "      <td>0.964207</td>\n",
       "      <td>0.328610</td>\n",
       "      <td>0.639889</td>\n",
       "      <td>-0.214696</td>\n",
       "      <td>0.630456</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>-192</td>\n",
       "      <td>0.149111</td>\n",
       "      <td>0.913437</td>\n",
       "      <td>1.100982</td>\n",
       "      <td>0.558971</td>\n",
       "      <td>0.908166</td>\n",
       "      <td>-0.123975</td>\n",
       "      <td>0.630086</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>-184</td>\n",
       "      <td>0.145874</td>\n",
       "      <td>0.874134</td>\n",
       "      <td>1.043646</td>\n",
       "      <td>0.679654</td>\n",
       "      <td>0.844264</td>\n",
       "      <td>-0.043150</td>\n",
       "      <td>0.456044</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>-176</td>\n",
       "      <td>0.034624</td>\n",
       "      <td>0.699074</td>\n",
       "      <td>0.890780</td>\n",
       "      <td>0.581905</td>\n",
       "      <td>0.571825</td>\n",
       "      <td>-0.101226</td>\n",
       "      <td>0.276082</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>-168</td>\n",
       "      <td>-0.180387</td>\n",
       "      <td>0.557769</td>\n",
       "      <td>0.865904</td>\n",
       "      <td>0.340539</td>\n",
       "      <td>0.493220</td>\n",
       "      <td>-0.281557</td>\n",
       "      <td>0.320194</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Time        FZ        CZ        PZ        F3        P3        F4        P4  \\\n",
       "0  -240 -0.039821  0.349694  0.668002  0.007620  0.441710 -0.047589  0.558231   \n",
       "1  -232  0.277747  0.723581  0.991673  0.498491  0.835016  0.218099  0.802094   \n",
       "2  -224  0.469915  0.984368  1.188373  0.690752  1.095063  0.325650  0.928789   \n",
       "3  -216  0.316125  0.895682  1.082024  0.389830  0.843432  0.118593  0.787853   \n",
       "4  -208  0.081047  0.730794  0.900028  0.180923  0.498306 -0.141546  0.632768   \n",
       "5  -200  0.061627  0.777218  0.964207  0.328610  0.639889 -0.214696  0.630456   \n",
       "6  -192  0.149111  0.913437  1.100982  0.558971  0.908166 -0.123975  0.630086   \n",
       "7  -184  0.145874  0.874134  1.043646  0.679654  0.844264 -0.043150  0.456044   \n",
       "8  -176  0.034624  0.699074  0.890780  0.581905  0.571825 -0.101226  0.276082   \n",
       "9  -168 -0.180387  0.557769  0.865904  0.340539  0.493220 -0.281557  0.320194   \n",
       "\n",
       "   Stimulus  \n",
       "0         1  \n",
       "1         1  \n",
       "2         1  \n",
       "3         1  \n",
       "4         1  \n",
       "5         1  \n",
       "6         1  \n",
       "7         1  \n",
       "8         1  \n",
       "9         1  "
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "row_index = EEG_data.groupby(EEG_data.index)\n",
    "means = row_index.mean()\n",
    "means.head(10)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In the next three parts, we are simply subsetting the previous data frame into separate dataframes, corresponding to the Stimulus number (1, 2, or 3)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Time</th>\n",
       "      <th>FZ</th>\n",
       "      <th>CZ</th>\n",
       "      <th>PZ</th>\n",
       "      <th>F3</th>\n",
       "      <th>P3</th>\n",
       "      <th>F4</th>\n",
       "      <th>P4</th>\n",
       "      <th>Stimulus</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>-240</td>\n",
       "      <td>-0.039821</td>\n",
       "      <td>0.349694</td>\n",
       "      <td>0.668002</td>\n",
       "      <td>0.007620</td>\n",
       "      <td>0.441710</td>\n",
       "      <td>-0.047589</td>\n",
       "      <td>0.558231</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>-232</td>\n",
       "      <td>0.277747</td>\n",
       "      <td>0.723581</td>\n",
       "      <td>0.991673</td>\n",
       "      <td>0.498491</td>\n",
       "      <td>0.835016</td>\n",
       "      <td>0.218099</td>\n",
       "      <td>0.802094</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>-224</td>\n",
       "      <td>0.469915</td>\n",
       "      <td>0.984368</td>\n",
       "      <td>1.188373</td>\n",
       "      <td>0.690752</td>\n",
       "      <td>1.095063</td>\n",
       "      <td>0.325650</td>\n",
       "      <td>0.928789</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>-216</td>\n",
       "      <td>0.316125</td>\n",
       "      <td>0.895682</td>\n",
       "      <td>1.082024</td>\n",
       "      <td>0.389830</td>\n",
       "      <td>0.843432</td>\n",
       "      <td>0.118593</td>\n",
       "      <td>0.787853</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>-208</td>\n",
       "      <td>0.081047</td>\n",
       "      <td>0.730794</td>\n",
       "      <td>0.900028</td>\n",
       "      <td>0.180923</td>\n",
       "      <td>0.498306</td>\n",
       "      <td>-0.141546</td>\n",
       "      <td>0.632768</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Time        FZ        CZ        PZ        F3        P3        F4        P4  \\\n",
       "0  -240 -0.039821  0.349694  0.668002  0.007620  0.441710 -0.047589  0.558231   \n",
       "1  -232  0.277747  0.723581  0.991673  0.498491  0.835016  0.218099  0.802094   \n",
       "2  -224  0.469915  0.984368  1.188373  0.690752  1.095063  0.325650  0.928789   \n",
       "3  -216  0.316125  0.895682  1.082024  0.389830  0.843432  0.118593  0.787853   \n",
       "4  -208  0.081047  0.730794  0.900028  0.180923  0.498306 -0.141546  0.632768   \n",
       "\n",
       "   Stimulus  \n",
       "0         1  \n",
       "1         1  \n",
       "2         1  \n",
       "3         1  \n",
       "4         1  "
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "means_stim_1 = means[(means.Stimulus == 1)]\n",
    "means_stim_1.head(5)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Time</th>\n",
       "      <th>FZ</th>\n",
       "      <th>CZ</th>\n",
       "      <th>PZ</th>\n",
       "      <th>F3</th>\n",
       "      <th>P3</th>\n",
       "      <th>F4</th>\n",
       "      <th>P4</th>\n",
       "      <th>Stimulus</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>150</th>\n",
       "      <td>-240</td>\n",
       "      <td>-0.135720</td>\n",
       "      <td>-0.526437</td>\n",
       "      <td>0.112582</td>\n",
       "      <td>-0.702145</td>\n",
       "      <td>0.118593</td>\n",
       "      <td>-0.221724</td>\n",
       "      <td>0.562485</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>151</th>\n",
       "      <td>-232</td>\n",
       "      <td>0.110270</td>\n",
       "      <td>-0.256403</td>\n",
       "      <td>0.246212</td>\n",
       "      <td>-0.421475</td>\n",
       "      <td>0.218931</td>\n",
       "      <td>0.206909</td>\n",
       "      <td>0.585605</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>152</th>\n",
       "      <td>-224</td>\n",
       "      <td>-0.489909</td>\n",
       "      <td>-0.839936</td>\n",
       "      <td>-0.391883</td>\n",
       "      <td>-0.986513</td>\n",
       "      <td>-0.307266</td>\n",
       "      <td>-0.500081</td>\n",
       "      <td>-0.251317</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>153</th>\n",
       "      <td>-216</td>\n",
       "      <td>-0.753007</td>\n",
       "      <td>-1.042462</td>\n",
       "      <td>-0.653594</td>\n",
       "      <td>-1.142338</td>\n",
       "      <td>-0.544008</td>\n",
       "      <td>-1.054946</td>\n",
       "      <td>-0.497307</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>154</th>\n",
       "      <td>-208</td>\n",
       "      <td>-0.342407</td>\n",
       "      <td>-0.693822</td>\n",
       "      <td>-0.415002</td>\n",
       "      <td>-0.573601</td>\n",
       "      <td>-0.163926</td>\n",
       "      <td>-0.640647</td>\n",
       "      <td>-0.268425</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "     Time        FZ        CZ        PZ        F3        P3        F4  \\\n",
       "150  -240 -0.135720 -0.526437  0.112582 -0.702145  0.118593 -0.221724   \n",
       "151  -232  0.110270 -0.256403  0.246212 -0.421475  0.218931  0.206909   \n",
       "152  -224 -0.489909 -0.839936 -0.391883 -0.986513 -0.307266 -0.500081   \n",
       "153  -216 -0.753007 -1.042462 -0.653594 -1.142338 -0.544008 -1.054946   \n",
       "154  -208 -0.342407 -0.693822 -0.415002 -0.573601 -0.163926 -0.640647   \n",
       "\n",
       "           P4  Stimulus  \n",
       "150  0.562485         2  \n",
       "151  0.585605         2  \n",
       "152 -0.251317         2  \n",
       "153 -0.497307         2  \n",
       "154 -0.268425         2  "
      ]
     },
     "execution_count": 10,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "means_stim_2 = means[(means.Stimulus == 2)]\n",
    "means_stim_2.head(5)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Time</th>\n",
       "      <th>FZ</th>\n",
       "      <th>CZ</th>\n",
       "      <th>PZ</th>\n",
       "      <th>F3</th>\n",
       "      <th>P3</th>\n",
       "      <th>F4</th>\n",
       "      <th>P4</th>\n",
       "      <th>Stimulus</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>300</th>\n",
       "      <td>-240</td>\n",
       "      <td>-0.905595</td>\n",
       "      <td>-0.623308</td>\n",
       "      <td>-0.356972</td>\n",
       "      <td>-0.712086</td>\n",
       "      <td>-0.694053</td>\n",
       "      <td>-1.458380</td>\n",
       "      <td>-0.705150</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>301</th>\n",
       "      <td>-232</td>\n",
       "      <td>-0.927790</td>\n",
       "      <td>-0.282066</td>\n",
       "      <td>0.007158</td>\n",
       "      <td>-0.909757</td>\n",
       "      <td>0.080677</td>\n",
       "      <td>-1.409135</td>\n",
       "      <td>-0.542159</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>302</th>\n",
       "      <td>-224</td>\n",
       "      <td>-1.280823</td>\n",
       "      <td>-0.689198</td>\n",
       "      <td>-0.298018</td>\n",
       "      <td>-1.425088</td>\n",
       "      <td>-0.194674</td>\n",
       "      <td>-1.459767</td>\n",
       "      <td>-0.910450</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>303</th>\n",
       "      <td>-216</td>\n",
       "      <td>-1.543690</td>\n",
       "      <td>-0.970792</td>\n",
       "      <td>-0.547014</td>\n",
       "      <td>-1.467396</td>\n",
       "      <td>-0.571289</td>\n",
       "      <td>-1.837769</td>\n",
       "      <td>-1.075523</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>304</th>\n",
       "      <td>-208</td>\n",
       "      <td>-1.391796</td>\n",
       "      <td>-0.524126</td>\n",
       "      <td>0.229104</td>\n",
       "      <td>-1.147655</td>\n",
       "      <td>0.172924</td>\n",
       "      <td>-1.834994</td>\n",
       "      <td>-0.420088</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "     Time        FZ        CZ        PZ        F3        P3        F4  \\\n",
       "300  -240 -0.905595 -0.623308 -0.356972 -0.712086 -0.694053 -1.458380   \n",
       "301  -232 -0.927790 -0.282066  0.007158 -0.909757  0.080677 -1.409135   \n",
       "302  -224 -1.280823 -0.689198 -0.298018 -1.425088 -0.194674 -1.459767   \n",
       "303  -216 -1.543690 -0.970792 -0.547014 -1.467396 -0.571289 -1.837769   \n",
       "304  -208 -1.391796 -0.524126  0.229104 -1.147655  0.172924 -1.834994   \n",
       "\n",
       "           P4  Stimulus  \n",
       "300 -0.705150         3  \n",
       "301 -0.542159         3  \n",
       "302 -0.910450         3  \n",
       "303 -1.075523         3  \n",
       "304 -0.420088         3  "
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "means_stim_3 = means[(means.Stimulus == 3)]\n",
    "means_stim_3.head(5)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Now we can plot.** We use both the pandas wrapper for matplotlib, as well as matplotlib itself.\n",
    "\n",
    "The first line specifies the subplot (to overlay lines later on). The second line sets the axis labels. The third through fifth lines specify each data to overlay on the plots (specifically for Stims 1,2 and 3).\n",
    "\n",
    "Note that this first plot is for the electrode 'FZ' (the electrode at the front of the scalp)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x7f221f32f990>"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\nTZIkqVL/D7K38G2FDk2VAAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f221f32f350>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "fig, ax = plt.subplots()\n",
    "ax.set(xlabel=\"Time (in milliseconds)\", ylabel=\"uV (microvolts)\")\n",
    "means_stim_1.plot(x = 'Time', y = 'FZ', kind = 'line', ylim = (-15,15), xlim = (-200,1000), ax=ax, label = 'Stim 1', figsize = (7,5), title = 'FZ')\n",
    "means_stim_2.plot(x = 'Time', y = 'FZ',ax=ax, label = 'Stim 2')\n",
    "means_stim_3.plot(x = 'Time', y = 'FZ',ax=ax, label = 'Stim 3')\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**We can do the same for CZ (the electrode at the center of the scalp).**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x7f221cd8d490>"
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f221cd4a550>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "fig, ax = plt.subplots()\n",
    "ax.set(xlabel=\"Time (in milliseconds)\", ylabel=\"uV (microvolts)\")\n",
    "means_stim_1.plot(x = 'Time', y = 'CZ', kind = 'line', ylim = (-15,15), xlim = (-200,1000), ax=ax, label = 'Stim 1', figsize = (7,5), title = 'CZ')\n",
    "means_stim_2.plot(x = 'Time', y = 'CZ',ax=ax, label = 'Stim 2')\n",
    "means_stim_3.plot(x = 'Time', y = 'CZ',ax=ax, label = 'Stim 3')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**And the same for PZ (the electrode at the back of the scalp).**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x7f221cc45810>"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\nAAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x7f221cc57bd0>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "fig, ax = plt.subplots()\n",
    "ax.set(xlabel=\"Time (in milliseconds)\", ylabel=\"uV (microvolts)\")\n",
    "means_stim_1.plot(x = 'Time', y = 'PZ', kind = 'line', ylim = (-15,15), xlim = (-200,1000), ax=ax, label = 'Stim 1', figsize = (7,5), title = 'PZ')\n",
    "means_stim_2.plot(x = 'Time', y = 'PZ',ax=ax, label = 'Stim 2')\n",
    "means_stim_3.plot(x = 'Time', y = 'PZ',ax=ax, label = 'Stim 3')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**The results are remarkably similar to it's R equivalent!**"
   ]
  }
 ],
 "metadata": {
  "anaconda-cloud": {},
  "kernelspec": {
   "display_name": "Python [conda root]",
   "language": "python",
   "name": "conda-root-py"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.12"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 1
}

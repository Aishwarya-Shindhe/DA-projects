# DA-projects
# This Python 3 environment comes with many helpful analytics libraries installed
# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python
# For example, here's several helpful packages to load

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)

# Input data files are available in the read-only "../input/" directory
# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory

import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))

# You can write up to 20GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using "Save & Run All" 
# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session

# Cleaning Column Names
# Strip spaces and remove special characters
df.columns = df.columns.str.strip().str.replace("\t", "").str.replace(" ", "_").str.lower()

# Convert Price and Tax to numeric by removing commas
df['price'] = df['price'].str.replace(',', '').astype(float)
df['tax'] = pd.to_numeric(df['tax'].str.replace(',', ''), errors='coerce')

# Standardizing 'distance_to_landmark' column (converting meters to km)
def convert_distance(value):
    value = value.strip()
    if 'km' in value:
        return float(value.replace('km', '').strip())
    elif 'm' in value:
        return float(value.replace('m', '').strip()) / 1000  # Convert meters to km
    return None

df['distance_to_landmark'] = df['distance_to_landmark'].apply(convert_distance)

# Handling missing values
df['star_rating'].fillna(df['star_rating'].median(), inplace=True)
df['tax'].fillna(df['tax'].median(), inplace=True)

# Verifying the cleaned data
df.info(), df.head()


import matplotlib.pyplot as plt
import seaborn as sns

# Redefine numeric_df (filter numeric columns)
numeric_df = df.select_dtypes(include='number')

# Set visual style
sns.set(style="whitegrid")

# Histogram for numeric columns
fig, axes = plt.subplots(3, 2, figsize=(14, 16))
fig.suptitle("Distribution of Numerical Variables", fontsize=20)

# Plotting histograms
columns = numeric_df.columns
for i, ax in enumerate(axes.flat):
    if i < len(columns):
        sns.histplot(numeric_df[columns[i]], kde=True, ax=ax, color='skyblue')
        ax.set_title(f"Distribution of {columns[i]}", fontsize=14)

plt.tight_layout()
plt.show()

# Box plots for outlier detection
fig, axes = plt.subplots(3, 2, figsize=(14, 18))
fig.suptitle("Box Plots for Outlier Detection", fontsize=20)

for i, ax in enumerate(axes.flat):
    if i < len(columns):
        sns.boxplot(x=numeric_df[columns[i]], ax=ax, color='lightcoral')
        ax.set_title(f"Box Plot of {columns[i]}", fontsize=14)

plt.tight_layout()
plt.show()

# Correlation Heatmap
plt.figure(figsize=(12, 8))
sns.heatmap(numeric_df.corr(), annot=True, cmap='coolwarm', linewidths=0.5)
plt.title("Correlation Heatmap", fontsize=18)
plt.show()
Data Visualization Insights

📊 1) Distribution of Numerical Variables (Histograms)
Rating: Normally distributed, centered around 4.0.

Reviews: Positively skewed, with most hotels having fewer reviews but a few with very high numbers (outliers).

Star Rating: Concentrated around 4 stars with slight variation.

Distance to Landmark: Broad distribution, indicating hotels are spread across Munnar with varying proximities to landmarks.

Price: Right-skewed distribution, suggesting most hotels have lower prices, but some premium hotels have significantly higher rates.

Tax: Similar right-skewed pattern, with most taxes being low but some high outliers.

📉 2) Box Plots for Outlier Detection
Reviews and Price have the most noticeable outliers.

Tax also shows several outliers, indicating a few hotels with unusually high taxes.

Distance to Landmark shows a fairly even spread with no significant outliers.

Rating and Star Rating have minor outliers but remain relatively consistent.

🔥 3) Correlation Heatmap
Price and Tax: Strong positive correlation, as expected. Higher-priced hotels typically have higher taxes.

Reviews and Rating: Slight positive correlation, indicating higher-rated hotels tend to have more reviews.

Distance to Landmark shows a weak correlation with other variables, suggesting location doesn't significantly impact price or rating.

Star Rating and Price: Mild positive correlation, indicating higher-rated hotels tend to be more expensive.


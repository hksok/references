## Interpretable Machine Learning

reference: https://www.analyticsvidhya.com/blog/2019/08/decoding-black-box-step-by-step-guide-interpretable-machine-learning-models-python/

reference: https://christophm.github.io/interpretable-ml-book/rulefit.html

- interpretation of a machine learning model is the process wherein we try to understand the predictions of a machine learning model.
- why do we need interpretable machine learning?
> - fairness
> - checking causality of features & debugging models
> - regulations

**Framework for Interpretable Machine Learning**

Scope:

- Whether we are looking to interpret globally for all data points, the importance of each variable, or are we looking to explain a particular prediction which is local?

Model:

The second way of looking at this is whether we are talking about a technique that works across all types of models (model agnostic) or is tailor-made for a particular class of algorithms (model specific).

**Global Surrogate Mthod**

- overall, we want an interpretable surrogate model that is trained to approximate the predictions of a black-box model and draw conclusions.
- step by step breakdown to understand how a global surrogate model works:

1. We get predictions from the black-box model
2. Next, we select an interpretable model (linear, decision tree, etc.)
3. we train an interpretable model on the original dataset and use black box preditions as the target
4. Measure the performance of the surrogate model
5. Finally, we interpret the surrogate model to understand how the black-box model is making its decisions.

**LIME (Local Interpretable Model agnostic Explanations)**

The global surrogate method is good for looking at an interpretable model that can explain predictions for a black-box approach. However, this will not work well if we want to understand how a single prediction was made for a given observation.

This is where we use the LIME technique which stands for local interpretable model agnostic explanations. LIME is based on the work presented in this paper:

https://arxiv.org/abs/1602.04938

Let us understand how LIME works using an example.

The steps:

- select your instance of interest for which you want have an explanation of its black box prediction
- perturb your dataset and get the black box predictions for these new fake data points
- weight the new samples according to their proximity to the instance of interest
- train a weighted, interpretable model on the dataset with the variations
- explain the prediction by interpreting the local model

---

## Python implementation of Interpretable ML techniques

```python
# importing the required libraries

import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error

from sklearn.linear_model import LinearRegression
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor
from xgboost.sklearn import XGBRegressor
from sklearn.preprocessing import OneHotEncoder, LabelEncoder
from sklearn import tree

# reading data

df = pd.read_csv('data.csv')

# Missing Value Treatment

df['Item_Weight'].fillna(df['Item_Weight'].median(), inplace=True)
df['Outlet_Size'].fillna(df['Outlet_Size'].mode()[0], inplace=True)

# Feature Engineering

# creating a broad category of type of Items
df['Item_Type_Combined'] = df['Item_Identifier'].apply(lambda df: df[0:2])
df['Item_Type_Combined'] = df['Item_Type_Combined'].map({'FD':'Food', 'NC':'Non-Consumable', 'DR':'Drinks'})

df['Item_Type_Combined'].value_counts()

# operating years of the store
df['Outlet_Years'] = 2013 - df['Outlet_Establishment_Year']

# modifying categories of Item_Fat_Content
df['Item_Fat_Content'] = df['Item_Fat_Content'].replace({'LF':'Low Fat', 'reg':'Regular', 'low fat':'Low Fat'})
df['Item_Fat_Content'].value_counts()

# Data Preprocessing

# label encoding the ordinal variables
le = LabelEncoder()
df['Outlet'] = le.fit_transform(df['Outlet_Identifier'])
var_mod = ['Item_Fat_Content','Outlet_Location_Type','Outlet_Size','Item_Type_Combined','Outlet_Type','Outlet']
le = LabelEncoder()
for i in var_mod:
df[i] = le.fit_transform(df[i])

# one hot encoding the remaining categorical variables 
df = pd.get_dummies(df, columns=['Item_Fat_Content','Outlet_Location_Type','Outlet_Size','Outlet_Type',
                              'Item_Type_Combined','Outlet'])

# Train-Test Split

# dropping the ID variables and variables that have been used to extract new variables
df.drop(['Item_Type','Outlet_Establishment_Year', 'Item_Identifier', 'Outlet_Identifier'],axis=1,inplace=True)

# separating the dependent and independent variables
X = df.drop('Item_Outlet_Sales',1)
y = df['Item_Outlet_Sales']

# creating the training and validation set
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.25, random_state=42)

# training a decision tree model

dt = DecisionTreeRegressor(max_depth = 5, random_state=10)

# fitting the decision tree model on the training set
dt.fit(X_train, y_train)

# use the Graphviz library to visualize the decision tree

# Visualising the decision tree
decision_tree = tree.export_graphviz(dt, out_file='tree.dot', feature_names=X_train.columns, filled=True, max_depth=2)

# converting the dot image to png format
!dot -Tpng tree.dot -o tree.png

#plotting the decision tree
image = plt.imread('tree.png')
plt.figure(figsize=(25,25))
plt.imshow(image)

# Feature Importance

# creating the Random Forest Regressor model
rf = RandomForestRegressor(n_estimators=200, max_depth=5, min_samples_leaf=100,n_jobs=-1)

# feature importance of the random forest model
feature_importance = pd.DataFrame()
feature_importance['variable'] = X_train.columns
feature_importance['importance'] = rf.feature_importances_

# feature_importance values in descending order
feature_importance.sort_values(by='importance', ascending=False).head(10)

# Global Surrogate

# saving the predictions of Random Forest as new target
new_target = rf.predict(X_train)

# defining the interpretable decision tree model
dt_model = DecisionTreeRegressor(max_depth=5, random_state=10)

# fitting the surrogate decision tree model using the training set and new target
dt_model.fit(X_train,new_target)

# Implementing LIME in Python to generate local interpretations of black-box models

# installing lime library
!pip install lime

# import Explainer function from lime_tabular module of lime library
from lime.lime_tabular import LimeTabularExplainer

# training the random forest model
rf_model = RandomForestRegressor(n_estimators=200,max_depth=5, min_samples_leaf=100,n_jobs=-1, random_state=10)
rf_model.fit(X_train, y_train)

# creating the explainer function
explainer = LimeTabularExplainer(X_train.values, mode="regression", feature_names=X_train.columns)

# storing a new observation
i = 6
X_observation = X_test.iloc[[i], :]

* RF prediction: {rf_model.predict(X_observation)[0]}

# Generate Explanations using LIME

# explanation using the random forest model
explanation = explainer.explain_instance(X_observation.values[0], rf_model.predict)
explanation.show_in_notebook(show_table=True, show_all=False)
print(explanation.score)
```
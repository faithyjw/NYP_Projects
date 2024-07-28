#libraries
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from datetime import datetime

#load dataset
df1 = pd.read_csv("Car details v3.csv")
print("df1 shape: ", df1.shape) #df1 rows and columns

#extract first word from the "name" column
df1["Brand"] = df1["name"].str.split().str[0]

#extract last few words from the "name"column
def carModel(name):
    words = name.split()
    return ' '.join(words[1:])
df1["Model"] = df1["name"].apply(carModel)

#extract value from the "mileage" column
df1["Mileage"] = df1['mileage'].str.split().str[0]
df1['Mileage'] = df1['Mileage'].astype(float) #change dtype from str to float

#extract units from the "mileage" column
df1["Mileage Unit"] = df1['mileage'].str.split().str[-1]
print("Unique values in Mileage Unit: ", df1["Mileage Unit"].unique()) #to check unique values

#count of values in each unit
kmkg = 0
kmpl = 0
for i in df1.mileage:
    if str(i).endswith("km/kg"):
        kmkg+=1
    elif str(i).endswith("kmpl"):
        kmpl+=1
print("No. of rows in km/kg : {} ".format(kmkg))
print("No. of rows in kmpl : {} ".format(kmpl))

#conversion of units - km/kg to kmpl
def convertMileageUnit(row):
    if row['Mileage Unit'] == 'km/kg':
        return row['Mileage'] * 1.4  #km/kg to kmpl, multiply value by 1.4
    return row['Mileage']
df1['Mileage'] = df1.apply(convertMileageUnit, axis=1)

#extract value from the "engine" column
df1["Engine (cc)"] = df1['engine'].str.split().str[0]

#extract units from "max_power" column
df1["Max Power Unit"] = df1['max_power'].str.split().str[-1]
print("Unique values in Max Power Unit: ", df1["Max Power Unit"].unique()) #check for unique values

#extract value from "max_power" column
df1["Max Power (bhp)"] = df1['max_power'].str.split().str[0]

#converting values from "km_driven" into kilometers
df1['Driven (km)'] = df1['km_driven'] / 1000

#drop columns
df1 = df1.drop(columns= ["name", "mileage", "engine", "torque", "max_power", "Max Power Unit",
                         "km_driven", "Mileage Unit"])

#rename columns
df1.rename(columns={"year": "Manufacturing Year", "selling_price": "Sold Price ($)", 
                    "fuel": "Fuel Type", "seller_type": "Seller Type",
                    "transmission" : "Transmission Type", "owner" : "Owner Type", 
                    "seats": "No. of Seats", "Mileage" : "Mileage (kmpl)"}, inplace= True)

#handling missing values
totalRows = len(df1) #no. of rows in df

#no. of rows that contain missing values
countNArows = df1.isnull().any(axis=1).sum()
print("Count of NA in ROWS: ", countNArows)

#no. & name of columns that contain missing values
countNAcolumns = df1.isnull().any(axis=0).sum() #count
print("Count of NA in COLUMNS: ", countNAcolumns)
columnsNameNA = df1.columns[df1.isnull().any()] #name
print ("Columns that contain NA: ", columnsNameNA)
df1 = df1.dropna(subset=['No. of Seats', 'Mileage (kmpl)', #drop missing values in these columns
                         'Engine (cc)', 'Max Power (bhp)'])
df1= df1[~df1['Max Power (bhp)'].str.contains('bhp', na=False)] #remove the row that contained "bhp")

#to calculate the amt in percentage when I removed NA rows
totalRows2 = len(df1) #no. of rows in df
percentageDrop = ((totalRows - totalRows2)/totalRows) *100
print("Percentage of the rows removed: " , percentageDrop)

#rounding data values to be respective decimal places
df1[['Mileage (kmpl)']] = df1[['Mileage (kmpl)']].round(1)
df1[['Max Power (bhp)']] = df1[['Max Power (bhp)']].round(1)
df1[['Driven (km)']] = df1[['Driven (km)']].round(2)

#checking dtypes & changing to its respective correct dtypes
print(df1.head())
df1['No. of Seats'] = df1['No. of Seats'].astype(int) #int
df1['Engine (cc)'] = df1['Engine (cc)'].astype(int) #int
df1['Max Power (bhp)'] = df1['Max Power (bhp)'].astype(float) #float
print(df1.dtypes)

#handling duplicated values
duplicateRows = df1.duplicated()
totalDuplicateRows = duplicateRows.sum() #total duplicated rows
print("No. of duplicate rows: ", totalDuplicateRows)
df1 = df1.drop_duplicates() #removing duplicated rows
print(df1)

#create "car age" column
currentYear = datetime.now().year
df1['Car Age'] = currentYear - df1['Manufacturing Year']

#encoding "owner type" column
ownerTypeEncode ={'Test Drive Car': 0, 'First Owner': 1,
                     'Second Owner': 2, 'Third Owner': 3,
                     'Fourth & Above Owner': 4,}
df1["Owner Type Encoded"] = df1["Owner Type"].map(ownerTypeEncode)
df1 = df1.drop(columns= ["Owner Type"])#drop owner type column

#create "car condition" column based on "driven (km)" & "car age" columns
def car_condition(row):
    if row["Car Age"] <= 5 and row["Driven (km)"] <= 50000:
        return "Good"
    elif row["Car Age"] <= 15 and row["Driven (km)"] <= 100000:
        return "Fair"
    else:
        return "Poor"
df1["Car Condition"] = df1.apply(car_condition, axis=1)

#checking for outliers in df
df1.select_dtypes(include=[np.number]).boxplot(figsize=(10, 6), vert=False)
plt.show()

#rearrange columns
print(df1.columns) #to show column names
columns_order = [
    "Brand", "Model", "Manufacturing Year", "Car Age", "Driven (km)", "Car Condition", 
    "No. of Seats", "Owner Type Encoded", "Transmission Type", "Fuel Type", "Engine (cc)", 
    "Mileage (kmpl)", "Max Power (bhp)", "Seller Type", "Sold Price ($)"]
df1 = df1[columns_order]

print(df1.describe()) #descriptive statistics

#to see distribution of seller type with selling price
plt.figure(figsize=(10, 6))
sns.boxplot(x='Seller Type', y='Sold Price ($)', data=df1)
plt.title('Sold Price Distribution by Seller Type')
plt.ylabel('Sold Price ($)')
plt.xlabel('Seller Type')
plt.show()

#to see relationship between no. of seats and selling price
plt.figure(figsize=(10, 6))
sns.regplot(x='No. of Seats', y='Sold Price ($)', data=df1, scatter_kws={'s':50}, line_kws={'color':'red'})
plt.title('Relationship between No. of Seats and Sold Price ($)')
plt.xlabel('No. of Seats')
plt.ylabel('Sold Price ($)')
plt.grid(True)
plt.show()

#export csv df to excel df
df1.to_excel('PAP_CarDataset.xlsx', sheet_name= "clean_data", index=False)

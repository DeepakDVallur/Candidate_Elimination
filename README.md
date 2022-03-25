## Implement and demonstrate the Candidate-Elimination algorithm to output a description of the set of all hypotheses consistent with the training examples, where a given set of training data examples stored in a .CSV file 

**Candidate-Elimination Learning Algorithm**

The CANDIDATE-ELIMINTION algorithm computes the version space containing all hypotheses from H that are consistent with an observed sequence of training examples.

```
Initialize G to the set of maximally general hypotheses in H
Initialize S to the set of maximally specific hypotheses in H
For each training example d, do

• If d is a positive example
  • Remove from G any hypothesis inconsistent with d
  • For each hypothesis s in S that is not consistent with d
      • Remove s from S
      • Add to S all minimal generalizations h of s such that
          • h is consistent with d, and some member of G is more general than h
      • Remove from S any hypothesis that is more general than another hypothesis in S

• If d is a negative example
  • Remove from S any hypothesis inconsistent with d
  • For each hypothesis g in G that is not consistent with d
      • Remove g from G
      • Add to G all minimal specializations h of g such that
        • h is consistent with d, and some member of S is more specific than h
      • Remove from G any hypothesis that is less general than another hypothesis in G
```

### To illustrate this algorithm, assume the learner is given the sequence of training examples from the EnjoySport task


| Example | Sky    | AirTemp | Humidity  | Wind    | Water | Forecast  | EnjoySport  |
| ------- | ------ | ------- | --------  | ------- | ----- | --------  | ----------  |
| 1       | Sunny  | Warm    | Normal    | Strong  | Warm  | Same      | Yes         |
| 2       | Sunny  | Warm    | High      | Strong  | Warm  | Same      | Yes         |
| 3       | Rainy  | Cold    | High      | Strong  | Warm  | Change    | No          |
| 4       | Sunny  | Warm    | High      | Strong  | Cool  | Change    | Yes         |

-------------------------------------------------------------------------------------------------------------------------------------
CANDIDATE-ELIMINTION algorithm begins by initializing the version space to the set of all hypotheses in H; 

![image](https://user-images.githubusercontent.com/88773998/160067855-f580e173-c681-4451-aa70-7b8088ee290b.png)

-	***When the first training example is presented***, the CANDIDATE-ELIMINTION algorithm checks the S boundary and finds that it is overly specific and it fails to cover the positive example. 
-	The boundary is therefore revised by moving it to the least more general hypothesis that covers this new example
-	No update of the G boundary is needed in response to this training example because Go correctly covers this example

![image](https://user-images.githubusercontent.com/88773998/160068342-e466c8d7-10df-4819-9b67-cea804500758.png)

-------------------------------------------------------------------------------------------------------------------------------------

-	***When the second training example is observed***, it has a similar effect of generalizing S further to S2, leaving G again unchanged i.e., G2 = G1 = G0

![image](https://user-images.githubusercontent.com/88773998/160068485-e465621c-32a7-48d8-bb39-34f39d240a50.png)

-------------------------------------------------------------------------------------------------------------------------------------

- ***Consider the third training example***. This negative example reveals that the G boundary of the version space is overly general, that is, the hypothesis in G incorrectly predicts that this new example is a positive example.
- The hypothesis in the G boundary must therefore be specialized until it correctly classifies this new negative example

![image](https://user-images.githubusercontent.com/88773998/160068716-721dcafd-8baf-4029-946a-848372e3c600.png)

Given that there are six attributes that could be specified to specialize G2, why are there only three new hypotheses in G3? 

For example, the hypothesis h = (?, ?, Normal, ?, ?, ?) is a minimal specialization of G2 that correctly labels the new example as a negative example, but it is not included in G3. The reason this hypothesis is excluded is that it is inconsistent with the previously encountered positive examples

-------------------------------------------------------------------------------------------------------------------------------------

- ***Consider the fourth training example.***
![image](https://user-images.githubusercontent.com/88773998/160068827-4df4b46b-7ab0-4798-a722-326cabfb72c1.png)

- This positive example further generalizes the S boundary of the version space. It also results in removing one member of the G boundary, because this member fails to cover the new positive example

-------------------------------------------------------------------------------------------------------------------------------------

After processing these four examples, the boundary sets S4 and G4 delimit the version space of all hypotheses consistent with the set of incrementally observed training examples.

![image](https://user-images.githubusercontent.com/88773998/160068948-61b92aaa-5bd5-4bea-bc62-882ca15fd6ee.png)

-------------------------------------------------------------------------------------------------------------------------------------
### Code

```python
import numpy as np 
import pandas as pd

data = pd.read_csv('2.csv')
concepts = np.array(data.iloc[:,0:-1])
target = np.array(data.iloc[:,-1])  
def learn(concepts, target): 
    specific_h = concepts[0].copy()  
    print("initialization of specific_h \n",specific_h)  
    general_h = [["?" for i in range(len(specific_h))] for i in range(len(specific_h))]     
    print("initialization of general_h \n", general_h)  

    for i, h in enumerate(concepts):
        if target[i] == "yes":
            print("If instance is Positive ")
            for x in range(len(specific_h)): 
                if h[x]!= specific_h[x]:                    
                    specific_h[x] ='?'                     
                    general_h[x][x] ='?'
                   
        if target[i] == "no":            
            print("If instance is Negative ")
            for x in range(len(specific_h)): 
                if h[x]!= specific_h[x]:                    
                    general_h[x][x] = specific_h[x]                
                else:                    
                    general_h[x][x] = '?'        

        print(" step {}".format(i+1))
        print(specific_h)         
        print(general_h)
        print("\n")
        print("\n")

    indices = [i for i, val in enumerate(general_h) if val == ['?', '?', '?', '?', '?', '?']]    
    for i in indices:   
        general_h.remove(['?', '?', '?', '?', '?', '?']) 
    return specific_h, general_h 

s_final, g_final = learn(concepts, target)

print("Final Specific_h:", s_final, sep="\n")
print("Final General_h:", g_final, sep="\n")

```

### Output
```
initialization of specific_h 
 ['sunny' 'warm' 'normal' 'strong' 'warm' 'same']
initialization of general_h 
 [['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], 
 ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]

If instance is Positive 
 step 1
['sunny' 'warm' 'normal' 'strong' 'warm' 'same']
[['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], 
['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]

If instance is Positive 
 step 2
['sunny' 'warm' '?' 'strong' 'warm' 'same']
[['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], 
['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]


If instance is Negative 
 step 3
['sunny' 'warm' '?' 'strong' 'warm' 'same']
[['sunny', '?', '?', '?', '?', '?'], ['?', 'warm', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], 
['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', 'same']]


If instance is Positive 
 step 4
['sunny' 'warm' '?' 'strong' '?' '?']
[['sunny', '?', '?', '?', '?', '?'], ['?', 'warm', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], 
['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]


Final Specific_h:
['sunny' 'warm' '?' 'strong' '?' '?']
Final General_h:
[['sunny', '?', '?', '?', '?', '?'], ['?', 'warm', '?', '?', '?', '?']]

```
### Reference

Machine Learning - Tom M. Mitchell - McGraw-Hill, 1997

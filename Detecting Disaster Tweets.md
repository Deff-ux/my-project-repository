# Detecting Disaster Tweets

Twitter has become an important communication channel in times of emergency.
The ubiquitousness of smartphones enables people to announce an emergency they’re observing in real-time. Because of this, more agencies are interested in programatically monitoring Twitter (i.e. disaster relief organizations and news agencies).

The goals of this project is to **build a machine learning model that predicts which Tweets are about real disasters and which one’s aren’t.**

## Import, Read, and Understand the Data Sets

The data and competition outline of this project can be viewed [here](https://www.kaggle.com/c/nlp-getting-started/data).


```python
import pandas as pd
pd.set_option('display.max_colwidth', -1)

#import train data
train_data = pd.read_csv('train.csv')
train_data.head()
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
      <th>id</th>
      <th>keyword</th>
      <th>location</th>
      <th>text</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Our Deeds are the Reason of this #earthquake May ALLAH Forgive us all</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>4</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Forest fire near La Ronge Sask. Canada</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>All residents asked to 'shelter in place' are being notified by officers. No other evacuation or shelter in place orders are expected</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13,000 people receive #wildfires evacuation orders in California</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Just got sent this photo from Ruby #Alaska as smoke from #wildfires pours into a school</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#import test data
test_data = pd.read_csv('test.csv')
test_data.head()
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
      <th>id</th>
      <th>keyword</th>
      <th>location</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Just happened a terrible car crash</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Heard about #earthquake is different cities, stay safe everyone.</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>there is a forest fire at spot pond, geese are fleeing across the street, I cannot save them all</td>
    </tr>
    <tr>
      <td>3</td>
      <td>9</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Apocalypse lighting. #Spokane #wildfires</td>
    </tr>
    <tr>
      <td>4</td>
      <td>11</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Typhoon Soudelor kills 28 in China and Taiwan</td>
    </tr>
  </tbody>
</table>
</div>




```python
#import sample submission
sample_submission = pd.read_csv('sample_submission.csv')
sample_submission.head()
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
      <th>id</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>9</td>
      <td>0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>11</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



## Preparing the data for machine learning

Perform some data cleaning and preprocessing. Drop the `location` and `keyword` features, and only use the actual text from the tweet for training. We are also going to drop the `id` column as this won’t be useful for training the model.


```python
train_data = train_data.drop(['keyword', 'location', 'id'], axis=1)
train_data.head()
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
      <th>text</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Our Deeds are the Reason of this #earthquake May ALLAH Forgive us all</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Forest fire near La Ronge Sask. Canada</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>All residents asked to 'shelter in place' are being notified by officers. No other evacuation or shelter in place orders are expected</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>13,000 people receive #wildfires evacuation orders in California</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Just got sent this photo from Ruby #Alaska as smoke from #wildfires pours into a school</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



### Cleaning Special Characters

Perform cleaning of special characters that are not necessarily going to be meaningful to a machine learning algorithm. The first step therefore taking is to remove these, also making all words lower case.


```python
import re
def  clean_text(df, text_field):
    df[text_field] = df[text_field].str.lower()
    df[text_field] = df[text_field].apply(lambda elem: re.sub(r"(@[A-Za-z0-9]+)|([^0-9A-Za-z \t])|(\w+:\/\/\S+)|^rt|http.+?", "", elem))  
    return df
data_clean = clean_text(train_data, "text")
data_clean.head()
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
      <th>text</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>our deeds are the reason of this earthquake may allah forgive us all</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>forest fire near la ronge sask canada</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>all residents asked to shelter in place are being notified by officers no other evacuation or shelter in place orders are expected</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>13000 people receive wildfires evacuation orders in california</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>just got sent this photo from ruby alaska as smoke from wildfires pours into a school</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



### Using NLTK Library for Processing Text Data

NLTK is a collection of python libraries and tools for processing text data, the full documentation can be accessed [here](https://www.nltk.org/). In addition to its processing tools, NLTK also has a large collection of text corpora and lexical resources, including one containing all stop words in a variety of languages. We are going to use this library to remove the stop words from our data sets.


```python
import nltk.corpus
nltk.download('stopwords')
```

    [nltk_data] Downloading package stopwords to
    [nltk_data]     C:\Users\defri\AppData\Roaming\nltk_data...
    [nltk_data]   Unzipping corpora\stopwords.zip.
    




    True




```python
#Using NLTK to read the stop words and use it to remove them from the tweets.
from nltk.corpus import stopwords
stop = stopwords.words('english')
data_clean['text'] = data_clean['text'].apply(lambda x: ' '.join([word for word in x.split() if word not in (stop)]))
data_clean.head()
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
      <th>text</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>deeds reason earthquake may allah forgive us</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>forest fire near la ronge sask canada</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>residents asked shelter place notified officers evacuation shelter place orders expected</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>13000 people receive wildfires evacuation orders california</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>got sent photo ruby alaska smoke wildfires pours school</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



## Data preprocessing

Preprocessing to prepare it for use in a machine learning algorithm. I am going to be using two from the scikit-learn library. 

* The first step in this process is to split the data into tokens, or individual words, count the frequency in which each word appears in the text, and then represent these counts as a sparse matrix. The [CountVectoriser](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html) function achieves this.

* The next step is to apply a weighting to the word counts produced by CountVectoriser. The goal of applying this weighting is to scale down the impact of very frequently occurring words in the text, so that less frequently occurring, and perhaps more informative words are deemed important during the model training. [TfidTransformer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfTransformer.html) can perform this function.

### Machine learning pipeline

Using a linear support vector machine classifier (SGDClassifier).


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(data_clean['text'],data_clean['target'],random_state = 0)

from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.pipeline import Pipeline
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction.text import TfidfTransformer
from sklearn.linear_model import SGDClassifier

pipeline_sgd = Pipeline([
    ('vect', CountVectorizer()),
    ('tfidf',  TfidfTransformer()),
    ('nb', SGDClassifier()),
])
model = pipeline_sgd.fit(X_train, y_train)
```

Use the trained model to predict on our reserved test data and see how the model performs.


```python
from sklearn.metrics import classification_report

y_predict = model.predict(X_test)
print(classification_report(y_test, y_predict))
```

                  precision    recall  f1-score   support
    
               0       0.80      0.85      0.83      1107
               1       0.78      0.71      0.74       797
    
        accuracy                           0.79      1904
       macro avg       0.79      0.78      0.79      1904
    weighted avg       0.79      0.79      0.79      1904
    
    


```python
#clean the text in the test file and use the model to make predictions. 
submission_test_clean = test_data.copy()
submission_test_clean = clean_text(submission_test_clean, "text")
submission_test_clean['text'] = submission_test_clean['text'].apply(lambda x: ' '.join([word for word in x.split() if word not in (stop)]))
submission_test_clean = submission_test_clean['text']
submission_test_clean.head()
```




    0    happened terrible car crash                                  
    1    heard earthquake different cities stay safe everyone         
    2    forest fire spot pond geese fleeing across street cannot save
    3    apocalypse lighting spokane wildfires                        
    4    typhoon soudelor kills 28 china taiwan                       
    Name: text, dtype: object




```python
#use the model to create predictions.
submission_test_pred = model.predict(submission_test_clean)
```


```python
#construct a dataframe containing just the id from the test set and our prediction.
id_col = test_data['id']
submission_df_1 = pd.DataFrame({
                  "id": id_col, 
                  "target": submission_test_pred})
submission_df_1.head()
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
      <th>id</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>9</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>11</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#save as a CSV file.
submission_df_1.to_csv('submission_1.csv', index=False)
```

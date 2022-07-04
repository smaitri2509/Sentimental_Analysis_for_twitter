# Sentimental_Analysis_for_twitter
import tweepy 
import string
from textblob import TextBlob
from wordcloud import WordCloud
import pandas as pd 
import numpy as np
import re 
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')
consumer_key ='lzhXLgAESsTdme2XnFCnZgMHc'
consumer_secret ='AHNyew1YoH1z5ePSHZ5zn5nEcE9p9cuwcKRNyvMAbZ0U3DD5ay'
key = '1537686324664143872-WfxfNDqw5EherNJAO8BcL6REZOIvFs'
secret = 'QsQVnzDUTt16oz3lGzELzrr5KGcw1YZ4SVdqZl1TEruml'
auth_handler = tweepy.OAuthHandler(consumer_key ,consumer_secret)
auth_handler.set_access_token(key , secret)
api = tweepy.API(auth_handler,wait_on_rate_limit=True)
#fetch tweets
q = input("enter the user id")
try :
  posts=api.user_timeline(screen_name = q ,count = 100 , lang ="en" , tweet_mode ="extended")
except:
  print('inivalid userid')
#creating dataframe using pandas
df=pd.DataFrame([tweet.full_text for tweet in posts],columns=['Tweets'])
#cleaning the tweets
def clean(text):
  text = re.sub(r'@[A-Za-z0-9]+' ,'',text)
  text = re.sub(r'#','',text)
  text=  re.sub(r'RT[\s]+' ,'',text)
  text= re.sub(r'https*\S+', ' ', text)
  return text
df['Tweets'] = df['Tweets'].apply(clean)
#finding subjectivity of tweets
def subjectivity(text):
  return TextBlob(text).subjectivity
#finding polarity of tweets
def polarity(text):
  return TextBlob(text).polarity
df['subjectivity'] = df['Tweets'].apply(subjectivity)
df['polarity'] = df ['Tweets'].apply(polarity)
def ana(score):
  if score<0:
    return 'negative'
  elif score == 0:
    return 'neutral'
  else:
    return 'positive'
df['Analysis'] = df['polarity'].apply(ana)
result = input("In what format you want your result to be in \n 1.WORDCLOUD \n 2.SCATTER GRAPH \n 3.BOTH\n")
if result == '1':
  allWords = ' '.join([twts for twts in df['Tweets']])
  wordCloud = WordCloud(width = 500 , height = 300 , random_state = 21 ,max_font_size=119).generate(allWords)
  plt.imshow(wordCloud,interpolation="bilinear")
  plt.axis('off')
  plt.show()
elif result== '2':
  plt.figure(figsize=(8,6))
  for i in range(0,df.shape[0]):
   plt.scatter(df['polarity'][i],df['subjectivity'][i],color='red')
  plt.title('sentimetal Analysis')
  plt.xlabel('polarity')
  plt.ylabel('subjectivity')
  plt.show()
  
elif result == '3':
  allWords = ' '.join([twts for twts in df['Tweets']])
  wordCloud = WordCloud(width = 500 , height = 300 , random_state = 21 ,max_font_size=119).generate(allWords)
  plt.imshow(wordCloud,interpolation="bilinear")
  plt.axis('off')
  plt.show()
  plt.figure(figsize=(8,6))
  for i in range(0,df.shape[0]):
   plt.scatter(df['polarity'][i],df['subjectivity'][i],color='red')
  plt.title('sentimetal Analysis')
  plt.xlabel('polarity')
  plt.ylabel('subjectivity')
  plt.show()
else :
  print('enter a valid input')

print('Positive %')
pt = df[df.Analysis == 'positive']
pt = pt['Tweets']
print(round((pt.shape[0]/df.shape[0])*100 , 1))
print('Negative %')
nt = df[df.Analysis == 'negative']
nt = nt['Tweets']
print(round((nt.shape[0]/df.shape[0])*100 , 1))
print('Neutral Comment %')
nu =df[df.Analysis == 'neutral']
nu = nu['Tweets']
round((nu.shape[0]/df.shape[0])*100 , 1)


# Sentimental Analysis

## Introduction
This project is a sentiment analysis tool that utilizes various Python libraries to scrape data, process it, and provide voice outputs. The main objective is to create an interactive and user-friendly sentiment analysis program.

### Code Snippet
```python
from ntscraper import Nitter
import pprint
import pandas as pd
import pyttsx3

# Welcome voice 
engine = pyttsx3.init()

def sys_voice():
    voices = engine.getProperty('voices')
    engine.setProperty('voice', voices[1].id)
    engine.setProperty('rate', 150)
    engine.setProperty('volume', 1.0)
    engine.say('Sentiment Analysis Program Online...')
    engine.runAndWait()

def voice_output(command):
    engine.say(command)
    engine.runAndWait()

sys_voice()
```

## Data Collection
This section describes the data collection process using the Nitter library to scrape tweets based on hashtags.

### Code Snippet
```python
def data_collection(t_hash, start_date, last_date): 
    scraper = Nitter(log_level=1, skip_instance_check=False) 
    tweets = scraper.get_tweets(t_hash, mode="hashtag", max_retries=5, since=start_date, until=last_date)
    return tweets

voice_output('Twitter data extraction module online...')
```

## Sentiment Analysis
This section explains how the sentiment analysis is performed using TextBlob and Google Translate to analyze the sentiment of tweets.

### Code Snippet
```python
from textblob import TextBlob
from textblob.sentiments import NaiveBayesAnalyzer
from googletrans import Translator

translator = Translator()

# Performing the Sentiment Analysis 
def reading_data():
    pos_tweet_score = []
    neg_tweet_score = []
    df = pd.read_csv('mydata.csv')
    cleaned_data = df.dropna()
    for tweet in cleaned_data['Ltweets']:
        try:
            translated = translator.translate(tweet, dest='en')
            if translated and translated.text:
                print(translated.text)
                blob = TextBlob(translated.text, analyzer=NaiveBayesAnalyzer())
                pos_tweet_score.append(blob.sentiment.p_pos)
                neg_tweet_score.append(blob.sentiment.p_neg)
            else:
                print("Translation failed or returned empty text.")
        except Exception as e:
            print(f"Translation error: {e}")
    voice_output("thanks for waiting....")
    voice_output("the ratio of positive and negative comments are on your screen")
    print(f'positive {sum(pos_tweet_score)/len(pos_tweet_score)}')
    print(f'Negative {sum(neg_tweet_score)/len(neg_tweet_score)}')
voice_output("Data reading and Sentimental Analysis module online...")
```

## User Interaction
This section details how the program interacts with the user to either fetch new data or use existing data for sentiment analysis.

### Code Snippet
```python
# Taking input from the user
def user_choice():
    voice_output('Would you like to access existing data or enter new data? Press 1 for new data or press 2 for existing data.')

    voice_output('Waiting for your input..')
    choice = int(input('Enter your choice'))
    if choice == 1:
        voice_output("Enter the hashtag you want to fetch")
        t_hash = input("Enter the hashtag")
        voice_output("Could you please let me know the starting date for the data you need? make sure you enter date in correct format")
        start_date = input('Enter the date in yyyy-mm-dd format for example 2000-03-28')
        voice_output("please take your time to let me know until when you would like to have the data make sure you enter date in correct format")
        last_date = input('Enter the date in yyyy-mm-dd format for example 2000-03-28')
        data = data_collection(t_hash, start_date, last_date)
        list_tweets = {'Ltweets': []}
        for _ in range(len(data["tweets"])):
            list_tweets['Ltweets'].append(data["tweets"][_]['text'])
        df = pd.DataFrame.from_dict(list_tweets)
        voice_output("Showing the glimpse of data in raw format.")
        pprint.pp(data)
        # Storing the data to csv file "encoded data"
        voice_output("Data collection Complete... storing in csv file.")
        df.to_csv('mydata.csv')
        reading_data()
    elif choice == 2:
        voice_output('starting sentimental analysis, it may take minutes please wait....')
        reading_data()
    else:
        voice_output("Wrong input enter data again...")
        user_choice()

voice_output("input module online.....")

user_choice()
```

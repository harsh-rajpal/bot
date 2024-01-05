# bot
Hey, Welcome to my bot repository. This is a bot that I made while learning python. It is a simple bot that can do some basic tasks. I am still working on it and will add more features to it. If you want to contribute to this project, you can fork this repository and make changes to it. I will be happy to merge your changes to this repository. 

## Features
- [x] Greet
- [x] Reply to some basic predefined questions

## How to use
1. Clone this repository
2. Install the required dependencies and create a virtual environment.
3. Create new.py file and add your bot token in it.
4. Create intents.json file and add your intents in it.
5. Create a new file named `bot.py` and add the following code in it.
```python
import random
import json
import pickle
import numpy as np
import nltk

from nltk.stem import WordNetLemmatizer
from keras.models import load_model

lemmatizer = WordNetLemmatizer()
intents = json.loads(open('C:/Users/rajpa/Desktop/bot/bot/intents.json').read())

words = pickle.load(open('words.pkl', 'rb'))
classes = pickle.load(open('classes.pkl', 'rb'))
model = load_model('chatbot_model.h5')


def clean_up_sentence(sentence):
    sentence_words = nltk.word_tokenize(sentence)
    sentence_words = [lemmatizer.lemmatize(word) for word in sentence_words]
    return sentence_words

def bag_of_words (sentence):
    sentence_words = clean_up_sentence(sentence)
    bag = [0] * len(words)
    for w in sentence_words:
        for i, word in enumerate(words):
            if word == w:
                bag[i] = 1
    return np.array(bag)

def predict_class (sentence):
    bow = bag_of_words (sentence)
    res = model.predict(np.array([bow]))[0]
    ERROR_THRESHOLD = 0.25
    results = [[i, r] for i, r in enumerate(res) if r > ERROR_THRESHOLD]

    results.sort(key=lambda x: x[1], reverse=True)
    return_list = []
    for r in results:
        return_list.append({'intent': classes [r[0]], 'probability': str(r[1])})
    return return_list

def get_response(intents_list, intents_json):
    tag = intents_list[0]['intent']
    list_of_intents = intents_json['intents']
    for i in list_of_intents:
        if i['tag'] == tag:
            result = random.choice (i['responses'])
            break
    return result

print("GO! Bot is running!")

while True:
    message = input("")
    ints = predict_class (message)
    res = get_response (ints, intents)
    print (res)
```

6. Run the bot using `python bot.py`
7. Now you can talk to your bot.
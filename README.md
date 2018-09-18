# Reddit-Thread-Comment-Filter
An extension to the the website Reddit that filters and hides negative comments from being viewed.
#CS2302
#Elijah Gabriel Kalani Pele
#Lab 1
#Instructor:Diego Aguirre, TA: Manoj Saha
#Date Last Modified 09-17-2018
#Purpose of program: To filter a Reddit posts comment tree by recursively placing each comment
#in one of three lists, postivie_comments_list, negative_comments_list,or neutral_comments_list.

import nltk
from nltk.sentiment.vader import SentimentIntensityAnalyzer
import praw
import timeit

#The code below will allow simple access to Reddit's API. This will be used to retrieve the Reddit posts' comment trees.
reddit = praw.Reddit(client_id= 'YWB3gI1yuzWUfQ',
                     client_secret= 'T2-nNOeZfH7dru-B6AgTfptjpWQ',
                     user_agent= 'call_me_pele')

#The code below will donwlad and make accesible the NLTK toolbox. 
#This toolbox will be used to acces the SentimentIntensityAnalyzer().
nltk.download('vader_lexicon')
sid = SentimentIntensityAnalyzer()

#This method will return a number close to 1 if it is a negative sentiment or close to zero if it is not.
def get_text_negative_proba(text):
    return sid.polarity_scores(text)['neg']

#This method will return a number close to 1 if it is a neutral sentiment or close to zero if it is not.
def get_text_neutral_proba(text):
    return sid.polarity_scores(text)['neu']

#This method will return a number close to 1 if it is a positive sentiment or close to zero if it is not.
def get_text_positive_proba(text):
    return sid.polarity_scores(text)['pos']

#This method will return an object sumission.comments
def get_submission_comments(url):
    submission = reddit.submission(url=url)
    submission.comments.replace_more()
    return submission.comments

#The method process_submission() recieves a lsit with reddit comments and appends each
#comment to a certain list depending if it is a positive, negative, or neutral comment.
def process_submission(comments, pos, neg, neu):
    if(comments is None):
        return
    i = 0
    while i < (len(comments)):
        if(comments[0].replies != None):
           process_submission(comments[i].replies, pos, neg, neu)        
           text = (comments[i].body)
           
           #Each comment is rated using the SentimentIntensityAnalyzer()
           #The scores for how close the comment is to a positive, negative or neutral
           #sentiment or neutral sentimentis saved in variables b, a, and c respectively.
           a=get_text_negative_proba(text)
           b=get_text_positive_proba(text)
           c=get_text_neutral_proba(text)
           
           #The variable with the highest score will dictate which list to append the comment to.
           if(a>b and a>c):
               neg.append(text) 
           elif(b>a and b>c):
               pos.append(text)
           else:
               neu.append(text)
           i = i+1
    return pos, neg, neu   
  
#This is the main() method. The previous methods are utilized here to populate three lists;
#negative_comments_list[], positive_comments_list[], and neutral_comments_list[].      
def main():  
    comments = get_submission_comments('https://www.reddit.com/r/learnprogramming/comments/5w50g5/eli5_what_is_recursion/')  
    positive_comments_list=[]
    negative_comments_list=[]
    neutral_comments_list=[]
    
    #The lists are populated with the Reddit posts comments.
    positve_comments_list, negative_comments_list, neutral_comments_list = process_submission(comments, positive_comments_list, negative_comments_list, neutral_comments_list)
    
    
    
    print("Positive comments list:")
    for i in range(len(positive_comments_list)):
        print(positive_comments_list[i])
    print("Negative comments list:")
    for j in range(len(negative_comments_list)):
        print(negative_comments_list[j])
    print("Nutral comments list:")
    for k in range(len(neutral_comments_list)):
        print(neutral_comments_list[k]) 
    
    #Printing total number of comments.    
    a,b,c = len(positive_comments_list), len(negative_comments_list), len(neutral_comments_list)
    total = a+b+c
    print("Total Comments = ", total)
main()

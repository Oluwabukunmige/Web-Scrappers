# Movies Reviews
This is the code snippet to scrape movie reviews from [metacritic](metacritic.com) using beautiful soup.

``` Python
#import all the required modules
import re
import requests, bs4
from requests import get
from bs4 import BeautifulSoup
from IPython.core.display import clear_output
from warnings import warn
from time import sleep
from random import randint
from time import time

#specify the header, to ensure our requests are accepted
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36'}

#loop through pages 0 -10 at interval of 2
pages = [str(i) for i in range(0,10,2)]

#declare lists to stored scraped data
names = []
release_dates = []
ratings= []
meta_scores =[]
user_scores = []

#prepare the monitoring loop
start_time = time()
requests = 0
        
# iterate through the pages
for page in pages:
    
    #make a get request
    movies = get('https://www.metacritic.com/browse/movies/score/metascore/all/filtered?page='+ page, headers = headers)
    
    #pause the loop for 8-20 seconds
    sleep(randint(8,20))
    
    #monitor the requests
    requests += 1
    elapsed_time = time() - start_time
    print('Request:{}; Frequency: {} requests/s'.format(requests, requests/elapsed_time))
    clear_output(wait = True)
    
    #show a warning if a non 200 status code is returned
    if movies.status_code != 200:
        warn('Request: {}; Status code: {}'.format(requests, response.status_code))
    
    #break the loop if the requests exceed 26
    if requests > 10:
        warn('Number of requests was greater than expected.')
        break
   
    #parse the  movie response content into the beautiful soup object
    movie_soup = BeautifulSoup(movies.text, 'html.parser')
    
    #find the major tag peculiar to each movie
    container = movie_soup.find_all('td', class_ = 'clamp-summary-wrap')
     
    #iterate through the major tag   
    for con in container:
        #scrape the movie names
        name = con.find('h3').text
        names.append(name)
    
        #scrape the release_dates
        release_date = con.select('div.clamp-details span')[0].text
        release_dates.append(release_date)
    
        #scrape the ratings
        rating = con.select('div.clamp-details span')[1].text
        ratings.append(rating)
    
        #scrape the meta scores
        meta_score= con.select('a.metascore_anchor div')[0].text
        meta_scores.append(meta_score)
    
        #scrape the user scores.
        user_score = con.select('a.metascore_anchor div')[2].text
        user_scores.append(user_score)


import pandas as pd
movie_df = pd.DataFrame({'Movie_names': names,
'Release_dates': release_dates,
'Ratings': ratings,
'Meta_scores': meta_scores,
'User_scores': user_scores})
print(movie_df.info())
```

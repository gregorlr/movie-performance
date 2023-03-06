# movie-performance
prediction model for movie performances. Gr√©gorio Loiarro and Maxime Perraza 


import time
import subprocess
subprocess.run("pip install selenium", shell=True)
subprocess.run("brew install geckodriver", shell=True)
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver import Safari
from selenium.webdriver.support.select import Select
from selenium.webdriver.common.by import By
import pandas as pd
from bs4 import BeautifulSoup
import urllib3
import requests

# Initiate a browser instance
browser = webdriver.Safari()

# Go to the allociné.com search page
browser.get("https://www.allocine.fr/film/meilleurs/")

#cliquer sur j'accepte les cookies
browser.find_element(By.XPATH,'//*[@id="cmp-main"]/button[2]').click()
time.sleep(20)

# Find all movie elements on the page
movie_elements = browser.find_elements(By.XPATH, '//*[@id="content-layout"]/section[3]/div[2]/ol/li[@class="mdl"]')

list_of_movies = []
# Loop through all movie elements and print their ratings
for movie in movie_elements:
     
    stars_notes = movie.find_elements(By.XPATH, './/div[@class="rating-item-content"]//span')
    stars_name = movie.find_element(By.XPATH, './/h2/a').text
    star_rating = [star.text for star in stars_notes]
    
    if star_rating:
        tmp_dict = {'name':stars_name,
                    'note_press':star_rating[0],
                    'note_public': star_rating[1]}
        list_of_movies.append(tmp_dict)


# Close the browser
browser.quit()


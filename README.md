# movie-performance
prediction model for movie performances. Grégorio Loiarro and Maxime Perraza 

import time
import subprocess
subprocess.run("install selenium", shell=True)
subprocess.run("install geckodriver", shell=True)
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

# Go to the societe.com search page
browser.get("https://www.allocine.fr/film/meilleurs/")

#cliquer sur j'accepte les cookies
browser.find_element(By.XPATH,'//*[@id="cmp-main"]/button[2]').click()
time.sleep(20)


#on est maintenant sur la page des meilleurs films
 
stars notes = browser.find_element(By.XPATH,'//*[@id="content-layout"]/section[3]/div[2]/ol/li[1]/div/div[4]/div[1]/div/div').



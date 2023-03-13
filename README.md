# movie-performance
prediction model for movie performances. Gr√©gorio Loiarro and Maxime Perraza 


from bs4 import BeautifulSoup
import urllib3
import requests
import pandas as pd
from pathlib import Path
import json

### liste utilisées pour plus tard
liste_auteur = []
liste_note = []
liste_note_press = []
liste_note_spec = []
liste_titre = []
liste_budget = []
liste_of_all_movies = []
list_local = []

### création d'un fichier en 'local' pour garder les données dans le temps
chemin = Path.cwd()
with open(f"{chemin}\dataz.json", "w") as f:
    json.dump(list_local,f)

### Boucle sur les 30 pages 'meilleurs films' sur le site d'allocine
for j in range(30):
    url = f'https://www.allocine.fr/film/meilleurs/?page={j}'



    # Make a request to the website
    response = requests.get(url)


    # Parse the HTML content of the page
    soup = BeautifulSoup(response.content, "html.parser")

    # Find all the movie elements on the page
    movies_grade = soup.find_all('div', class_='stareval stareval-medium stareval-theme-default')
    movies_title = soup.find_all('div', class_ = 'card entity-card entity-card-list cf' )
    movies_real = soup.find_all('div', class_ = 'meta-body-item meta-body-direction')
    movie_page_indiv = soup.find_all("a", class_ = 'meta-title-link')

    # Loop through the movies and print their title
    for movie in movies_grade:
        stars = movie.find("span", class_='stareval-note').text
        if stars == "--":
            pass
        elif stars != '--':
            liste_note.append(stars)

    for movie in movies_title:
        name = movie.find("a", class_ = 'meta-title-link').text
        liste_titre.append(name)

    # Loop through the movies and print their real
    for movie in movies_real:
        soup_real_link = movie.find("a", class_ = 'blue-link')
        if soup_real_link == None:
            real = str(movie).split('blue-link">')
            real = real[1]
            real = real.split('</span>')
            real = real[0]

        else:
            real = str(soup_real_link).split('.html">')
            real = real[1]
            real = real.split('</a>')
            real = real[0]

        liste_auteur.append(real)

    ### recup le budget
        ###recup l'url de chaque page de film pour pouvoir récupérer leur budget sur leur page perso
    for indiv in movie_page_indiv:
        lien_indiv = str(indiv).split('href="')
        lien_indiv = lien_indiv[1].split('">')
        lien_indiv = lien_indiv[0]
        url2 = f"https://www.allocine.fr{lien_indiv}"

        ### RECUP DANS CHAQUE PAGE LE BUDGET
        response2 = requests.get(url2)
        soup2 = BeautifulSoup(response2.content, "html.parser")
        soup_movie_indiv = soup2.find_all("div", class_ = "item")
        budget_film = str(soup_movie_indiv).split('Budget')
        budget_film = str(budget_film[1]).split('$')
        budget_film = str(budget_film[0]).split('"that">')
        budget_film = budget_film[1]
        liste_budget.append(budget_film)

    ### trie des notes des spectateurs et de la presse
    for i in range(len(liste_note)):
        note = liste_note[i]
        if i%2 == 0:
            liste_note_press.append(note)
        elif i%2 != 0:
            liste_note_spec.append(note)

    ### ici on met tout les dictionnaires dans une liste
    for j in range(len(liste_titre)):
        tmp_dict = dict()
        tmp_dict.update({"nom du film" : liste_titre[j]})
        tmp_dict.update({"realisateur": liste_auteur[j-1]})
        tmp_dict.update({"note des spectateurs": liste_note_spec[j]})
        tmp_dict.update({"note de la presse": liste_note_press[j]})
        tmp_dict.update({"Budget": liste_budget[j]})
        liste_of_all_movies.append(tmp_dict)

### cette boucle sert à stocker les films dans le fichier enregistrable en faisant attention à ce que le film n'y soit pas déjà
for film in liste_of_all_movies:
    with open("dataz.json", "r") as f:
        list_json = json.load(f)
        if film in list_json:
            pass
        elif film not in list_json:
            with open("dataz.json", "r") as f:
                film_to = json.load(f)
            film_to.append(film)
            with open("dataz.json", "w") as f:
                json.dump(film_to, f)




liste_des_film_VRAI = []

### les dernières lignes servent à ensuite mettre l'ensemble des films récoltés "au cours du temps" depuis le json en local dans une liste
with open("dataz.json", "r") as f:
    list_json2 = json.load(f)
    for film in list_json2:
        liste_des_film_VRAI.append(film)

### à partir de la liste précédente on créer une dataframe puis on l'exporte au format csv pour pour pouvoir l'exploiter plus tard
    data = pd.DataFrame(liste_des_film_VRAI)

data.to_csv('data_film.csv', index = False)

req = urllib3.PoolManager()

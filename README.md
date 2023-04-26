#exploration du fichier des vélos de la ville de toulouse
# test-alternance-ELLIPSE
import requests
import folium
import webbrowser
# Clé d'API JCDecaux
api_key = 'e0a1bf2c844edb9084efc764c089dd748676cc14'

# URL de l'API JCDecaux
base_url = 'https://api.jcdecaux.com/vls/v3/'

# Paramètres de la requête API
params = {
    'apiKey': api_key,
    'contract': 'toulouse' # contrat de location de vélos
}

# Récupération des données sur les stations de vélos en libre-service
stations_url = base_url + 'stations'
response = requests.get(stations_url, params=params)
stations_data = response.json()
#création d'une carte centrée sur toulouse métropole
m = folium.Map(location=[stations_data[0]["position"]['latitude'], stations_data[0]["position"]['longitude']], zoom_start=12)
#pour chaque i qui va servir d'indice de la liste des stations de vélos
#on récupère les informations nécéssaires
for i in range(len(stations_data)):
    print(stations_data[i])
    nom_station=stations_data[i]["name"]
    lat=stations_data[i]["position"]['latitude']
    lng=stations_data[i]["position"]['longitude']
    velo_dispo=stations_data[i]["totalStands"]["availabilities"]["bikes"]

    velo_meca=stations_data[i]["totalStands"]["availabilities"]["mechanicalBikes"]
    velo_elec=stations_data[i]["totalStands"]["availabilities"]["electricalBikes"]
    if velo_dispo !=0:
        pourcent_velo_elec=velo_elec/velo_dispo
        pourcent_velo_meca=velo_meca/velo_dispo
    else:
        pourcent_velo_elec=""
        pourcent_velo_meca=""
    #pour chaque stations on fait une phrase qui décrit la station
    phrase="station "+str(nom_station)+"\nvélos disponibles: "+str(velo_dispo)+"\nvélos mécaniques: "+str(velo_meca)+"\nvélos électriques: "+str(velo_elec)
    #on met un marqueur sur chaque station pour voir où elle se trouve géographiquement et on annote avec la phrase qui décrit cette station
    folium.Marker(location=[lat, lng], popup=phrase).add_to(m)
    print(phrase)
#on sauvegarde le fichier html créé

m.save("carte.html")
#on ouvre le fichier html 
webbrowser.open("carte.html")
        




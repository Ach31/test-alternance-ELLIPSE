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
m = folium.Map(location=[stations_data[0]["position"]['latitude'], stations_data[0]["position"]['longitude']], zoom_start=12)
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
    phrase="station "+str(nom_station)+"\nvélos disponibles: "+str(velo_dispo)+"\nvélos mécaniques: "+str(velo_meca)+"\nvélos électriques: "+str(velo_elec)

    """
    velo_meca=stations_data[i]["totalStands"]["availabilities"]["mechanicalBikes"]
    pourcent_velo_meca=str(velo_meca/velo_dispo*100)+"%"
    velo_elec=stations_data[i]["totalStands"]["availabilities"]["electricalBikes"]
    pourcent_velo_elec=str(velo_elec/velo_dispo*100)+"%"
    phrase="station "+str(nom_station)+"\nvélos disponibles: "+str(velo_dispo)+"\nvélos mécaniques: "+str(velo_meca)+"- pourcentage: "+pourcent_velo_meca+"\nvélos électriques: "+str(velo_elec)+"- pourcentage: "+pourcent_velo_elec
    """
    folium.Marker(location=[lat, lng], popup=phrase).add_to(m)
    print(phrase)
m.save("carte.html")
webbrowser.open("carte.html")
        
"""
# Extraction des informations intéressantes
total_bikes = 0
total_electric_bikes = 0
city_counts = {}

for station in stations_data:
    city = station['Name'].split(',')[1].strip()
    bike_count = station['mainStands']['availabilities']['bikes']
    electric_bike_count = station['mainStands']['availabilities']['electricBikes']
    
    # Mise à jour des compteurs
    total_bikes += bike_count
    total_electric_bikes += electric_bike_count
    
    if city not in city_counts:
        city_counts[city] = {'total_bikes': 0, 'total_electric_bikes': 0}
    
    city_counts[city]['total_bikes'] += bike_count
    city_counts[city]['total_electric_bikes'] += electric_bike_count

# Calcul du pourcentage de vélos électriques par ville
city_percentages = {}

for city, counts in city_counts.items():
    total = counts['total_bikes'] + counts['total_electric_bikes']
    
    if total > 0:
        percentage = counts['total_electric_bikes'] / total * 100
    else:
        percentage = 0
    
    city_percentages[city] = percentage

# Affichage des résultats
print('Total bikes:', total_bikes)
print('Total electric bikes:', total_electric_bikes)
print('Electric bike percentage by city:')
for city, percentage in sorted(city_percentages.items(), key=lambda x: x[1], reverse=True):
    print(city, ':', round(percentage, 2), '%')
"""

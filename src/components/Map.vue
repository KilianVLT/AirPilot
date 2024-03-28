<template>
    <div id="map" ref="mapContainer"></div>
</template>
  
<script>
    import { ref, onMounted } from 'vue';
    import L from 'leaflet';

    import 'leaflet/dist/leaflet.css';
    import 'leaflet.markercluster/dist/MarkerCluster.css';
    import 'leaflet.markercluster/dist/MarkerCluster.Default.css';
    import 'leaflet.markercluster/dist/leaflet.markercluster.js';
    import 'leaflet-rotatedmarker/leaflet.rotatedMarker.js';

    export default {
        name: "Map",
        setup(props, context){
            const mapContainer = ref(null);
            let flights = ref({})
            const url = 'https:{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png'
            const attribution = '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors'

            async function getFlights(){

                try{
                    const response = await fetch("https://opensky-network.org/api/states/all", {
                        method: "GET"
                    });

                    if(!response.ok){
                        throw new Error('Erreur lors de la récupération des données de vols.');
                    }

                    flights.value = await response.json();
                }
                catch(error){
                    console.error('Erreur lors de la récupération des données de vols:', error);
                    return [];
                }

            }

            onMounted(async ()=> {

                await getFlights()
                console.log(flights.value);

                let planeIcon = L.icon({
                    iconUrl: "/src/assets/icons/plane.png",
                    iconSize: [30, 30],
                });

                const markersGroup = L.markerClusterGroup({
                    spiderfyOnMaxZoom: true,
                    showCoverageOnHover: false,
                    zoomToBoundsOnClick: true,
                    chunkedLoading: true
                });

                let map = L.map(mapContainer.value).setView([30.50, -0.09], 3)
                L.tileLayer(url, {
                    attribution: attribution,
                    maxZoom: 18,
                    minZoom: 3,
                }).addTo(map);

                const states = flights.value.states
                let i = 0 
                states.forEach(state => {
                    if(state[6] && state[5]){
                        let marker = L.marker([state[6], state[5]], {icon: planeIcon, rotationAngle: state[10]})
                        marker.bindPopup("En provenance de " + state[2])
                        markersGroup.addLayer(marker)
                        marker.addEventListener('click', () => {
                            context.emit("flight", state)
                        })
                    }
                });
                
                map.addLayer(markersGroup);
            })

            return {
                mapContainer,
            };
        }
    }
</script>

<style scoped>
    #map {
        height: 100vh!important;
        width: 100vw!important;
    }
</style>
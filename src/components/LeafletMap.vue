<template>
    <div>
        <div id="map"></div>
        <button @click="changeLocation">Change Location</button>
    </div>
</template>

<script>
import L from 'leaflet'

export default {
    data() {
        return {
            map: '',
            viewCoordArr: [[51.505, -0.09], [22.286, 114.150], [52.520, 13.405], [13.737, 100.523]],
            cycleCounter: 0
        }
    },
    methods: {
        addMapTiles() {
            this.map.setView([51.505, -0.09], 15)
            L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token={accessToken}', {
                attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
                maxZoom: 18,
                id: 'mapbox.streets',
                accessToken: 'pk.eyJ1IjoiYXJ0emUiLCJhIjoiY2psNWdxZ3g1MHA5dDN4cGJ4Ym93Mnl6YyJ9.OJ2PstZSXAnFJa5UMqwS5w'
            }).addTo(this.map)
        },
        changeLocation() {
            if(this.cycleCounter === this.viewCoordArr.length - 1) {
                this.cycleCounter = 0
            } else {
                this.cycleCounter++
            }
            this.map.setView(this.viewCoordArr[this.cycleCounter], 15)
            this.addCirclesAndPolyline()
        },
        addCirclesAndPolyline() {
            this.map.eachLayer((layer) => {
                if(!(layer instanceof L.TileLayer)) {
                    this.map.removeLayer(layer)
                }
            })
            let markerCoordArr = []
            let lat = this.viewCoordArr[this.cycleCounter][0]
            let lon = this.viewCoordArr[this.cycleCounter][1]
            for(let i = 0; i < 1000; i++) {
                lat += 0.001
                lon += 0.001
                markerCoordArr.push([lat, lon])
            }
            L.polyline(markerCoordArr, {color: 'blue'})
                .setStyle({weight: 12})
                .addTo(this.map)
            markerCoordArr.forEach((coord) => {
                L.circle(coord, {
                    radius: 4,
                    weight: 1,
                    color: '#000000',
                    fillColor: '#EEEEEE',
                    fillOpacity: 1
                })
                    .addTo(this.map)
            })
        }
    },
    mounted() {
        this.map = L.map('map')
        this.addMapTiles()
    }
}
</script>

<style scoped>
    #map{
        height: 500px;
        width: auto;
    }
</style>

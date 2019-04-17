<template>
    <div class="all">
            <button class="all__btn" @click="toggleCountries">display all</button>
        <div class="all__feeds feeds">
            <div class="feeds__countries" v-for="item in location">
                <button :data-lat="item.latitude" @click="selectCountry">
                    {{item.name}}
                </button>
            </div>
        </div>
        <div class="map" id="map"></div>
    </div>
</template>

<script>
export default {
    name: 'Map',
    data() {
        return {
            map: null,
            tileLayer: null,
            location: [],
            locations:  [],
            markers: [],
            ids: [],
            latitude: null,
        }    
    },

    created() {
         // fetch data from JSON and push new locations into locations array so when the app is created 
        // location with longitudes and latitudes is updated with values and names 
        fetch("http://s3-eu-west-1.amazonaws.com/omnifi/techtests/locations.json")
            .then(response => response.json())
            .then((data) => {
            this.location = data;
            
             for (var i = 0; i < this.location.length; i++) {
                 // get name, longitude and latitude from JSON
                 this.name = this.location[i].name
                 this.longitude = this.location[i].longitude
                 this.latitude = this.location[i].latitude
                //  console.log(this.latitude)

                // push new array into the location array -> according to leaftet.js pattern 
                // [['name', lon, lat]] so the markers can be displayed using leaftet.js func
                this.locations.push([this.name, this.latitude, this.longitude])
             }
            //  console.log(this.locations)
            this.initMap(); 
        });
    },

  methods: {
    initMap() {
        // console.log(this.locations)
        // set a map for Europe with zoom 5 
        this.map = L.map('map').setView([53.0000, 16.0000], 4.5);

        // map layer
        this.tileLayer = L.tileLayer(
            'https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png',
            {
            maxZoom: 18,
            attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; <a href="https://carto.com/attribution">CARTO</a>',
            }
        );

        // custom marker 
        this.icon = L.icon({
        iconUrl: 'http://www.myiconfinder.com/uploads/iconsets/256-256-7a195b78d9607a48fb234f98634fa5ea-pin.png',
        iconSize: [21, 34]
    });

        // set up markers
        this.marker = new L.Marker([17.385044, 78.486671]);
        this.tileLayer.addTo(this.map);
        // console.log(this.locations)

        // loop all countries and display their markers
        for (var i = 0; i < this.locations.length; i++) {
            this.ids.push(this.marker._leaflet_id)

            this.marker = new L.marker([this.locations[i][1],this.locations[i][2]], {icon: this.icon})
            .bindPopup('<span class="popup-txt">'+this.locations[i][0]+'</span>')
            .addTo(this.map).on('click', function(e) {
                console.log(e.latlng);
            });
            
            this.marker.openPopup()
            this.ids.push(this.marker._leaflet_id)

        }
    },
  
    toggleCountries(event) {
        event.target.parentNode.classList.toggle('is-active')
    },

    selectCountry(e) {
        console.log(Number(e.currentTarget.getAttribute('data-lat')));

        for (var i = 0; i < this.location.length; i++) {
            // console.log(this.location[i].latitude)

            // if latitude from the button is equal to the latitude from marker openPopup()
            if (parseInt(Number(e.currentTarget.getAttribute('data-lat'))) === this.location[i].latitude) {
                // console.log('match')

                console.log(this.location[i].latitude)

                this.location[i].openPopup() // <-------- FIX
            }
            else {
                // console.log('nothing')
            }
        }
    }
  },
}


</script>

<style lang="scss">
@import "../assets/_settings.scss";

.all {
    display: grid;

    @include lg {
        grid-template-columns: 150px auto;
    }

    &__feeds {
        display: none;

        @include lg {
            display: flex;
            flex-direction: column;
        }
    }

     &__btn {
        &:after {
            content: '\f107';
            font-family: "Font Awesome 5 Free";
            font-size: 15px;
            margin-left: 10px;
            transition: .3s;
        }

        text-transform: uppercase;
        width: 100%;
        height: 40px;
        font-weight: bold;
        position: relative;
        display: inline-flex;
        align-items: center;
        justify-content: center;    

        @include lg {
            display: none;
        }
    }

    &.is-active {
        .all {
            &__btn {
                background: #e5e6e8;

                &:after {
                    transform: rotate(180deg);
                }
            }

            &__feeds {
                display: flex;
                flex-direction: column;

                @include md {
                    display: grid;
                    grid-template-columns: repeat(3, 1fr);
                }

                button {
                    padding: 10px 40px;
                }
            }
        }
    }
}

.feeds {
        @include lg {
            height: 100vh;
            overflow: scroll;
        }

        &__countries {
            width: 100%;
        }

        button {
            font-weight: bold;
            padding: 10px;
            line-height: 18px;
            width: 100%;

            @include lg {
                padding: 20px;
            }
        
        }
    }

.show-more {
    &::after {
        content: '\f107';
        font-family: "Font Awesome  Free";
        font-size: 15px;
        margin-left: 10px;
        transition: .3s;
    }

    text-transform: uppercase;
}
   .map {
       height: 100vh;
       width: 100vw;
   }

   .leaflet-popup-content {
        text-transform: uppercase;
        font-family: 'Montserrat', sans-serif;
        font-weight: bold;
   }

   .leaflet-marker-icon{
       height: 24px !important;
       width: 24px !important;
   }    

</style>



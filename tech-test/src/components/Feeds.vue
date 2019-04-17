<template>
<div class="all">
    <button class="all__btn" @click="test">display all</button>
    <div class="all__feeds feeds">
        <div class="feeds__countries" v-for="item in countries" :key="item.longitude">
            <button data-dep="xxx" @click="selectCountry">
                {{item}}
            </button>
        </div>
    </div>
</div>
</template>

<script>
export default {
    name: 'Feeds',
    data() {
        return {
            countries: []
        }
    },

    created() {
        // fetch data from JSON and push new locations into locations array so when the app is created 
        // location with longitudes and latitudes is updated with values and names 
        fetch("http://s3-eu-west-1.amazonaws.com/omnifi/techtests/locations.json")
            .then(response => response.json())
            .then((data) => {
            this.country = data;
            
             for (var i = 0; i < this.country.length; i++) {
                 this.countries.push(this.country[i].name)
                // console.log(this.country[i].name)            
             }

            //  console.log(this.countries);
        });
    },
    methods: {
        test(event) {
             event.target.parentNode.classList.toggle('is-active')
        },

        selectCountry() {
             this.markerFunction(info[e.currentTarget.getAttribute('data-dep')].latitude);
        }
    }
}
</script>

<style lang="scss">
@import "../assets/_settings.scss";

.all {

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

    &__feeds {
        display: none;

        @include lg {
            display: flex;
            flex-direction: column;
            background: #ddd;
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
</style>

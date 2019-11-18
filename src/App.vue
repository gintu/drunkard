<template>
  <div id='App'>
<template>
    <b-navbar >
        <template slot="brand">
            <b-navbar-item tag="router-link" :to="{ path: '/' }">
                <h1><strong>drunkard</strong></h1>
            </b-navbar-item>
        </template>


        <template slot="end">
            <b-navbar-item tag="div">
               <b-navbar-item href="#">
                About
            </b-navbar-item>
            </b-navbar-item>
        </template>
    </b-navbar>
</template>
<template>
 <b-tabs position="is-centered" class="block">
            <b-tab-item label="Generate a random drink">
              <div class="container">
                <template>
                  <section>
                      <b-button class="is-primary is-large" @click="generate" :loading='loading'>
                        Generate
                        </b-button>
                  </section>
              </template>
              <template>
                <div class="notification has-margin-top-20" v-if="isGenerated">
                  <div class="columns">

                  <div class="column ">
                    <h1 class="title">
                        {{drink.strDrink}}
                        </h1>
                        <h2 class="subtitle">
                        {{drink.strAlcoholic}}
                        </h2>
                   <p> {{drink.strInstructions}}</p>
                   </div>
                   <div class="column">
                  <img   :src="`${drink.strDrinkThumb}`"/>
                  </div>
                  </div>

                </div>
              </template>
              </div>
            </b-tab-item>
            <b-tab-item label="Find Details about a drink">


 <div class="container">
 <template>
    <section>


        <b-field label="Find details about a drink">
            <b-autocomplete
                :data="data"
                placeholder="e.g. Vodka"
                field="title"
                :loading="isFetching"
                @typing="getAsyncData">


                <template slot-scope="props">
                    <div class="media">
                        <div class="media-content">
                            {{ props.option.strType }}
                            <br>
                        </div>
                    </div>
                </template>
            </b-autocomplete>
        </b-field>
    </section>
</template>
<template>
  <div class="notification has-margin-top-50" v-if="isContent">
    <h1 class="title">
      {{data[0].strType}}</h1>
      <h2 class="subtitle">
      {{data.strAlcohol == 'Yes' ? `Alcoholic (ABV ${data.strABV}%)`:'Non Alcoholic'}}
      </h2>
      <p>{{data[0].strDescription}}</p>

  </div>
</template>
</div>


            </b-tab-item>
        </b-tabs>
</template>


  </div>
</template>

<style lang="scss">
// Import Bulma's core
@import "~bulma/sass/utilities/_all";
@import url('https://fonts.googleapis.com/css?family=Lexend+Exa&display=swap');
// Set your colors
$primary: #be7575;
$primary-invert: findColorInvert($primary);
$twitter: #4099FF;
$twitter-invert: findColorInvert($twitter);

// Setup $colors to use as bulma classes (e.g. 'is-twitter')
$colors: (
    "white": ($white, $black),
    "black": ($black, $white),
    "light": ($light, $light-invert),
    "dark": ($dark, $dark-invert),
    "primary": ($primary, $primary-invert),
    "info": ($info, $info-invert),
    "success": ($success, $success-invert),
    "warning": ($warning, $warning-invert),
    "danger": ($danger, $danger-invert),
    "twitter": ($twitter, $twitter-invert)
);

$family-primary:"Lexend Exa","Roboto";
// Links
$link: $primary;
$link-invert: $primary-invert;
$link-focus-border: $primary;

// Import Bulma and Buefy styles
@import "~bulma";
@import "~buefy/src/scss/buefy";

</style>
<script>
import debounce from 'lodash/debounce';
import axios from 'axios';

export default {
  data() {
    return {
      data: [],
      drink: {},
      selected: null,
      isFetching: false,
      isContent: false,
      isGenerated: false,
      loading: false,
    };
  },
  methods: {
    // You have to install and import debounce to use it,
    // it's not mandatory though.
    getAsyncData: debounce(function (name) {
      if (!name.length) {
        this.data = [];
        this.isContent = false;
        return;
      }
      this.isFetching = true;
      axios({
        method: 'get',
        url: 'https://the-cocktail-db.p.rapidapi.com/search.php',
        headers: {
          'x-rapidapi-host': 'the-cocktail-db.p.rapidapi.com',
          'x-rapidapi-key': 'f8d6460f15msh57ae6ecec56ef71p1352c1jsn470f527ed1e2',
        },
        params: {
          i: `${name}`,
        },
      })
        .then((response) => {
          this.data = [];
          // response.data.ingredients.forEach(item => this.data.push(item));
          const [ingredients] = response.data.ingredients;
          this.data.push(ingredients);
          this.isContent = true;
          console.log(this.data);
        })
        .catch((error) => {
          this.data = [];
          throw error;
        })
        .finally(() => {
          this.isFetching = false;
        });
    }, 500),

    generate() {
      this.loading = true;
      axios({
        method: 'get',
        url: 'https://the-cocktail-db.p.rapidapi.com/random.php',
        headers: {
          'x-rapidapi-host': 'the-cocktail-db.p.rapidapi.com',
          'x-rapidapi-key': 'f8d6460f15msh57ae6ecec56ef71p1352c1jsn470f527ed1e2',
        },
      })
        .then((response) => {
          this.drink = {};
          console.log(response);
          this.isGenerated = true;
          [this.drink] = [...response.data.drinks];
        })
        .catch((error) => {
          this.drink = {};
          throw error;
        })
        .finally(() => {
          this.loading = false;
          this.isFetching = false;
        });
    },
  },
};
</script>

<template>
  <v-container grid-list-md fluid>
    <v-layout wrap>
      <v-flex xs12 sm4 md3 v-for="pet in dogs" :key="pet.breed">
        <appDog :dog="pet" @addToFavorites="addToFavorites"></appDog>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<style scoped>
p {
  margin: 0;
}
</style>
<script>
import Dog from "../components/Dog.vue";
import { Dogs } from "../data/dogs";
import axios from "axios";
axios.defaults.baseURL = "https://dog.ceo/api";
import { mapActions } from "vuex";

export default {
  components: {
    appDog: Dog
  },
  data() {
    return {
      dogs: Dogs
    };
  },
  methods: {
    ...mapActions(["addToFavorites"])
  },
  created() {
    this.dogs.forEach(dog => {
      dog.img = "";
    });
    const linksArray = this.dogs.map(
      dog => "/breed/" + dog.breed + "/images/random"
    );
    axios.all(linksArray.map(link => axios.get(link))).then(
      axios.spread((...res) => {
        this.dogs.forEach((dog, index) => {
          dog.img = res[index].data.message;
        });
      })
    );
  }
};
</script>
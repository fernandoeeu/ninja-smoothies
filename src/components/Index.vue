<template>
  <div class="index container">
    <div class="card" v-for="smoothie in smoothies" :key="smoothie.id">
      <div class="card-content">
        <i @click="removeSmoothie(smoothie.id)" class="material-icons delete">delete</i>
        <h2 class="indigo-text">{{ smoothie.title }}</h2>
        <ul class="ingredients">
          <li v-for="(ing, index) in smoothie.ingredients" :key="index">
            <span class="chip">{{ ing }}</span>
          </li>
        </ul>
      </div>
      <span class="btn-floating btn-large halfway-fab pink">
        <router-link :to="{ name: 'EditSmoothie', params: {smoothie_slug: smoothie.slug} }">
          <i class="material-icons edit">edit</i>
        </router-link>
      </span>
    </div>
  </div>
</template>

<script>
import db from '@/firebase/init'
import { mapMutations } from 'vuex'

export default {
  name: "Index",
  data() {
    return {

    }
  },
  methods: {
    ...mapMutations(["ADD_SMOOTHIE", "REMOVE_SMOOTHIE", "CLEAR_SMOOTHIE"]),
    clearSmoothie() {
      this.CLEAR_SMOOTHIE()
    },
    addSmoothie: function(smoothie) {
      this.ADD_SMOOTHIE(smoothie)
    },
    removeSmoothie(id) {
      this.REMOVE_SMOOTHIE(id)
    },
  },
  computed: {
    smoothies() {
      return this.$store.getters.smoothies
    }
  },
  created() {
    // fetch data from firestore
    this.clearSmoothie()
    db.collection('smoothies').get()
    .then(snapshot => {
      snapshot.forEach(doc => {
        let smoothie = doc.data()
        smoothie.id = doc.id
        this.addSmoothie(smoothie)
      })
    })
  }
}
</script>

<style>
.index {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 30px;
  margin-top: 60px;
}
.index h2 {
  font-size: 1.8em;
  text-align: center;
  margin-top: 0;
}
.index .ingredients {
  margin: 30px auto;
}

.index .ingredients li {
  display: inline-block;
}

.index .delete {
  position: absolute;
  top: 4px;
  right: 4px;
  cursor: pointer;
  color: #AAA;
  font-size: 1.4em;
}

.index .edit {
  font-size: 1.4em;
  cursor: pointer;
}
</style>

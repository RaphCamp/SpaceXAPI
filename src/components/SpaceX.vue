<script setup>
import { ref,watch } from 'vue'
import axios from 'axios'

defineProps({
})

var nextLaunch = ref(null)
nextLaunch.value = null
axios.get('https://api.spacexdata.com/v4/launches/next').then((reponse) => {nextLaunch.value = reponse.data;
console.log(nextLaunch.value.links.webcast)
})
var pastLaunches = ref(null)
axios.get('https://api.spacexdata.com/v4/launches/past').then((reponse) => {pastLaunches.value = reponse.data.reverse().slice(0,10);
console.log(pastLaunches.value);
})

const decompte = ref(null)
function ComputeDate() {
  decompte.value = Math.round(((nextLaunch.value.date_unix*1000)-Date.now())/1000)
}
setInterval(ComputeDate,1000) 

watch(decompte, ComputeDate)

</script>

<template>
  <h1>SpaceX lancements</h1>

  <div v-if="nextLaunch">
    <h2>Prochain lancement</h2>
    <p>Date : {{nextLaunch.date_utc}}</p>
    <p>Nom : {{nextLaunch.name}}</p>
    <p v-if="decompte">Lancement dans : {{decompte}} secondes</p>
    <p v-else>chargement...</p>
  </div>

  <div>
    <select name="filterlaunch">
      <option value="tous">Tous les lancements</option>
      <option value="succes">Lancements réussis</option>
      <option value="fails">Lancements échoués</option>
    </select>
  </div>
  
  <h2>Les 10 derniers lancements :</h2>
  <div id="liste">
    <div :key="index" v-for="(pastLaunch,index) in pastLaunches">
      <h3>Nom : {{pastLaunch.name}}</h3>
      <p>date : {{pastLaunch.date_utc}}</p>
      <p>détail : {{pastLaunch.details}}</p>
      <p>image : {{pastLaunch.name}}</p>
      <img v-bind:src="pastLaunch.links.patch.small" alt="image" />
      <p><a v-bind:href="pastLaunch.links.article">article</a></p>
      <p><a v-bind:href="pastLaunch.links.webcast">vidéo Youtube</a></p>
      <p>launchpad : {{pastLaunch.launchpad}}</p>
      <p>chargements envoyés : {{pastLaunch.payloads}}</p>
      <p>client : pas moi</p>
    </div>
  </div>
</template>

<style scoped>
#liste{
  display: flex;
  flex-wrap: wrap;
}
#liste>div{
  padding:50px;
  width:20%;
  min-width: 200px;
  border: 1px white solid;
  overflow: hidden
}
</style>

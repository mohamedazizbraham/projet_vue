<template>
  <div class="container">
    <h2>Enregistrer une participation</h2>
    <form @submit.prevent="ajouterParticipation">
      <div class="form-group">
        <label for="personne">Personne :</label>
        <select id="personne" v-model="data.formulaire.personneId">
          <option v-for="personne in data.personnes" :key="personne.id" :value="personne.id">
            {{ personne.nom }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="projet">Projet :</label>
        <select id="projet" v-model="data.formulaire.projetId">
          <option v-for="projet in data.projets" :key="projet.id" :value="projet.id">
            {{ projet.nom }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="role">Rôle :</label>
        <input id="role" type="text" v-model="data.formulaire.role" />
      </div>

      <div class="form-group">
        <label for="pourcentage">Pourcentage :</label>
        <input id="pourcentage" type="range" v-model="data.formulaire.pourcentage" min="0" max="100" />
        <span>{{ data.formulaire.pourcentage }}%</span>
      </div>

      <button type="submit">Enregistrer</button>
    </form>
  </div>
</template>

<script setup>
import { onMounted, reactive } from "vue";
import doAjaxRequest from "@/util/util.js";

const participationVide = {
  personneId: "",
  projetId: "",
  role: "",
  pourcentage: 50,
};

let data = reactive({
  formulaire: { ...participationVide },
  personnes: [],
  projets: [],
});

function ajouterParticipation() {
  const options = {
    method: "POST",
    body: JSON.stringify(data.formulaire),
    headers: { "Content-Type": "application/json" },
  };

  doAjaxRequest("/api/participations", options)
    .then(() => {
      data.formulaire = { ...participationVide };
      alert("Participation enregistrée !");
    })
    .catch(error => alert(error.message));
}

function refresh() {
  doAjaxRequest("/api/personnes").then(result => (data.personnes = result._embedded.personnes));
  doAjaxRequest("/api/projets").then(result => (data.projets = result._embedded.projets));
}

onMounted(refresh);
</script>

<style scoped>
.container {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 10px;
}
.form-group {
  margin-bottom: 15px;
}
button {
  background-color: blue;
  color: white;
  padding: 10px;
  border: none;
  cursor: pointer;
}
</style>

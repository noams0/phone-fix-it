<template>
  <div>
    <div @click="toggleForm" class="repair-banner">Je répare mon smartphone</div>
    <div v-if="showForm" class="repair-form">
      <!-- Formulaire de réparation -->
      <form @submit.prevent="submitForm">
        <label for="brand">Marque :</label>
        <select v-model="selectedBrand" id="brand">
          <option value="">Sélectionner une marque</option>
          <option v-for="brand in brands" :key="brand" :value="brand">{{ brand }}</option>
        </select>

        <label v-if="selectedBrand" for="model">Modèle :</label>
        <select v-model="selectedModel" id="model" v-if="selectedBrand">
          <option value="">Sélectionner un modèle</option>
          <option v-for="model in models" :key="model" :value="model">{{ model }}</option>
        </select>

        <label  v-if="selectedModel" for="component">Composant :</label>
        <select v-model="selectedComponent" id="component" v-if="selectedModel">
          <option value="">Sélectionner un composant</option>
          <option v-for="component in components" :key="component" :value="component">{{ component }}</option>
        </select>
        <button type="submit">Rechercher</button>
      </form>
      <div v-if="price">
        <p>Prix estimé : {{ price }} €</p>
        <p><a :href="tutorialLink" target="_blank">Tutoriel de réparation</a></p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import smartphonesData from "@/data/smartphones.json";

const showForm = ref(false);
const selectedBrand = ref('');
const selectedModel = ref('');
const selectedComponent = ref('');
const price = ref(null);
const tutorialLink = ref(null);

const brands = Object.keys(smartphonesData[0]);

// Fonction pour obtenir les modèles associés à une marque sélectionnée
const getModelsForBrand = (brand) => {
  return Object.keys(smartphonesData[0][brand][0]);
};

// Fonction pour obtenir les composants associés à un modèle sélectionné
// Fonction pour obtenir les composants associés à un modèle sélectionné
const getComponentsForModel = (brand, model) => {
  if (smartphonesData[0][brand][0][model]) {
    const components = [];
    for (const componentObj of smartphonesData[0][brand][0][model]) {
      const componentKeys = Object.keys(componentObj);
      components.push(...componentKeys);
    }
    return components;
  } else {
    return [];
  }
};

const models = ref([]);
const components = ref([]);

// Mettre à jour les modèles en fonction de la marque sélectionnée
watch(selectedBrand, (newBrand) => {
  models.value = getModelsForBrand(newBrand);
});

// Mettre à jour les composants en fonction du modèle sélectionné
watch(selectedModel, (newModel) => {
  components.value = getComponentsForModel(selectedBrand.value, newModel);
});
function toggleForm() {
  showForm.value = !showForm.value;
}

function submitForm() {
  if (smartphonesData[0][selectedBrand.value][0][selectedModel.value]) {
    for (const componentObj of smartphonesData[0][selectedBrand.value][0][selectedModel.value]) {
      for (const key in componentObj) {
        if (key === selectedComponent.value) {
          console.log(componentObj[key].Prix);
          price.value = componentObj[key].Prix;
          tutorialLink.value = componentObj[key].Tuto
          return;
        }
      }
    }
  }
}



</script>

<style scoped lang="scss">
@import "@/assets/main.css";
form {
  display: flex;
  flex-direction: column;
  justify-content: center; /* Centrage vertical */
  align-items: center; /* Centrage horizontal */
  gap: 10px;
}
  fieldset {
    width: 800px;
    border-radius: 5px; /* Coins arrondis */
    margin-bottom: 30px; /* Marge en bas entre les fieldsets */
    padding: 10px 25px; /* Espacement à l'intérieur des fieldsets */
    flex: 1;
    background-color:white ;
    border: none;
    /* Stylisation des légendes */
    legend {
      font-weight: bolder; /* Texte en gras */
      font-size: x-large;
    }
}
.repair-banner {
  background-color: var(--green-forfait);
  color: var(--text-color);
  padding: 10px;
  cursor: pointer;
}

.repair-form {
  margin-top: 20px;
  padding: 20px;
  border: 1px solid var(--text-color);
}
</style>

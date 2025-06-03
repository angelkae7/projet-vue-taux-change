<template>

  <div>
    <div class="container">
      <img src="@/assets/logo.png" alt="Logo" width="100" />
      <div class="title">
        <h1>Bureau de change</h1>
        <p>Exchange office</p>
      </div>

      <div class="montant-container">
        <div class="montant-xpf">
          Montant {{ amountXPF }} francs XPF
        </div>
      </div>
    </div>

    <div class="en-tete">
      <label for="xpf-amount">Montant en XPF : </label>
      <input type="number" id="xpf-amount" v-model.number="amountXPF" min="0" placeholder="Entrez un montant en XPF" />
    </div>

    <div class="right-column">
      <!-- Affichage pour mobile (une seule colonne) -->
      <div v-if="isMobile">
        <div class="currency-card-th">
          <div>
            <p>Drapeau</p>
            <h4>Pays</h4>
            <p>Taux de change</p>
            <p>Montant converti</p>
          </div>
        </div>
        <div class="currency-card" v-for="(rate, currency) in filteredConversionRates" :key="currency">
          <img :src="getFlagURL(currency)" :alt="`Flag of ${currency}`">
          <div class="carte">
            <div class="data-carte">
              <h3>{{ currency }}</h3>
              <p> {{ rate }}</p>
              <p> {{ (rate * amountXPF) }}</p>
            </div>
            <span>Mise à jour {{ lastUpdated }}</span>
          </div>
        </div>
      </div>
      <!-- Affichage pour desktop (deux colonnes) -->
      <div v-else class="columns-container">
        <div class="column">
          <div class="currency-card-th">
            <div>
              <p>Drapeau</p>
              <h4>Pays</h4>
              <p>Taux de change</p>
              <p>Montant converti</p>
            </div>
          </div>
          <div class="currency-card" v-for="(rate, currency) in column1Currencies" :key="currency">
            <img :src="getFlagURL(currency)" :alt="`Flag of ${currency}`">
            <div class="carte">
              <div class="data-carte">
                <h3>{{ currency }}</h3>
                <p> {{ rate }}</p>
                <p> {{ (rate * amountXPF) }}</p>
              </div>
              <span>Mise à jour {{ lastUpdated }}</span>
            </div>
          </div>
        </div>
        <div class="column">
          <div class="currency-card-th">
            <div>
              <p>Drapeau</p>
              <h4>Pays</h4>
              <p>Taux de change</p>
              <p>Montant converti</p>
            </div>
          </div>
          <div class="currency-card" v-for="(rate, currency) in column2Currencies" :key="currency">
            <img :src="getFlagURL(currency)" :alt="`Flag of ${currency}`">
            <div class="carte">
              <div class="data-carte">
                <h3>{{ currency }}</h3>
                <p> {{ rate }}</p>
                <p> {{ (rate * amountXPF) }}</p>
              </div>
              <span>Mise à jour {{ lastUpdated }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// Import des fonctions nécessaires de Vue.js pour la Composition API
// ref: https://vuejs.org/api/reactivity-core.html#ref
import { ref, onMounted, onBeforeUnmount } from 'vue';

// Définition des props que le composant reçoit du parent
// defineProps: https://vuejs.org/api/sfc-script-setup.html#defineprops
const props = defineProps({
  msg: String // Prop pour le message d'accueil
});

// Déclaration des variables réactives avec ref()
const amountXPF = ref(1); // Montant en francs XPF, valeur initiale à 1
const conversionRates = ref({}); // Objet contenant les taux de conversion
const filteredConversionRates = ref({}); // Objet contenant les taux de conversion filtrés
const lastUpdated = ref(new Date().toLocaleString()); // Date de la dernière mise à jour, formatée en string
const isMobile = ref(false); // Booléen indiquant si l'écran est de type mobile
const column1Currencies = ref({}); // Objet contenant les devises de la colonne 1
const column2Currencies = ref({}); // Objet contenant les devises de la colonne 2

// Fonction asynchrone pour récupérer les taux de change depuis l'API
const fetchExchangeRates = () => {
  // Utilisation de l'API exchangerate-api.com pour obtenir les taux de change
  // fetch API: https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch
  fetch('https://v6.exchangerate-api.com/v6/1520648efe11018bdeeeb7cd/latest/XPF')
    .then(response => response.json()) // Conversion de la réponse en JSON
    .then(data => {
      const allRates = data.conversion_rates; // Extraction des taux de conversion
      // Sélection des devises à afficher
      const selectedCurrencies = {
        AUD: allRates.AUD,
        NZD: allRates.NZD,
        CAD: allRates.CAD,
        USD: allRates.USD,
        FJD: allRates.FJD,
        SGD: allRates.SGD,
        THB: allRates.THB,
        CHF: allRates.CHF,
        EUR: allRates.EUR,
        GBP: allRates.GBP,
        JPY: allRates.JPY,
        VUV: allRates.VUV
      };
      conversionRates.value = allRates; // Mise à jour des taux de conversion
      filteredConversionRates.value = selectedCurrencies; // Mise à jour des taux de conversion filtrés
      distributeCurrencies(); // Distribution des devises dans les colonnes
    })
    .catch(error => {
      console.error('Error fetching data:', error); // Gestion des erreurs
    });
};

// Fonction pour obtenir l'URL du drapeau d'un pays à partir du code de la devise
const getFlagURL = (currency) => {
  // Mapping des codes de devise aux codes de pays ISO 3166-1 alpha-2
  // ISO 3166-1 alpha-2: https://fr.wikipedia.org/wiki/ISO_3166-1_alpha-2
  const countryCodes = {
    AUD: 'au',
    NZD: 'nz',
    CAD: 'ca',
    USD: 'us',
    FJD: 'fj',
    SGD: 'sg',
    THB: 'th',
    CHF: 'ch',
    EUR: 'eu',
    GBP: 'gb',
    JPY: 'jp',
    VUV: 'vu'
  };
  const countryCode = countryCodes[currency]; // Récupération du code de pays
  if (countryCode) {
    return `https://flagcdn.com/144x108/${countryCode}.png`; // Retourne l'URL du drapeau
  } else {
    return ''; // Retourne une chaîne vide si le code de pays n'est pas trouvé
  }
};

// Fonction pour déterminer si l'écran est de type mobile
const checkIfMobile = () => {
  isMobile.value = window.innerWidth <= 768; // Détermine si la largeur de l'écran est inférieure ou égale à 768px
};

// Fonction pour distribuer les devises dans les deux colonnes
const distributeCurrencies = () => {
  const currencies = Object.entries(filteredConversionRates.value); // Conversion de l'objet en tableau de paires clé/valeur
  const col1 = []; // Tableau pour la colonne 1
  const col2 = []; // Tableau pour la colonne 2
  for (let i = 0; i < currencies.length; i++) {
    if (i % 2 === 0) {
      col1.push(currencies[i]); // Ajout de la devise à la colonne 1 si l'index est pair
    } else {
      col2.push(currencies[i]); // Ajout de la devise à la colonne 2 si l'index est impair
    }
  }
  column1Currencies.value = Object.fromEntries(col1); // Mise à jour des devises de la colonne 1
  column2Currencies.value = Object.fromEntries(col2); // Mise à jour des devises de la colonne 2
};

// Cycle de vie du composant: onMounted (après le montage du composant)
// onMounted: https://vuejs.org/api/composition-api-lifecycle.html#onmounted
onMounted(() => {
  fetchExchangeRates(); // Récupération initiale des taux de change
  checkIfMobile(); // Détermination initiale du type d'écran
  window.addEventListener('resize', checkIfMobile); // Ajout d'un écouteur d'événement pour détecter les changements de taille de l'écran
  // Mise à jour des taux de change toutes les heures
  setInterval(() => {
    fetchExchangeRates(); // Récupération des taux de change
    lastUpdated.value = new Date().toLocaleString(); // Mise à jour de la date de dernière mise à jour
  }, 3600000); // 1 heure = 3600000 millisecondes
});

// Cycle de vie du composant: onBeforeUnmount (avant le démontage du composant)
// onBeforeUnmount: https://vuejs.org/api/composition-api-lifecycle.html#onbeforeunmount
onBeforeUnmount(() => {
  window.removeEventListener('resize', checkIfMobile); // Suppression de l'écouteur d'événement resize
});
</script>

<style scoped>
body {
  background-color: rgb(10, 32, 77);
  color: white;
  font-family: Arial, sans-serif;
}

.container {
  display: flex;
  flex-direction: row;
  justify-content: left;
  gap: 20px;
  align-items: center;

  color: white;
  padding: 20px;
}

.title {
  display: flex;
  flex-direction: column;
  gap: 10px;
  text-align: left;
  margin: 0 20px;
  border: rgba(255, 255, 255, 0.5);
  border-left: 1px solid;
  padding: 20px;
  width: 50%;
}

.title h1,
.title p {
  color: white;
  margin: 0;
}

.montant-container {
  display: flex;
  justify-content: right;
  align-items: center;
  width: 100%;
  padding: 20px;
}

.montant-xpf {
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  font-size: 1.2rem;
  font-weight: bold;
  text-transform: uppercase;
  color: white;
  margin: 20px;
  padding: 10px;
  text-align: left;
}

.en-tete {
  color: white;
  display: flex;
  justify-content: right;
  gap: 10px;
  align-items: center;
  margin: 20px;
}

input {
  width: 5%;
  padding: 10px;
  border-radius: 5px;
  border: none;
  background-color: #f0f0f0;
  color: #333;
}

.right-column {
  display: flex;
  width: 100%;
}

.columns-container {
  display: flex;
  width: 100%;
}

.column {
  flex: 1;
  padding: 10px;
}

.currency-card-th {
  font-family: "Verdana";
  font-weight: bold;
  color: white;
  box-sizing: border-box;
  display: flex;
  justify-content: space-around;
  padding: 10px;
}

.currency-card-th div {
  display: flex;
  width: 100%;
  justify-content: space-around;
  align-items: center;
  flex-direction: row;
}

.currency-card {
  padding: 1.5rem;
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  margin-bottom: 10px;
}

.currency-card img {
  width: 40px;
  height: 30px;
  margin: 20px 30px;
  padding: 20px;
}

.container-cards {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: stretch;
}

.carte {
  width: 100%;
  background: #2E4167;
  background: radial-gradient(circle, rgba(46, 65, 103, 1) 24%, rgb(69, 90, 132) 60%, rgba(255, 255, 255, 0.1) 80%, rgba(255, 255, 255, 0) 100%);
  background-size: 200% 200%; /* Augmente la taille du dégradé */
  color: white;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  padding: 20px;
  animation: gradientShift 5s ease infinite; /* Animation du dégradé */
}

.carte span {
  text-align: right;
  font-size: 0.8rem;
  color: #ccc;
}

.data-carte {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  font-size: large;
  font-weight: bold;
}

.carte h3 {
  padding: 0 20px;
}

/* Media query pour les écrans de petite taille (mobile) */
@media (max-width: 768px) {
  .right-column {
    flex-direction: column;
  }

  .columns-container {
    flex-direction: column;
  }

  .column {
    width: 100%;
    border-left: none;
  }

  .currency-card-th {
    width: 100%;
  }
}

/* Définition de l'animation du dégradé */
@keyframes gradientShift {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
</style>
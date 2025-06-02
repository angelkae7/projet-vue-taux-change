<template>
    <h1>{{ msg }}</h1>

<div class="container">
      <div class="title">
      <h1>Bureau de change</h1>
      <p>Exchange office</p>
      </div>
</div>
        <div class="en-tete">
          <label for="xpf-amount">Montant en XPF : </label>
          <input type="number" id="xpf-amount" v-model.number="amountXPF" min="0" placeholder="Entrez un montant en XPF" />
        </div>

        <!-- // Affichage des taux de change -->
        <div class="container-cards">
          <div class="currency-card-th">
            <div>
              <p>Drapeau</p>
              <h4>Pays</h4>
              <p>Taux de change</p>
              <p>Montant converti</p>
            </div>
          </div>
          <div class="currency-card-th">
            <div>
              <p>Drapeau</p>
              <h4>Pays</h4>
              <p>Taux de change</p>
              <p>Montant converti</p>
            </div>
          </div>
        </div>


      <div class="right-column">
        <div class="currency-card" v-for="(rate, currency) in filteredConversionRates" :key="currency">
          <div>
            <img :src="getFlagURL(currency)" :alt="`Flag of ${currency}`" width="30">
            <h4>{{ currency }}</h4>
            <p> {{ rate }}</p>
            <p> {{ (rate * amountXPF) }}</p>
          </div>
          <span>Mise à jour {{ lastUpdated }}</span>
        </div>
      </div>
   

</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      amountXPF: 1,
      conversionRates: {},
      filteredConversionRates: {},
      lastUpdated: new Date().toLocaleString()
    };
  },

  mounted() {
    this.fetchExchangeRates();
    setInterval(this.fetchExchangeRates, 3600000); // 1 heure
  },

  methods: {
    fetchExchangeRates() {
      fetch('https://v6.exchangerate-api.com/v6/1520648efe11018bdeeeb7cd/latest/XPF')
        .then(response => response.json())
        .then(data => {
          const allRates = data.conversion_rates;
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
          this.conversionRates = allRates; // Conserve toutes les devises pour référence
          this.filteredConversionRates = selectedCurrencies; // Assignez les devises filtrées
        })
        .catch(error => {
          console.error('Error fetching data:', error);
        });
    },
    getFlagURL(currency) {
      // Mappez les codes de devise aux codes de pays ISO 3166-1 alpha-2
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
      const countryCode = countryCodes[currency];
      if (countryCode) {
        return `https://flagcdn.com/w40/${countryCode}.png`; // Utilisez Flagcdn pour les drapeaux
      } else {
        return ''; // Retourne une chaîne vide si le code de pays n'est pas trouvé
      }
    }
  }
};
</script>

<style scoped>

body {
  background-color: rgb(10, 32, 77);
  color: white;
  font-family: Arial, sans-serif;
}
h1 {
  color: white;
  text-align: center;
  margin: 0;
}
.title {
  text-align: center;
  margin: 20px 0;
  border: 1px solid rgba(255, 255, 255, 0.5);
  padding: 10px;
  border-radius: 10px;
  width: 50%;
}
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  color: white;
}

.hello {
  background-color: rgb(10, 32, 77);
  padding: 20px;
  color: white;
}

.right-column {

  flex: 8;
  /* Ajustez la valeur selon vos besoins */
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  /* Distribue les cartes uniformément */
}

.currency-card {
  background: linear-gradient(to bottom right,
      rgba(255, 255, 255, 0.2),
      rgba(255, 255, 255, 0.05));
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.5), -1px -1px 2px #aaa, 1px 1px 2px #555;
  backdrop-filter: blur(0.8rem);
  padding: 1.5rem;
  border-radius: 1rem;
  width: calc(50% - 20px);
  /* Ajustez la largeur selon vos besoins (50% moins la marge) */
  margin: 10px;
  box-sizing: border-box;
  /* Inclut la marge et le padding dans la largeur */
  display: flex;
  flex-direction: column;

}

.currency-card-th {
 font-family:"Verdana";
 font-weight: bold;
  color: white;
  border-radius: 1rem;
  width: calc(50% - 20px);
  /* Ajustez la largeur selon vos besoins (50% moins la marge) */
  box-sizing: border-box;
  /* Inclut la marge et le padding dans la largeur */
  display: flex;
  flex-direction: column;


}

.currency-card div {
    color: white;
  display: flex;
  justify-content: space-around;
  align-items: center;
  /* Centre le contenu horizontalement */
  flex-direction: row;
}

.currency-card-th div {
  display: flex;
  width: 100%;

  justify-content: space-around;
  align-items: center;
  /* Centre le contenu horizontalement */
  flex-direction: row;
}

.currency-card span {
  text-align: right;
  font-size: 0.8rem;
  color: #ccc;
}

.container-cards {
 
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: stretch;
  /* Distribue les cartes uniformément */
}

h3 {
  margin: 40px 0 0;
}

input {
  width: 5%;
  padding: 10px;
  border-radius: 5px;
  border: none;
  background-color: #f0f0f0;
  color: #333;
}

.en-tete {
  color: white;
  display: flex;
  justify-content: center;
  gap: 10px;
  align-items: center;
  margin-bottom: 20px;
}

</style>

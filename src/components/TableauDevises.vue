<template>
  <h1>{{ msg }}</h1>

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
      lastUpdated: new Date().toLocaleString(),
      isMobile: false,
      column1Currencies: {},
      column2Currencies: {}
    };
  },
  mounted() {
    this.fetchExchangeRates();
    this.checkIfMobile();
    window.addEventListener('resize', this.checkIfMobile);
    setInterval(this.fetchExchangeRates, 3600000); // 1 heure
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkIfMobile);
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
          this.conversionRates = allRates;
          this.filteredConversionRates = selectedCurrencies;
          this.distributeCurrencies();
        })
        .catch(error => {
          console.error('Error fetching data:', error);
        });
    },
    getFlagURL(currency) {
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
        return `https://flagcdn.com/144x108/${countryCode}.png`;
      } else {
        return '';
      }
    },
    checkIfMobile() {
      this.isMobile = window.innerWidth <= 768;
    },
    distributeCurrencies() {
      const currencies = Object.entries(this.filteredConversionRates);
      const column1 = [];
      const column2 = [];
      for (let i = 0; i < currencies.length; i++) {
        if (i % 2 === 0) {
          column1.push(currencies[i]);
        } else {
          column2.push(currencies[i]);
        }
      }
      this.column1Currencies = Object.fromEntries(column1);
      this.column2Currencies = Object.fromEntries(column2);
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
  text-transform: uppercase;
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  font-size: 1.2rem;
  font-weight: bold;
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
  animation: fadeIn 0.5s ease-in-out; /* Animation d'apparition */
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

/* Définition de l'animation d'apparition */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
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

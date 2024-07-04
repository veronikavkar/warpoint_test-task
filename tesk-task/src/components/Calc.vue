<template>
  <div>
    <div class="container">
      <h1 class="title">Тарифный Калькулятор</h1>
      <div class="grid">
        <div
          class="grid-item"
          v-for="(item, index) in items.tariffOptions"
          :key="index"
          active-class="active"
          :class="{ active: selectedTariff === item.value }"
          @click="selectedTariff = item.value"
        >
          <svg-icon type="mdi" :path="item.icon"></svg-icon>
          <p class="grid-text">{{ item.label }}</p>
        </div>

        <div
          class="grid-item"
          v-for="(item, index) in items.currencyOptions"
          :key="index"
          :class="{ active: selectedCurrency === item.value }"
          @click="selectedCurrency = item.value"
        >
          <svg-icon type="mdi" :path="item.icon"></svg-icon>
          <p class="grid-text">{{ item.label }}</p>
        </div>
        <div
          class="grid-item"
          v-for="(item, index) in items.periodOptions"
          :key="index"
          :class="{ active: selectedPeriod === item.value }"
          @click="selectedPeriod = item.value"
        >
          {{ item.label }}
        </div>
      </div>
      <button class="calculate-btn" @click="calculate">Рассчитать</button>
      <div id="result" class="result">{{ result }}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import SvgIcon from "@jamescoyle/vue-icon";
import { mdiAirplane } from "@mdi/js";
import { mdiRocketLaunch } from "@mdi/js";
import { mdiCurrencyCny } from "@mdi/js";
import { mdiCurrencyKzt } from "@mdi/js";

export default {
  components: { SvgIcon },
  data() {
    return {
      icons: [{ plane: mdiAirplane }, { rocket: mdiRocketLaunch }],

      selectedTariff: "",
      selectedCurrency: null,
      selectedPeriod: null,
      result: "",
      items: {
        tariffOptions: [
          { value: "standard", label: "Стандартный", icon: mdiAirplane },
          { value: "pro", label: "Про", icon: mdiRocketLaunch },
        ],
        currencyOptions: [
          { value: "kzt", label: "Тенге", icon: mdiCurrencyKzt },
          { value: "cny", label: "Юань", icon: mdiCurrencyCny },
        ],
        periodOptions: [
          { value: "month", label: "Месяц" },
          { value: "year", label: "Год" },
        ],
        exchangeRates: {
          KZT: null,
          CNY: null,
        },
        prices: {
          standard: {
            month: 100,
            year: 1000,
          },
          pro: {
            month: 150,
            year: 1450,
          },
        },
      },
    };
  },
  created() {
    axios
      .get(
        "https://v6.exchangerate-api.com/v6/16885272c4acc9d4957df86e/latest/RUB"
      )
      .then((response) => {
        let currency = response.data.conversion_rates;
        this.items.exchangeRates.KZT = currency.KZT;
        this.items.exchangeRates.CNY = currency.CNY;
      });
  },

  methods: {
    calculate() {
      if (
        !this.selectedTariff ||
        !this.selectedCurrency ||
        !this.selectedPeriod
      ) {
        this.result = "Пожалуйста, выберите все параметры.";
        return;
      }

      const basePrice =
        this.items.prices[this.selectedTariff][this.selectedPeriod];
      const exchangeRate =
        this.selectedCurrency === "kzt"
          ? this.items.exchangeRates.KZT
          : this.items.exchangeRates.CNY;
      const priceInSelectedCurrency = basePrice * exchangeRate;

      this.result = `Стоимость: ${priceInSelectedCurrency.toFixed(2)} ${
        this.selectedCurrency === "kzt" ? "тенге" : "юань"
      }`;
    },
  },
};
</script>
<style scoped>
.container {
  margin: auto;
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
  position: relative;
  min-width: 400px;
  height: 540px;
  overflow: hidden;
  padding: 24px 30px;
  box-sizing: border-box;
  background: #d2dbee;
  border-radius: 15%;
  box-shadow: 9.91px 9.91px 15px #bfc7d9, -9.91px -9.91px 15px #e5efff;
}
.title {
  text-align: center;
  color: #3e87a1;
  font-size: 26px;
  margin-bottom: 30px;
}
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}
.grid-item {
  color: #3e87a1;
  border-radius: 12px;
  padding: 20px;
  text-align: center;
  cursor: pointer;
  background: #d2dbee;
  border-radius: 15%;
  box-shadow: 9.91px 9.91px 15px #bfc7d9, -9.91px -9.91px 15px #e5efff;
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
}
.grid-item:hover {
  background: #c8d1e5;
}
.grid-item.active {
  background: #d2dbee;
  border-radius: 15%;
  box-shadow: inset 9.91px 9.91px 15px #bfc7d9,
    inset -9.91px -9.91px 15px #e5efff;
}
.grid-text {
  margin-top: 10px;
}
.calculate-btn {
  display: block;
  width: 100%;
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
  margin-top: 30px;
  background: #d2dbee;
  border-radius: 30px;
  box-shadow: 9.91px 9.91px 15px #bfc7d9, -9.91px -9.91px 15px #e5efff;
  border: none;
  color: #3e87a1;
  font-weight: 600;
}
.result {
  margin-top: 26px;
  text-align: center;
  font-size: 18px;
  color: #3e87a1;
}
</style>

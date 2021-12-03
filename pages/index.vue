<template>
  <v-row>
    <v-col>
      <v-card>
        <v-card-title>
          Pairs PankakeSwapV2
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search1"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
          <v-spacer></v-spacer>
          <v-btn>
            REFRESH
          </v-btn>
        </v-card-title>
        <v-data-table
          :headers="headersPairs"
          :items="desserts"
          :search="search1"
        >
          <template v-slot:[`item.base_name`]="{ item }">
            {{ item.base_symbol }}/{{ item.quote_symbol }}
          </template>
          <template v-slot:[`item.liquidity`]="{ item }">
            {{ parseFloat(item.liquidity).toFixed(2) }} USD
          </template>
          <template v-slot:[`item.price`]="{ item }">
            {{ parseFloat(item.price).toFixed(2) }}
          </template>
          <template v-slot:[`item.info`]="{ item }">
            <v-btn
              :href="`https://bscscan.com/token/${item.base_address}`"
              target="_blank"
            >
              {{ item.base_symbol }}
            </v-btn>
            <v-btn
              :href="`https://bscscan.com/token/${item.quote_address}`"
              target="_blank"
            >
              {{ item.quote_symbol }}
            </v-btn>
          </template>
        </v-data-table>
      </v-card>
    </v-col>
    <v-col>
      <v-card>
        <v-card-title>
          Coins Binance Smart Chain
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search2"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table
          dense
          :headers="headersCoins"
          :items="listCoins"
          :search="search2"
        >
          <template
            v-slot:[`item.price_change_percentage_1h_in_currency`]="{ item }"
          >
            {{ item.price_change_percentage_1h_in_currency.toFixed(2) }} %
          </template>
          <template
            v-slot:[`item.price_change_percentage_24h_in_currency`]="{ item }"
          >
            {{ item.price_change_percentage_24h_in_currency.toFixed(2) }} %
          </template>
          <template
            v-slot:[`item.price_change_percentage_7d_in_currency`]="{ item }"
          >
            {{ item.price_change_percentage_7d_in_currency.toFixed(2) }} %
          </template>
          <template
            v-slot:[`item.price_change_percentage_30d_in_currency`]="{ item }"
          >
            {{ item.price_change_percentage_30d_in_currency.toFixed(2) }} %
          </template>
        </v-data-table>
      </v-card>
    </v-col>

    <br />
    <br />
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      search1: "",
      search2: "",
      headersPairs: [
        {
          text: "PAIR",
          align: "center",
          value: "base_name",
        },
        { text: "LIQUIDITY ON POOL", value: "liquidity", align: "center" },
        { text: "PRICE (X/Y)", value: "price", align: "center" },
        { text: "INFORMATION", value: "info", align: "center" },
      ],
      headersCoins: [
        {
          text: "NAME",
          align: "center",
          value: "name",
        },
        { text: "MARKETCAP (USD)", value: "market_cap", align: "center" },
        { text: "PRICE (USD) ", value: "current_price", align: "center" },
        { text: "% 1h", value: "price_change_percentage_1h_in_currency", align: "center" },
        { text: "% 24h", value: "price_change_percentage_24h_in_currency", align: "center" },
        { text: "% 7d", value: "price_change_percentage_7d_in_currency", align: "center" },
        { text: "% 30d", value: "price_change_percentage_30d_in_currency", align: "center" },
      ],
      desserts: [],
      listCoins: [],
    };
  },
  methods: {
    async info() {
      let listPairs = [];
      let objectPairs = await this.$axios.get(
        "https://api.pancakeswap.info/api/v2/pairs"
      );
      objectPairs = objectPairs.data;
      for (const property in objectPairs.data) {
        listPairs.push(objectPairs.data[property]);
      }
      this.desserts = listPairs;
      let { data } = await this.$axios.get(
        "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&category=binance-smart-chain&order=market_cap_desc&per_page=100&page=1&sparkline=false&price_change_percentage=1h%2C24h%2C7d%2C30d"
      );
      this.listCoins = data;
    },
  },
  mounted() {
    this.info();
  },
};
</script>

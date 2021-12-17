<template>
  <v-container>
    <v-overlay :value="overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>
    <v-row>
      <v-col>
        <v-card>
          <v-card-title> Search </v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field
                  label="Min Liquidity"
                  v-model.number="min"
                  prefix="$"
                  suffix="USD"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-text-field
                  label="Max Liquidity"
                  v-model.number="max"
                  prefix="$"
                  suffix="USD"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-text-field
                  label="Total TX"
                  v-model.number="tx"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-menu
                  v-model="menu2"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="date"
                      label="Date"
                      prepend-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="date"
                    @input="menu2 = false"
                  ></v-date-picker>
                </v-menu>
              </v-col>
              <v-col>
                <v-btn @click="info2()"> SEARCH </v-btn>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row v-show="listPairs[0]">
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
          </v-card-title>
          <v-data-table
            :headers="headersPairs"
            :items="listPairs"
            :search="search1"
          >
            <template v-slot:[`item.liquidity`]="{ item }">
              {{ parseFloat(item.reserveUSD).toFixed(2) }} USD
            </template>
            <template v-slot:[`item.reserve0`]="{ item }">
              {{ parseFloat(item.reserve0).toFixed(2) }}
              {{ item.token0.symbol }}
            </template>
            <template v-slot:[`item.reserve1`]="{ item }">
              {{ parseFloat(item.reserve1).toFixed(2) }}
              {{ item.token1.symbol }}
            </template>
            <template v-slot:[`item.totalSupply`]="{ item }">
              {{ parseFloat(item.totalSupply).toFixed(2) }} LPs
            </template>
            <template v-slot:[`item.date`]="{ item }">
              {{
                new Date(Math.floor(item.timestamp * 1000.0))
                  .toISOString()
                  .substr(0, 10)
              }}
            </template>
            <template v-slot:[`item.info`]="{ item }">
              <v-btn
                :href="`https://bscscan.com/token/${item.token0.id}`"
                target="_blank"
              >
                {{ item.token0.symbol }}
              </v-btn>
              <v-btn
                :href="`https://bscscan.com/token/${item.token1.id}`"
                target="_blank"
              >
                {{ item.token1.symbol }}
              </v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-col>
      <!--<v-col>
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
          <v-spacer></v-spacer>
          <v-btn @click="info()"> REFRESH </v-btn>
        </v-card-title>
        <v-data-table
          dense
          :headers="headersCoins"
          :items="listCoins"
          :search="search2"
        >
          <template v-slot:[`item.image`]="{ item }">
            <v-img :src="item.image" width="20"> </v-img>
          </template>
          <template
            v-slot:[`item.price_change_percentage_1h_in_currency`]="{ item }"
          >
            <span
              :style="`color: ${
                item.price_change_percentage_1h_in_currency > 0
                  ? 'green'
                  : 'red'
              }`"
            >
              {{ item.price_change_percentage_1h_in_currency.toFixed(2) }} %
            </span>
          </template>
          <template
            v-slot:[`item.price_change_percentage_24h_in_currency`]="{ item }"
          >
            <span
              :style="`color: ${
                item.price_change_percentage_24h_in_currency > 0
                  ? 'green'
                  : 'red'
              }`"
              >{{
                item.price_change_percentage_24h_in_currency.toFixed(2)
              }}
              %</span
            >
          </template>
          <template
            v-slot:[`item.price_change_percentage_7d_in_currency`]="{ item }"
          >
            <span
              :style="`color: ${
                item.price_change_percentage_7d_in_currency > 0
                  ? 'green'
                  : 'red'
              }`"
              >{{
                item.price_change_percentage_24h_in_currency.toFixed(2)
              }}
              %</span
            >
          </template>
          <template
            v-slot:[`item.price_change_percentage_30d_in_currency`]="{ item }"
          >
            <span
              :style="`color: ${
                item.price_change_percentage_30d_in_currency > 0
                  ? 'green'
                  : 'red'
              }`"
              >{{
                item.price_change_percentage_30d_in_currency.toFixed(2)
              }}
              %</span
            >
          </template>
        </v-data-table>
      </v-card>
    </v-col>-->

      <br />
      <br />
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      overlay: false,
      search1: "",
      search2: "",
      headersPairs: [
        {
          text: "PAIR",
          align: "center",
          value: "name",
        },
        { text: "LIQUIDITY ON POOL", value: "liquidity", align: "center" },
        { text: "TRANSACTIONS", value: "totalTransactions", align: "center" },
        { text: "RESERVE BASE", value: "reserve0", align: "center" },
        { text: "RESERVE QUOTE", value: "reserve1", align: "center" },
        { text: "SUPPlY", value: "totalSupply", align: "center" },
        { text: "DATE CREATE", value: "date", align: "center" },
        { text: "INFORMATION", value: "info", align: "center" },
      ],
      headersCoins: [
        { text: "", value: "image", align: "center" },
        {
          text: "NAME",
          align: "center",
          value: "name",
        },
        { text: "MARKETCAP (USD)", value: "market_cap", align: "center" },
        { text: "PRICE (USD) ", value: "current_price", align: "center" },
        {
          text: "% 1h",
          value: "price_change_percentage_1h_in_currency",
          align: "center",
        },
        {
          text: "% 24h",
          value: "price_change_percentage_24h_in_currency",
          align: "center",
        },
        {
          text: "% 7d",
          value: "price_change_percentage_7d_in_currency",
          align: "center",
        },
        {
          text: "% 30d",
          value: "price_change_percentage_30d_in_currency",
          align: "center",
        },
      ],
      listPairs: [],
      listCoins: [],
      date: new Date(Date.now()).toISOString().substr(0, 10),
      menu2: false,
      min: 1000,
      max: 50000,
      tx: 10,
      time: 0,
      lastTx: 0,
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
      this.listPairs = listPairs;
      let { data } = await this.$axios.get(
        "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&category=binance-smart-chain&order=market_cap_desc&per_page=100&page=1&sparkline=false&price_change_percentage=1h%2C24h%2C7d%2C30d"
      );
      this.listCoins = data;
    },
    async info2() {
      try {
        this.overlay = true;
        let myDate = this.date.split("-");
        var newDate = new Date(myDate[0], myDate[1] - 1, myDate[2]);
        let dateOnaDayAgo = new Date(Date.now());
        dateOnaDayAgo.setDate(dateOnaDayAgo.getDate() - 1);
        this.time = Math.floor(newDate.getTime() / 1000.0);
        this.lastTx = Math.floor(dateOnaDayAgo / 1000.0);
        var { data } = await this.$axios({
          method: "POST",
          url: "https://bsc.streamingfast.io/subgraphs/name/pancakeswap/exchange-v2",
          data: {
            query: ` query queryPairs($min: BigDecimal!, $max: BigDecimal!, $tx: BigInt!, $time: BigInt!, $lastTx: BigInt! ) {
  pairs(first: 1000, orderBy: reserveUSD, orderDirection: desc, where: {reserveUSD_gte: $min, reserveUSD_lte: $max, timestamp_gte: $time, totalTransactions_gte: $tx}) {
    id
    name
    reserveUSD
    timestamp
    reserve0
    reserve1
    totalSupply
    totalTransactions
    token0 {
      name
      id
      symbol
    }
    token1 {
      name
      id
      symbol
    }
    swaps(where: {timestamp_gte: $lastTx}){
      timestamp
    }
  }
}
          `,
            variables: {
              min: this.min,
              max: this.max,
              tx: this.tx,
              time: this.time,
              lastTx: this.lastTx,
            },
          },
        });

        this.listPairs = data.data.pairs.filter((p) => p.swaps.length != 0);
        this.overlay = false;
      } catch (error) {
        alert("error in query try again");
        console.error(error);
        this.overlay = false;
      }
    },
  },
  async mounted() {},
};
</script>

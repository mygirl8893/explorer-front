<template>
  <div class="dashboard-card col-12 col-lg-6">
    <div class="item-bg">
      <div class="header">
        <div class="title">
          <img class="logo" src="/static/img/nax-logo.png" />
          <span>NAX {{ $t("price") }}</span>
          <span class="net-status">{{ mainnetText }}</span>
        </div>
        <div v-if="market" class="details">
          {{ $t("dashboardNasPriceUpdateTimePrefix") }}:
          <span v-if="market">{{ timeConversion(this.market.updatedAt) }}</span>
        </div>
      </div>

      <div v-if="market" class="detail">
        <span class="prefix">$</span>
        <span>{{ market.price }}</span>
        <span
          :class="{
            'price-down': market.trends <= 0,
            'price-up': market.trends > 0
          }"
          >{{ market.trends > 0 ? "+" : "-" }}{{ priceChange
          }}<span class="suffix">%</span>
        </span>
      </div>
      <!-- market realtime data -->
      <div class="market">
        <div class="row">
          <div class="col-6">
            <label>{{ $t("dashboardNasMarketCap") }}</label>
            <div>
              <span class="prefix">$</span>
              {{ marketCap }}
            </div>
          </div>
          <div class="col-6">
            <label
              >{{ $t("estimateSupply") }} <br />
              ({{ $t("baseOnCurrentDstakingRate") }})</label
            >
            <div>
              {{ totalSupply }}

              <span class="suffix">NAX</span>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-6">
            <label>{{ $t("dashboardNasMarketVol") }}</label>
            <div>
              <span class="prefix">$</span>
              {{ volume24h }}
            </div>
          </div>
          <div class="col-6">
            <label>{{ $t("totalCirculatingSupply") }}</label>
            <div>
              {{ totalCirculation }}
              <span class="suffix">NAX</span>
            </div>
          </div>
        </div>

        <div class="row bg-black">
          <div class="col-6">
            <label>{{ $t("dstakingNas") }}</label>
            <!-- <div v-if="market">${{ numberAddComma(market.volume24h) }}</div> -->
            <div>
              {{ totalStaking }}
              <span class="suffix">NAS</span>
            </div>

            <a target="__blank" href="https://dstaking.nebulas.io/"
              >{{ $t("dstakeNasMintNaxNow") }} &gt;
            </a>
          </div>
          <div class="col-6">
            <label>{{ $t("dstakingRate") }}</label>
            <div>
              {{ stakingRate }}

              <span class="suffix">%</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { convert2NaxStr, convert2NasStr, isMainnet } from "@/utils/neb";
import { toLocaleString } from "@/utils/number";

export default {
  name: "NaxMarketCard",
  data() {
    return {
      market: null
    };
  },
  methods: {
    timeConversion(ms) {
      return this.$moment(ms).fromNow();
    }
  },
  mounted() {
    this.$api.home.getNaxMarket().then(res => (this.market = res));
  },
  computed: {
    stakingRate() {
      return this.market && (this.market.stakingRate * 100).toFixed(2);
    },
    priceChange() {
      return this.market && `${toLocaleString(this.market.change24h)}`;
    },
    marketCap() {
      return this.market && `${toLocaleString(this.market.marketCap)}`;
    },

    volume24h() {
      return this.market && `${toLocaleString(this.market.volume24h)}`;
    },
    totalSupply() {
      return this.market && convert2NaxStr(this.market.totalSupply, "");
    },
    totalCirculation() {
      return this.market && convert2NaxStr(this.market.totalCirculation, "");
    },
    totalStaking() {
      return this.market && convert2NasStr(this.market.totalStaking, "");
    },
    mainnetText() {
      return !isMainnet(this.$route) ? `Mainnet` : "";
    }
  }
};
</script>

<style lang="scss" scoped>
.title {
  display: flex;
  align-items: center;

  .logo {
    width: 25px;
    height: 25px;
    margin-right: 6px;
  }
}
.bg-black {
  background-color: black;
  padding: 1rem;
  margin: 0 -2rem;

  .col-6 {
    margin-bottom: 15px;
  }
}
</style>

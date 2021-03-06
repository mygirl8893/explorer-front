<template>
  <div class="dashboard-card col-12 col-lg-6">
    <div class="item-bg">
      <div class="header">
        <div class="title">
          <img class="logo" src="/static/img/nas-logo.png" />
          <span>NAS {{ $t("price") }}</span>
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
          <div class="col-6 col-md-6">
            <label>{{ $t("dashboardNasMarketCap") }}</label>
            <div>
              <span class="prefix">$</span>
              {{ this.marketCap }}
            </div>
          </div>
          <div class="col-6 col-md-6">
            <label>{{ $t("totalSupply") }}</label>
            <div>
              {{ this.totalSupply }}
              <span class="suffix">NAS</span>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-6 col-md-6">
            <label>{{ $t("dashboardNasMarketVol") }}</label>
            <div>
              <span class="prefix">$</span>
              {{ this.volume24h }}
            </div>
          </div>
          <div class="col-6 col-md-6">
            <label>{{ $t("circulatingSupply") }}</label>
            <div>
              {{ this.totalCirculation }}
              <span class="suffix">NAS</span>
            </div>
            <router-link class="link link-style" :to="'/distribution'">
              {{ $t("viewNasDistribution") }} &gt;
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { convert2NasStr, isMainnet } from "@/utils/neb";
import { toLocaleString } from "@/utils/number";

export default {
  name: "NasMarketCard",
  data() {
    return {
      market: null
    };
  },
  mounted() {
    this.$api.home.getNasMarket().then(res => (this.market = res));
  },
  methods: {
    timeConversion(ms) {
      return this.$moment(ms).fromNow();
    }
  },
  computed: {
    stakingRate() {
      return this.market && this.market.stakingRate * 100;
    },
    marketCap() {
      return this.market && `${toLocaleString(this.market.marketCap)}`;
    },
    priceChange() {
      return this.market && `${toLocaleString(this.market.change24h)}`;
    },

    volume24h() {
      return this.market && `${toLocaleString(this.market.volume24h)}`;
    },
    totalSupply() {
      return this.market && convert2NasStr(this.market.totalSupply, "");
    },
    totalCirculation() {
      return this.market && convert2NasStr(this.market.totalCirculation, "");
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
    width: 32px;
    height: 32px;
    margin-right: 6px;
  }
}
</style>

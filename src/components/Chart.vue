<template>
  <div>
    <trading-vue :data="chart" :overlays="overlays" :width="this.width" :height="this.height" title-txt="EUR/UDS"
      :toolbar="true"></trading-vue>
  </div>
</template>

<script>
import TradingVue, { DataCube } from "trading-vue-js";
import HeatMap from "./HeatMap.vue";
export default {
  name: "ChartTest",
  components: { TradingVue },
  data() {
    return {
      chart: {},
      width: window.innerWidth - 20,
      height: window.innerHeight - 20,
      heatmapInstance: null,
      overlays: [HeatMap],
    };
  },
  mounted() {
    this.getData();
    setInterval(this.getData(), 500);
    // this.heat();
  },
  methods: {
    getData() {
      const now = new Date().toISOString().split("T")[0];
      fetch(
        `https://api.polygon.io/v2/aggs/ticker/C:EURUSD/range/1/minute/2023-01-09/${now}?adjusted=true&sort=asc&limit=100&apiKey=_225fiCenzxp01e37Spu3_ObWrZhwW7s`
      )
        .then((response) => response.json())
        .then((data) => {
          const { results } = data;
          const ohlcvData = results.map((position) => {
            const { t, o, h, l, c, v } = position;
            return [t, o, h, l, c, v];
          });

          const heatdata = results.map((position) => {
            const { t, c, h, l, n, o, v, vw} = position;
            return [t, c, h, l, n, o, v, vw];
          });

          const datForCube = {
            ohlcv: ohlcvData,
            onchart: [
              {
                name: 'Heatmap',
                type: 'HeatMap',
                data: heatdata,
              },
            ],
            tools: [
              {
                type: "Cursor",
                icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABkAAAAZAgMAAAC5h23wAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAAxQTFRFAAAATU1NTU1NTU1NwlMHHwAAAAR0Uk5TAOvhxbpPrUkAAAAkSURBVHicY2BgYHBggAByabxg1WoGBq2pRCk9AKUbcND43AEAufYHlSuusE4AAAAASUVORK5CYII=",
              },
              {
                type: "LineToolSegment",
                icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABkAAAAZAgMAAAC5h23wAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAAlQTFRFAAAATU1NJCQkCxcHIQAAAAN0Uk5TAP8SmutI5AAAACxJREFUeJxjYMACGAMgNAsLdpoVKi8AVe8A1QblQlWRKt0AoULw2w1zGxoAABdiAviQhF/mAAAAAElFTkSuQmCC",
              },
              {
                type: "LineToolExtended",
                icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABkAAAAZAQMAAAD+JxcgAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAAAZQTFRFAAAATU1NkJ+rOQAAAAJ0Uk5TAP9bkSK1AAAANElEQVR4nGNggABGEMEEIlhABAeI+AASF0AlHmAqA4kzKAAx8wGQuAMKwd6AoYzBAWonAwAcLwTgNfJ3RQAAAABJRU5ErkJggg==",
              },
            ],
            tool: "Cursor",
          };
          this.chart = new DataCube(datForCube);
        });
    },
  },
};
</script>


<style scoped>
</style>

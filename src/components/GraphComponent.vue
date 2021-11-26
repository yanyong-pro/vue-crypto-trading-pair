<template>
    <div class="chart-container">
        <select v-model="selectedPair">
            <option
                v-for="(tradingPair, indexTradingPair) in tradingPairs"
                :value="tradingPair.value"
                :key="indexTradingPair">
                {{ tradingPair.text }}
            </option>
        </select>

        <line-chart
            :selectedPairObject="selectedPairObject">
        </line-chart>
    </div>
</template>

<script>
import LineChart from './LineChart.vue'

export default {
    name: 'GraphComponent',

    components: {
      LineChart
    },

    data() {
        return {
            buf: {},
            chartData: {},
            options: {},
            showGraph: true,
            selectedPair: 'BTCUSD',
            tradingPairs: [
                { text: 'BTC/USD', value: 'BTCUSD' },
                { text: 'BTC/EUR', value: 'BTCEUR' },
                { text: 'BTC/JPY', value: 'BTCJPY' }
            ]
        }
    },

    computed: {
        selectedPairObject () {
            return this.tradingPairs.find(tradingPair => {
                return tradingPair.value === this.selectedPair
            })
        }
    }
}
</script>

<style>
.chart-container {
    max-width: 600px;
    margin:  150px auto;
}
</style>
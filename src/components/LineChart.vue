<script>

import { Line } from 'vue-chartjs'

import 'chartjs-plugin-streaming';


export default {

    extends: Line,

    props: {
        selectedPairObject: {
            type: Object,
            default: null
        }
    },

    data() {
        return {
            datasets: {},
            options: {},
            buf: {},
            webSocket: null
        }
    },

    methods: {
        initialDataForDataSet () {
            this.datasets = {
                datasets: [
                    {
                        label: 'Buy',                     // 'buy' price data
                        borderColor: 'rgb(255, 99, 132)', // line color
                        backgroundColor: 'rgba(255, 99, 132, 0.5)', // fill color
                        fill: false,                      // no fill
                        lineTension: 0,                    // straight line
                        data: []
                    },
                    {
                        label: 'Sell',                    // 'sell' price data
                        borderColor: 'rgb(54, 162, 235)', // line color
                        backgroundColor: 'rgba(54, 162, 235, 0.5)', // fill color
                        fill: false,                      // no fill
                        lineTension: 0,                    // straight line
                        data: []
                    }
                ]
            }
        },

        initialDataForOptions () {
            let id = 'Bitfinex'

            this.options = {
                title: {
                        text: `${this.selectedPairObject.text} (${id})`, // chart title
                        display: true
                },
                scales: {
                    xAxes: [{
                        type: 'realtime', // auto-scroll on X axis
                        realtime: {
                            onRefresh: (chart)=> {
                                Array.prototype.push.apply( chart.data.datasets[0].data, this.buf[id][0] )
                                Array.prototype.push.apply( chart.data.datasets[1].data, this.buf[id][1] ) 
                                this.buf[id] = [[], []];
                            },
                            duration: 300000
                        }
                    }]
                }
            }
        },

        initialWebSocket () {
            //  initialize for collecting the trading data
            this.buf['Bitfinex'] = [[], []]

            this.webSocket = new WebSocket('wss://api.bitfinex.com/ws/');
            this.webSocket.onopen = ()=> {
                this.webSocket.send(JSON.stringify({      // send subscribe request
                    "event": "subscribe",
                    "channel": "trades",
                    "pair": this.selectedPairObject.value
                }))
            }
            this.webSocket.onmessage = (msg) => {     // callback on message receipt
                var response = JSON.parse(msg.data)
                if (response[1] === 'te') {    // Only 'te' message type is needed
                    this.buf['Bitfinex'][response[5] > 0 ? 0 : 1].push({
                        x: response[3] * 1000, // timestamp in milliseconds
                        y: response[4]         // price in US dollar
                    })
                }
            }
        },

        initializeForChart () {
            //  initial web socket
            this.initialWebSocket()
            //  initial dataset for graph
            this.initialDataForDataSet()
            //  initial option for graph
            this.initialDataForOptions()
            this.renderChart( this.datasets, this.options)
        },

        updateTradingPair () {
            //  close connection when change web socket
            this.webSocket.close()

            //  initial everything again because trading pair is changed
            this.initializeForChart()
        }
    },

    mounted () {
        this.initializeForChart()
    },

    watch: {
        'selectedPairObject.value' () {
            //  when trading pair is changed, it will reset graph and create a new connection
            this.updateTradingPair()
        }
    },
}

</script>
<template>
    <div id="chart_container"></div>
</template>

<script lang="ts">
import Vue from 'vue';
import axios from 'axios'
import { Watch, Component } from 'vue-property-decorator'
import store from '../store';
declare const TradingView:any
declare const $:any
const LastPrice = 1234.2365
const text = '测试机'
@Component
export default class TradingViewComponent extends Vue {
    feed:any = null
    chart:any = null
    currency1:string = 'USD'
    currency2:string = 'BTC'
    bars:any = [
        {
            time:1508313600000,
            close:42.1,
            open:41.0,
            high:43.0,
            low:40.4,
            volume:12000
        }, {
            time:1508317200000,
            close:43.4,
            open:42.9,
            high:44.1,
            low:42.1,
            volume:18500
        }, {
            time:1508320800000,
            close:44.3,
            open:43.7,
            high:44.8,
            low:42.8,
            volume:24000
        }, {
            time:1508324400000,
            close:42.8,
            open:44.5,
            high:44.5,
            low:42.3,
            volume:45000
        }
    ]

    constructor() {
        super()
        this.createData()
        store.dispatch('updateChartData', this.bars)
    }

    createData() {
        setInterval(() => {
            let data = store.getters.chartData
            data.push({
                time: new Date().getTime(),
                close: 40 + Math.random() * 5,
                open: 40 + Math.random() * 5,
                high: 40 + Math.random() * 5,
                low: 40 + Math.random() * 5,
                volume: 100000 * Math.random()
            })
            store.dispatch('updateChartData', data)
            this.changePair()
        }, 1000)
    }

    changePair() {
        if(this.chart && this.feed){
            this.feed._fireEvent('pair_change');
            this.chart.activeChart().resetData();
            this.chart.activeChart().setSymbol(this.currency1+":"+this.currency2, () => {
            });
        }
    }

    mounted() {
        this.feed = this.createFeed();

        TradingView.onready((configurationData:any) => {
            this.chart = new TradingView.widget({
                fullscreen: false,
                autosize: true,
                symbol:this.currency1 + ":"+ this.currency2,
                container_id: "chart_container",
                datafeed: this.feed,
                library_path: "/custom_scripts/chart_main/",
                locale: "en",
                timezone: 'Etc/UTC', //todo: ustawianie timezone'a po strefie usera
                charts_storage_api_version: "1.1",
                client_id: 'tradingview.com',
                user_id: 'public_user_id',
                debug: true,
                // loading_screen:{ backgroundColor: "#00ff00",foregroundColor: "#000000", }, //todo:do it
                interval: '60',
                // timeframe:'',//todo: na koncu
                toolbar_bg: "#20334d",
                // saved_data: this.savedData,
                allow_symbol_change: true,
                time_frames: [
                    {text: "1y", resolution: "1W"},
                    {text: "6m", resolution: "3D"},
                    {text: "3m", resolution: "1D"},
                    {text: "1m", resolution: "1D"},
                    {text: "1w", resolution: "30"},
                    {text: "3d", resolution: "30"},
                    {text: "1d", resolution: "30"},
                    {text: "6h", resolution: "15"},
                    {text: "1h", resolution: "1"}],
                drawings_access: {
                    type: 'black',
                    // tools: [{name: "Regression Trend"}]//todo: moje
                    tools: [{name: "Trend Line", grayed: true}, {name: "Trend Angle", grayed: true}] //todo: bb
                },
                disabled_features: [
                    "header_symbol_search",
                    "header_interval_dialog_button",
                    "show_interval_dialog_on_key_press",
                    "symbol_search_hot_key",
                    "study_dialog_search_control",
                    "display_market_status",
                    "header_compare",
                    "edit_buttons_in_legend",
                    "symbol_info",
                    "border_around_the_chart",
                    "main_series_scale_menu",
                    "star_some_intervals_by_default",
                    "datasource_copypaste",
                    "right_bar_stays_on_scroll",
                    "context_menus",
                    "go_to_date",
                    "compare_symbol",
                    "border_around_the_chart",
                    "timezone_menu",
                    "header_resolutions",//todo: przetestowac
                    "control_bar",//todo: przetestowac
                    "edit_buttons_in_legend",//todo: przetestowac
                    "remove_library_container_border",
                ],
                enabled_features: [
                    "dont_show_boolean_study_arguments",
                    "use_localstorage_for_settings",
                    "remove_library_container_border",
                    "save_chart_properties_to_local_storage",
                    "side_toolbar_in_fullscreen_mode",
                    "hide_last_na_study_output",
                    "constraint_dialogs_movement",//todo: nie do końca jestem pewien
                ],
                studies_overrides: {
                    "volume.volume.color.0": "#fe4761",
                    "volume.volume.color.1": "#3fcfb4",
                    "volume.volume.transparency": 75,
                },
                overrides: {
                    "symbolWatermarkProperties.color": "rgba(0,0,0, 0)",
                    "paneProperties.background": "#20334d",
                    "paneProperties.vertGridProperties.color": "#344568",
                    "paneProperties.horzGridProperties.color": "#344568",
                    "paneProperties.crossHairProperties.color": "#58637a",
                    "paneProperties.crossHairProperties.style": 2,
                    "mainSeriesProperties.style": 9,
                    "mainSeriesProperties.showCountdown": false,
                    "scalesProperties.showSeriesLastValue": true,
                    "mainSeriesProperties.visible": false,
                    "mainSeriesProperties.showPriceLine": false,
                    "mainSeriesProperties.priceLineWidth": 1,
                    "mainSeriesProperties.lockScale": false,
                    "mainSeriesProperties.minTick": "default",
                    "mainSeriesProperties.extendedHours": false,
                    "volumePaneSize": "tiny",
                    editorFontsList: ["Lato", "Arial", "Verdana", "Courier New", "Times New Roman"],
                    "paneProperties.topMargin": 5,
                    "paneProperties.bottomMargin": 5,
                    "paneProperties.leftAxisProperties.autoScale": true,
                    "paneProperties.leftAxisProperties.autoScaleDisabled": false,
                    "paneProperties.leftAxisProperties.percentage": false,
                    "paneProperties.leftAxisProperties.percentageDisabled": false,
                    "paneProperties.leftAxisProperties.log": false,
                    "paneProperties.leftAxisProperties.logDisabled": false,
                    "paneProperties.leftAxisProperties.alignLabels": true,
                    // "paneProperties.legendProperties.showStudyArguments": true,
                    "paneProperties.legendProperties.showStudyTitles": true,
                    "paneProperties.legendProperties.showStudyValues": true,
                    "paneProperties.legendProperties.showSeriesTitle": true,
                    "paneProperties.legendProperties.showSeriesOHLC": true,
                    "scalesProperties.showLeftScale": false,
                    "scalesProperties.showRightScale": true,
                    "scalesProperties.backgroundColor": "#20334d",
                    "scalesProperties.lineColor": "#46587b",
                    "scalesProperties.textColor": "#8f98ad",
                    "scalesProperties.scaleSeriesOnly": false,
                    "mainSeriesProperties.priceAxisProperties.autoScale": true,
                    "mainSeriesProperties.priceAxisProperties.autoScaleDisabled": false,
                    "mainSeriesProperties.priceAxisProperties.percentage": false,
                    "mainSeriesProperties.priceAxisProperties.percentageDisabled": false,
                    "mainSeriesProperties.priceAxisProperties.log": false,
                    "mainSeriesProperties.priceAxisProperties.logDisabled": false,
                    "mainSeriesProperties.candleStyle.upColor": "#3fcfb4",
                    "mainSeriesProperties.candleStyle.downColor": "#fe4761",
                    "mainSeriesProperties.candleStyle.drawWick": true,
                    "mainSeriesProperties.candleStyle.drawBorder": true,
                    "mainSeriesProperties.candleStyle.borderColor": "#3fcfb4",
                    "mainSeriesProperties.candleStyle.borderUpColor": "#3fcfb4",
                    "mainSeriesProperties.candleStyle.borderDownColor": "#fe4761",
                    "mainSeriesProperties.candleStyle.wickColor": "#737375",
                    "mainSeriesProperties.candleStyle.wickUpColor": "#3fcfb4",
                    "mainSeriesProperties.candleStyle.wickDownColor": "#fe4761",
                    "mainSeriesProperties.candleStyle.barColorsOnPrevClose": false,
                    "mainSeriesProperties.hollowCandleStyle.upColor": "#3fcfb4",
                    "mainSeriesProperties.hollowCandleStyle.downColor": "#fe4761",
                    "mainSeriesProperties.hollowCandleStyle.drawWick": true,
                    "mainSeriesProperties.hollowCandleStyle.drawBorder": true,
                    "mainSeriesProperties.hollowCandleStyle.borderColor": "#3fcfb4",
                    "mainSeriesProperties.hollowCandleStyle.borderUpColor": "#3fcfb4",
                    "mainSeriesProperties.hollowCandleStyle.borderDownColor": "#fe4761",
                    "mainSeriesProperties.hollowCandleStyle.wickColor": "#737375",
                    "mainSeriesProperties.hollowCandleStyle.wickUpColor": "#3fcfb4",
                    "mainSeriesProperties.hollowCandleStyle.wickDownColor": "#fe4761",
                    "mainSeriesProperties.haStyle.upColor": "#3fcfb4",
                    "mainSeriesProperties.haStyle.downColor": "#fe4761",
                    "mainSeriesProperties.haStyle.drawWick": true,
                    "mainSeriesProperties.haStyle.drawBorder": true,
                    "mainSeriesProperties.haStyle.borderColor": "#3fcfb4",
                    "mainSeriesProperties.haStyle.borderUpColor": "#3fcfb4",
                    "mainSeriesProperties.haStyle.borderDownColor": "#fe4761",
                    "mainSeriesProperties.haStyle.wickColor": "#737375",
                    "mainSeriesProperties.haStyle.wickUpColor": "#3fcfb4",
                    "mainSeriesProperties.haStyle.wickDownColor": "#fe4761",
                    "mainSeriesProperties.haStyle.barColorsOnPrevClose": false,
                    "mainSeriesProperties.barStyle.upColor": "#3fcfb4",
                    "mainSeriesProperties.barStyle.downColor": "#fe4761",
                    "mainSeriesProperties.barStyle.barColorsOnPrevClose": false,
                    "mainSeriesProperties.barStyle.dontDrawOpen": false,
                    "mainSeriesProperties.lineStyle.color": "#0cbef3",
                    "mainSeriesProperties.lineStyle.linestyle": 0,
                    "mainSeriesProperties.lineStyle.linewidth": 1,
                    "mainSeriesProperties.lineStyle.priceSource": "close",
                    "mainSeriesProperties.areaStyle.color1": "#0cbef3",
                    "mainSeriesProperties.areaStyle.color2": "#0098c4",
                    "mainSeriesProperties.areaStyle.linecolor": "#0cbef3",
                    "mainSeriesProperties.areaStyle.linestyle": 0,
                    "mainSeriesProperties.areaStyle.linewidth": 1,
                    "mainSeriesProperties.areaStyle.priceSource": "close",
                    "mainSeriesProperties.areaStyle.transparency": 80
                },
                custom_css_url: 'chart.css'
            });
        });
    }

    createFeed() {
        let Datafeed:any = {};

        Datafeed.DataPulseUpdater = function(datafeed:any, updateFrequency:any) {
            this._datafeed = datafeed;
            this._subscribers = {};

            this._requestsPending = 0;
            var that = this;

            var update = function() {
                if (that._requestsPending > 0) {
                    return;
                }

                for (var listenerGUID in that._subscribers) {
                    var subscriptionRecord = that._subscribers[listenerGUID];
                    var resolution = subscriptionRecord.resolution;

                    var datesRangeRight = parseInt(((new Date().valueOf()) / 1000).toFixed());

                    //	BEWARE: please note we really need 2 bars, not the only last one
                    //	see the explanation below. `10` is the `large enough` value to work around holidays
                    var datesRangeLeft = datesRangeRight - that.periodLengthSeconds(resolution, 10);

                    that._requestsPending++;

                    (function(_subscriptionRecord) { // eslint-disable-line
                        that._datafeed.getBars(_subscriptionRecord.symbolInfo, resolution, datesRangeLeft, datesRangeRight, function(bars:any) {
                                that._requestsPending--;

                                //	means the subscription was cancelled while waiting for data
                                if (!that._subscribers.hasOwnProperty(listenerGUID)) {
                                    return;
                                }

                                if (bars.length === 0) {
                                    return;
                                }

                                var lastBar = bars[bars.length - 1];
                                if (!isNaN(_subscriptionRecord.lastBarTime) && lastBar.time < _subscriptionRecord.lastBarTime) {
                                    return;
                                }

                                var subscribers = _subscriptionRecord.listeners;

                                //	BEWARE: this one isn't working when first update comes and this update makes a new bar. In this case
                                //	_subscriptionRecord.lastBarTime = NaN
                                var isNewBar = !isNaN(_subscriptionRecord.lastBarTime) && lastBar.time > _subscriptionRecord.lastBarTime;

                                //	Pulse updating may miss some trades data (ie, if pulse period = 10 secods and new bar is started 5 seconds later after the last update, the
                                //	old bar's last 5 seconds trades will be lost). Thus, at fist we should broadcast old bar updates when it's ready.
                                if (isNewBar) {
                                    if (bars.length < 2) {
                                        throw new Error('Not enough bars in history for proper pulse update. Need at least 2.');
                                    }

                                    var previousBar = bars[bars.length - 2];
                                    for (var i = 0; i < subscribers.length; ++i) {
                                        subscribers[i](previousBar);
                                    }
                                }

                                _subscriptionRecord.lastBarTime = lastBar.time;

                                for (var j = 0; j < subscribers.length; ++j) {
                                    subscribers[j](lastBar);
                                }
                            },

                            //	on error
                            function() {
                                that._requestsPending--;
                            });
                    })(subscriptionRecord);
                }
            };

            if (typeof updateFrequency != 'undefined' && updateFrequency > 0) {
                setInterval(update, updateFrequency);
            }
        };

        Datafeed.DataPulseUpdater.prototype.periodLengthSeconds = function(resolution:any, requiredPeriodsCount:any) {
            var daysCount = 0;

            if (resolution === 'D') {
                daysCount = requiredPeriodsCount;
            } else if (resolution === 'M') {
                daysCount = 31 * requiredPeriodsCount;
            } else if (resolution === 'W') {
                daysCount = 7 * requiredPeriodsCount;
            } else {
                daysCount = requiredPeriodsCount * resolution / (24 * 60);
            }

            return daysCount * 24 * 60 * 60;
        };

        Datafeed.DataPulseUpdater.prototype.subscribeDataListener = function(symbolInfo:any, resolution:any, newDataCallback:any, listenerGUID:any) {
            this._datafeed._logMessage('Subscribing ' + listenerGUID);

            if (!this._subscribers.hasOwnProperty(listenerGUID)) {
                this._subscribers[listenerGUID] = {
                    symbolInfo: symbolInfo,
                    resolution: resolution,
                    lastBarTime: NaN,
                    listeners: []
                };
            }

            this._subscribers[listenerGUID].listeners.push(newDataCallback);
        };

        Datafeed.DataPulseUpdater.prototype.unsubscribeDataListener = function(listenerGUID:any) {
            this._datafeed._logMessage('Unsubscribing ' + listenerGUID);
            delete this._subscribers[listenerGUID];
        };

        Datafeed.Container = function(updateFrequency:any){
            this._configuration = {
                supports_search: false,
                supports_group_request: false,
                supported_resolutions: ['1', '3', '5', '15', '30', '60', '120', '240', '360', '720', '1D', '3D', '1W', '1M'],
                supports_marks: true,
                supports_timescale_marks: true,
                exchanges: ['myExchange']
            };

            this._barsPulseUpdater = new Datafeed.DataPulseUpdater(this, updateFrequency || 10 * 1000);
            // this._quotesPulseUpdater = new Datafeed.QuotesPulseUpdater(this);

            this._enableLogging = true;
            this._callbacks = {};

            this._initializationFinished = true;
            this._fireEvent('initialized');
            this._fireEvent('configuration_ready');
        };

        Datafeed.Container.prototype._fireEvent = function(event:any, argument:any) {
            if (this._callbacks.hasOwnProperty(event)) {
                var callbacksChain = this._callbacks[event];
                for (var i = 0; i < callbacksChain.length; ++i) {
                    callbacksChain[i](argument);
                }

                this._callbacks[event] = [];
            }
        };

        Datafeed.Container.prototype._logMessage = function(message:any) {
            if (this._enableLogging) {
                var now = new Date();
            }
        };

        Datafeed.Container.prototype.on = function(event:any, callback:any) {
            if (!this._callbacks.hasOwnProperty(event)) {
                this._callbacks[event] = [];
            }

            this._callbacks[event].push(callback);
            return this;
        };

        Datafeed.Container.prototype.onReady = function(callback:any) {
            let that = this;
            if (this._configuration) {
                setTimeout(function() {
                    callback(that._configuration);
                }, 0);
            }
            else {
                this.on('configuration_ready', function() {
                    callback(that._configuration);
                });
            }
        };

        Datafeed.Container.prototype.resolveSymbol = function(symbolName:any, onSymbolResolvedCallback:any, onResolveErrorCallback:any) {
            this._logMessage("GOWNO :: resolve symbol "+ symbolName);
            Promise.resolve().then(() => {
                this._logMessage("GOWNO :: onResultReady inject "+this.currency1 + ":"+ this.currency2);
                onSymbolResolvedCallback({
                    "name": this.currency1 + ":"+ this.currency2,
                    "timezone": "Europe/Warsaw",
                    "pricescale": LastPrice > 1000 ? 100 : 100000000,
                    "minmov": 1,
                    "minmov2": 0,
                    "ticker": this.currency1 + ":"+ this.currency2,
                    "description": "",
                    "session": "24x7",
                    "type": "bitcoin",
                    "exchange-traded": "myExchange",
                    "exchange-listed": "myExchange",
                    "has_intraday": true,
                    "intraday_multipliers": ['60'], //It is an array containing intraday resolutions (in minutes) the datafeed wants to build by itself. E.g., if the datafeed reported he supports resolutions ["1", "5", "15"], but in fact it has only 1 minute bars for symbol X, it should set intraday_multipliers of X = [1]. This will make Charting Library to build 5 and 15 resolutions by itself.
                    "has_weekly_and_monthly": false,
                    "has_no_volume": false,
                    "regular_session": "24x7"
                });
            })
        };

        Datafeed.Container.prototype.getBars = function(symbolInfo:any, resolution:any, rangeStartDate:any, rangeEndDate:any, onDataCallback:any, onErrorCallback:any) {
            if (rangeStartDate > 0 && (rangeStartDate + '').length > 10) {
                throw new Error();
            }
            onDataCallback([], { noData: true });
            //onDataCallback(bars, { noData: true , nextTime: data.nb || data.nextTime });
        };

        Datafeed.Container.prototype.subscribeBars = function(symbolInfo:any, resolution:any, onRealtimeCallback:any, listenerGUID:any, onResetCacheNeededCallback:any) {
            store.getters.chartData.forEach(function (bar:any) { // in subscribeBars
                onRealtimeCallback(bar)
            });
            this.on('pair_change', function() {
                onResetCacheNeededCallback();
            });
            //this._barsPulseUpdater.subscribeDataListener(symbolInfo, resolution, onRealtimeCallback, listenerGUID, onResetCacheNeededCallback);
        };

        Datafeed.Container.prototype.unsubscribeBars = function(listenerGUID:any) {
            this._barsPulseUpdater.unsubscribeDataListener(listenerGUID);
        };

        return new Datafeed.Container;
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
#chart_container {
    height: calc(100vh - 100px);
}
</style>

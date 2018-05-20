<!--<template>
  <div class="wrapper" @click="update">
    <image :src="logoUrl" class="logo"></image>
    <text class="title">Hello app{{target}}</text>
    <text class="desc">Now, let's use vue to build your weex app.</text>
  </div>
</template>

<style>
  .wrapper { align-items: center; margin-top: 120px; }
  .title { padding-top:40px; padding-bottom: 40px; font-size: 48px; }
  .logo { width: 360px; height: 156px; }
  .desc { padding-top: 20px; color:#888; font-size: 24px;}
</style>

<script>
  export default {
    data: {
      logoUrl: 'http://img1.vued.vanthink.cn/vued08aa73a9ab65dcbd360ec54659ada97c.png',
      target: 'World'
    },
    methods: {
      update: function (e) {
        this.target = 'Weex'
        console.log('target:', this.target)
      }
    }
  }
</script>-->

<template>
  <scroller>
    <div class="group">
      <text class="title">method = getListingsResult</text>
      <text class="count">{{getListingsResult}}</text>
    </div>

    <div class="group">
      <text class="title">method = getTickerResult</text>
      <cell class="cell" v-for="item in getTickerResultObj">
        <text class="A-title">{{item.symbol}}</text>
        <text class="A-desc">{{item.quotes.USD.price}}</text>
        <text class="A-comment">{{item.quotes.USD.percent_change_24h}}%</text>
      </cell>
      <text class="count">{{getTickerResult}}</text>
    </div>

    <div class="group">
      <text class="title">method = getTickerIDResult</text>
      <cell class="cell" v-for="item in items">
        <text class="A-title">{{item.name}}</text>
        <text class="A-desc">{{item.quotes.USD.price}}</text>
        <text class="A-comment">{{item.quotes.USD.percent_change_24h}}%</text>
      </cell>
      <text class="count">{{getTickerIDResult}}</text>
    </div>

    <div class="group">
      <text class="title">method = getTickerIdCnyResult</text>
      <text class="count">{{getTickerIdCnyResult}}</text>
    </div>
    <div class="group">
      <text class="title">method = getGlobalResult</text>
      <text class="count">{{getGlobalResult}}</text>
    </div>
  </scroller>
</template>

<script>

    var stream = weex.requireModule('stream');
    var getTickerResultObj;
    var btcData;
    module.exports = {
        data: function () {
            return {
                getListingsResult: 'loading...',
                getTickerResult: 'loading...',
                getTickerIDResult: 'loading...',
                getTickerIdCnyResult: 'loading...',
                getGlobalResult: 'loading...',
                items: [{
                    "id": 1,
                    "name": "Bitcoin",
                    "symbol": "BTC",
                    "website_slug": "bitcoin",
                    "rank": 1,
                    "circulating_supply": 17041575.0,
                    "total_supply": 17041575.0,
                    "max_supply": 21000000.0,
                    "quotes": {
                        "USD": {
                            "price": 8214.7,
                            "volume_24h": 5473430000.0,
                            "market_cap": 139991426153.0,
                            "percent_change_1h": 0.09,
                            "percent_change_24h": 2.29,
                            "percent_change_7d": -2.44
                        }
                    },
                    "last_updated": 1526699671
                }]
            }
        },
        created: function() {
            this.update();
            this.getData();
        },
        beforeDestroy:function(){
            if(this.timer){
                clearInterval(this.timer);  //在Vue实例销毁前，清除我们的定时器
            }
        },
        methods: {
        update () {
            var _this = this;  //声明一个变量指向Vue实例this，保证作用域一致
            this.timer = setInterval(function(){
                _this.getData();
            },5000)
        },
        getData () {
            var me = this;
            var listings_URL = 'https://api.coinmarketcap.com/v2/listings/'
            var ticker_URL = 'https://api.coinmarketcap.com/v2/ticker/?limit=10'

            var ticker_id_URL = 'https://api.coinmarketcap.com/v2/ticker/1/'
            var ticker_id_cny_URL = 'https://api.coinmarketcap.com/v2/ticker/1/?convert=CNY'
            var global_URL = 'https://api.coinmarketcap.com/v2/global/?convert=BTC'

            /*stream.fetch({
                method: 'GET',
                url: listings_URL,
                type:'json'
            }, function(ret) {
                if(!ret.ok){
                    me.getListingsResult = "request failed";
                }else{
                    console.log('get:'+ret);
                    me.getListingsResult = JSON.stringify(ret.data);
                }
            },function(response){
                console.log('get in progress:'+response.length);
                me.getListingsResult = "bytes received:"+response.length;
            });*/

            stream.fetch({
                method: 'GET',
                url: ticker_URL,
                type: 'json'
            }, function (ret) {
                if (!ret.ok) {
                    me.getTickerResult = "request failed";
                } else {
                    console.log('get:' + ret);
                    me.getTickerResult = JSON.stringify(ret.data);
                    me.getTickerResultObj = ret.data.data;
                    //me.btcData = eval('(' + getTickerResultObj['0'] + ')');
                }
            }, function (response) {
                console.log('get in progress:' + response.length);
                me.getTickerResult = "bytes received:" + response.length;
            });
            stream.fetch({
                method: 'GET',
                url: ticker_id_URL,
                type: 'json'
            }, function (ret) {
                if (!ret.ok) {
                    me.getTickerIDResult = "request failed";
                } else {
                    console.log('get:' + ret);
                    me.getTickerIDResult = JSON.stringify(ret.data);
                    me.items.push(ret.data.data);
                }
            }, function (response) {
                console.log('get in progress:' + response.length);
                me.getTickerIDResult = "bytes received:" + response.length;
            });
            stream.fetch({
                method: 'GET',
                url: ticker_id_cny_URL,
                type: 'json'
            }, function (ret) {
                if (!ret.ok) {
                    me.getTickerIdCnyResult = "request failed";
                } else {
                    console.log('get:' + ret);
                    me.getTickerIdCnyResult = JSON.stringify(ret.data);
                }
            }, function (response) {
                console.log('get in progress:' + response.length);
                me.getTickerIdCnyResult = "bytes received:" + response.length;
            });
            stream.fetch({
                method: 'GET',
                url: global_URL,
                type: 'json'
            }, function (ret) {
                if (!ret.ok) {
                    me.getGlobalResult = "request failed";
                } else {
                    console.log('get:' + ret);
                    me.getGlobalResult = JSON.stringify(ret.data);
                }
            }, function (response) {
                console.log('get in progress:' + response.length);
                me.getGlobalResult = "bytes received:" + response.length;
            });

        }
        }

    };
</script>

<style scoped>
  .group {
    margin-left:30px;
    margin-right:30px;
    margin-bottom:32px;
  }
  .title {
    font-size: 45px;
    color: #41B883;
  }
  .count {
    margin-top:6px;
    font-size: 28px;
    color: #888888;
  }
  .list {
    background-color: #F5F5F5;
  }
  .cell {
    flex-direction: row;
    width: 690px;
    border-bottom-width: 2px;
    border-bottom-color: #F5F5F5;
    background-color: #FFFFFF;
    padding-top: 35px;
    padding-bottom: 25px;
    justify-content: center;
    align-items: center;
  }
  .A-title {
    font-size: 40px;
    width: 150px;

  }
  .A-desc {
    color: #999;
    font-size: 40px;
    padding-left: 30px;
    padding-right: 30px;
    width: 300px;
  }
  .A-comment {
    color: #52bfe6;
    font-size: 40px;
    text-align: right;
    padding-right: 50px;
    width: 240px;
  }
</style>

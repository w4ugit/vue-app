<template>
  <div>
    <loading :active.sync="isLoading"
             :can-cancel="false"
             :is-full-page="true"
             :color="color"></loading>
    <div class="header-main">
      <a href="javascript:;" v-on:click="showMenu" class="btn-menu">
        <i></i>
        <i></i>
        <i></i>
      </a>
      <div class="article">{{title}}</div>
      <a href="#" @click.prevent="showCancel = true" class="cancel-btn-header" v-show="cancel">{{$ml.get('CANCEL_2')}}</a>
    </div>
    <main-menu v-bind:show-menu="show_menu" v-on:closeMenu="showMenu" v-on:swipeHandler="swipeHandler" v-bind:propActiveOrder="activeOrder"></main-menu>
    <div id="map"></div>
    <div class="map-trip-main" v-show="step.step1">
      <div class="order-trip location-wrapper" v-bind:class="{'main-order': order2step}">
        <div class="box-location">
          <form class="left-q">
            <a href="javascript:;" v-on:click="selectLocation('from')" class="one-block from-bl">
              <div class="mark-location not-active" v-text="from"></div>
              <i class="fashion-8"></i>
            </a>
            <a href="javascript:;" class="one-block to-bl not-after" v-on:click="selectLocation('to')">
              <div class="mark-location not-active" v-text="to"></div>
              <i class="fashion-9"></i>
            </a>
            <div class="block-input" v-show="order2step">
              <i class="fashion-23"></i>
              <input type="text" :placeholder="$ml.get('YOU_PROP_PRICE')" v-model="price" v-on:focus="showPrice" class="placeholder-grey all-number" maxlength="22">
              <a href="javascript:;" class="clear-input-btn" @click="price = ''" v-show="price !== ''">
                <i class="fashion-1"></i>
              </a>
            </div>
            <div class="block-input" v-show="order2step">
              <i class="fashion-21"></i>
              <input type="text" :placeholder="$ml.get('COMMENT_FROM_DRIVER')" v-model="comment" v-on:focus="comments.showComment = true" class="placeholder-grey">
              <a href="javascript:;" class="clear-input-btn" @click="comment = ''" v-show="comment !== ''">
                <i class="fashion-1"></i>
              </a>
            </div>
            <button class="btn-brand" type="button" v-on:click="saveOrder" v-show="order2step">Замовити</button>
          </form>
        </div>
      </div>
    </div>
    <select-address
      v-bind:show-address="select_address"
      v-on:closeAddress="selectLocation"
      v-on:searchItems="searchItems"
      v-bind:items="citiesObject"
      v-bind:showFavorite="showFavorite"
      v-on:saveAddress="saveAddress"
    ></select-address>
    <price
      v-bind:showPanel="showPanel"
      v-bind:price="recPrice"
      v-on:closePrice="closePrice"
      v-on:setPrice="setPrice"
      v-on:setTypePay="setTypePay"
    ></price>
    <div class="order-trip-s" v-bind:class="{ open: comments.showComment }">
      <!--Хедер-->
      <div class="header">
        <a href="javascript:;" v-on:click="comments.showComment = false" class="arrow-left-btn"><i class="fashion-4"></i></a>
        <div class="article-header">{{this.$ml.get('SET_COMMENT')}}</div>
      </div>
      <!--/Хедер-->

      <form v-show="step.step1" class="comment-for-driver">
        <div class="textarea-wrapper">
          <i class="fashion-21"></i>
          <textarea class="text-resize" style="resize: none; overflow-y: hidden; position: absolute; top: 0px; left: -9999px; height: 50px; width: 458px; line-height: normal; text-decoration: none solid rgb(0, 0, 0); letter-spacing: 0px;" tabindex="-1"></textarea><textarea :placeholder="$ml.get('COMMENT_FROM_DRIVER')" v-model="order.comment" class="text-resize" style="resize: none; overflow-y: hidden;"></textarea>
        </div>
        <div class="more-four-passenger">
          <i class="fashion-22"></i>
          <span>{{$ml.get('MIN_4')}}</span>
          <ul class="checkbox-button-one">
            <li>
              <label class="checkbox">
                <input type="checkbox" v-model="order.more.min_4">
                <span class="checkbox-text"></span>
              </label>
            </li>
          </ul>
        </div>
        <div class="more-four-passenger">
          <i class="fashion-33"></i>
          <span>{{$ml.get('BUKS')}}</span>
          <ul class="checkbox-button-one">
            <li>
              <label class="checkbox">
                <input type="checkbox" v-model="order.more.buks">
                <span class="checkbox-text"></span>
              </label>
            </li>
          </ul>
        </div>
        <div class="more-four-passenger">
          <i class="fashion-34"></i>
          <span>{{$ml.get('SMOK')}}</span>
          <ul class="checkbox-button-one">
            <li>
              <label class="checkbox">
                <input type="checkbox" v-model="order.more.smok">
                <span class="checkbox-text"></span>
              </label>
            </li>
          </ul>
        </div>
        <div class="more-four-passenger">
          <i class="fashion-35"></i>
          <span>{{$ml.get('DELIVERY')}}</span>
          <ul class="checkbox-button-one">
            <li>
              <label class="checkbox">
                <input type="checkbox" v-model="order.more.delivery">
                <span class="checkbox-text"></span>
              </label>
            </li>
          </ul>
        </div>
        <a href="#" @click.prevent="setComment" class="btn-brand">{{$ml.get('COMPLETE')}}</a>
      </form>
    </div>
    <div class="map-trip-main" v-show="step.step2">
      <div class="box-driver-main raising-box">
        <div class="one-order">
          <div class="location-wrapper">
            <div class="box-location">
              <div class="left-q">
                <div class="one-block from-bl">
                  <div class="mark-location">{{from}}</div>
                </div>
                <div class="one-block to-bl">
                  <div class="mark-location">{{to}}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="up-price">
          {{this.$ml.get('GO_UP_PRICE')}}
        </div>
        <div class="line-price-way">
          <a href="javascript:;" @click="minus5" class="btn-minus center-flex-full">- 5 {{this.$ml.get('CURRENCY')}}</a>
          <div class="input-value">
            <input type="text" placeholder="0" v-model="order.price" class="price-trip">
            <span>{{this.$ml.get('CURRENCY')}}</span>
          </div>
          <a href="javascript:;" @click="plus5" class="btn-plus center-flex-full">+ 5 {{this.$ml.get('CURRENCY')}}</a>
        </div>
      </div>
    </div>
    <!--Коли хтось прийняв замовлення-->
    <div class="map-trip-main" v-show="step.step3">
      <div class="box-driver-main">
        <div class="arrival-text">{{$ml.get('TRANSFER_ZA')}} ~ {{driver.time}} {{$ml.get('HV')}}</div>
        <div class="arrival-panel">
          <a href="#" class="mail-main center-flex-full"><i class="fashion-24"></i></a>
          <div class="box-user-wrapper">
            <div class="box-img">
              <img :src="driver.photo" alt="img" class="object-fit">
            </div>
            <div class="star-client">{{driver.rating}}</div>
            <div class="car-client">{{driver.marka}}</div>
            <div class="type-car">{{driver.color}}, {{driver.number}}</div>
          </div>
          <a :href="'tel:+38' + driver.phone" class="phone-main center-flex-full"><i class="fashion-25"></i></a>
        </div>
      </div>
    </div>
    <!--Коли водій підїхав-->
    <div class="map-trip-main" v-show="step.step4">
      <div class="white-box-main">
        <div class="article">{{$ml.get('DRIVER_HERE')}}</div>
        <div class="line-two-block">
          <div class="left-q">{{driver.marka}}, {{driver.color}}, {{driver.number}}</div>
          <div class="right-q">{{price}} {{$ml.get('CURRENCY')}}</div>
        </div>
        <button class="btn-brand" v-show="showIdu" @click="idu">{{$ml.get('IDU')}}</button>
      </div>
    </div>
    <!--Коли оплата картою-->
    <div class="map-trip-main" v-show="step.step5">
      <div class="white-box-main">
        <div class="article">Оплата</div>
        <div class="line-two-block">
          <div class="left-q">Вартість поїздки</div>
          <div class="right-q">100 грн</div>
        </div>
        <button class="btn-brand" v-show="pay.first">Оплата</button>
        <button class="btn-brand" v-show="pay.second">Повторити оплату</button>
      </div>
    </div>
    <socket
      v-on:confirmOrder="confirmOrder"
      v-on:driverHere="driverHere"
      v-on:endOrder="endOrder"
    ></socket>
    <div class="order-trip-s" v-bind:class="{ open: showCancel }">
      <!--Хедер-->
      <div class="header">
        <a href="#" @click.prevent="showCancel = false" class="arrow-left-btn"><i class="fashion-4"></i></a>
        <div class="article-header">{{$ml.get('CANCELATION')}}</div>
      </div>
      <!--/Хедер-->

      <div class="response-page">

        <div class="response-article">{{$ml.get('NOTE_CANCEL')}}</div>
        <div class="under-article">{{$ml.get('TEXT_CANCEL')}}</div>
        <form action="">
          <ul class="checkbox-button-one2">
            <li>
              <label class="checkbox">
                <input type="radio" name="cancellation" :value="$ml.get('REAZON_CANCEL_1')" v-model="reazon">
                <span class="checkbox-text">{{$ml.get('REAZON_CANCEL_1')}}</span>
              </label>
            </li>
            <li>
              <label class="checkbox">
                <input type="radio" name="cancellation" :value="$ml.get('REAZON_CANCEL_2')" v-model="reazon">
                <span class="checkbox-text">{{$ml.get('REAZON_CANCEL_2')}}</span>
              </label>
            </li>
            <li>
              <label class="checkbox">
                <input type="radio" name="cancellation" :value="$ml.get('REAZON_CANCEL_3')" v-model="reazon">
                <span class="checkbox-text">{{$ml.get('REAZON_CANCEL_3')}}</span>
              </label>
            </li>
            <li>
              <label class="checkbox">
                <input type="radio" name="cancellation" :value="$ml.get('REAZON_CANCEL_4')" v-model="reazon">
                <span class="checkbox-text">{{$ml.get('REAZON_CANCEL_4')}}</span>
              </label>
            </li>
            <li>
              <label class="checkbox your-version">
                <input type="radio" name="cancellation" :value="$ml.get('REAZON_CANCEL_5')" v-model="reazon">
                <span class="checkbox-text">{{$ml.get('REAZON_CANCEL_5')}}</span>
              </label>
            </li>
          </ul>
          <textarea :placeholder="$ml.get('REAZON_CANCEL_5')" v-model="reazontext" class="textarea-custom"></textarea>
          <button @click.prevent="cancelOrder" class="btn-brand">{{$ml.get('SEND')}}</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker, LControlZoom, LCircleMarker, LIcon } from 'vue2-leaflet'
import L from 'leaflet'
import axios from 'axios'
import { setTimeout } from 'timers'
import Loading from 'vue-loading-overlay'
import 'vue-loading-overlay/dist/vue-loading.css'
import { OpenStreetMapProvider } from 'leaflet-geosearch'
require('leaflet.marker.slideto')
require('leaflet-routing-machine')
const provider = new OpenStreetMapProvider()

export default {
  name: 'home',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LControlZoom,
    LCircleMarker,
    LIcon,
    Loading
  },
  data () {
    return {
      title: this.$ml.get('homeTitle'),
      url: 'http://85.10.194.125:4444/styles/osm-bright/{z}/{x}/{y}.png',
      zoom: 17,
      center: [50.446829, 30.554954],
      my: [50.446829, 30.554954],
      city: false,
      citiesObject: null,
      show_menu: false,
      select_address: false,
      timer: null,
      showFavorite: false,
      isLoading: true,
      color: '#ff6214',
      q: '',
      from: this.$ml.get('FROM'),
      to: this.$ml.get('TO'),
      activePoint: null,
      order2step: false,
      coords: {
        from: {
          lat: null,
          lng: null
        },
        to: {
          lat: null,
          lng: null
        }
      },
      distance: 0,
      price: '',
      comment: '',
      showPanel: false,
      showComment: false,
      recPrice: 0,
      typePay: null,
      comments: {
        showComment: false
      },
      step: {
        step1: true,
        step2: false,
        step3: false,
        step4: false,
        step5: false
      },
      driver: {
        time: 0,
        photo: '',
        rating: 0,
        marka: '',
        color: '',
        number: '',
        phone: ''
      },
      order: {
        id: 0,
        price: 0,
        type_pay: 0,
        comment: '',
        more: {
          min_4: false,
          buks: false,
          smok: false,
          delivery: false
        }
      },
      showIdu: true,
      pay: {
        first: true,
        second: false
      },
      activeOrder: true,
      cancel: false,
      showCancel: false,
      reazon: '',
      reazontext: ''
    }
  },
  mounted () {
    let self = this
    let myMap = L.map('map', {
      zoomControl: false
    })
    let iMarker = false
    let dMarker = false
    let myIcon = L.divIcon({className: 'my-div-icon'})

    let driverIcon = L.icon({
      iconUrl: 'http://express.w4u.pp.ua/img/Car.png',
      iconSize: [20, 30],
      iconAnchor: [5, 10],
      popupAnchor: [-3, -76],
      shadowSize: [68, 95],
      shadowAnchor: [22, 94]
    })
    this.city = JSON.parse(localStorage.profile).city_id
    this.q = this.$ml.get('SEARCH')
    navigator.geolocation.getCurrentPosition(function (res) {
      self.center = [res.coords.latitude, res.coords.longitude]
      self.my = [res.coords.latitude, res.coords.longitude]
      myMap.setView(self.center, 18)
      L.tileLayer('http://85.10.194.125:4444/styles/osm-bright/{z}/{x}/{y}.png').addTo(myMap)
      iMarker = L.marker(self.center, {icon: myIcon}).addTo(myMap)
      axios
        .post(self.$root.apiLink + 'geo/street', {
          q: '',
          lang: self.$ml.current,
          place: self.city
        }, {
          headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
        })
        .then(function (response) {
          if (response.data.status === 1) {
            self.cancel = true
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = true
            self.from = response.data.data.location_from
            self.to = response.data.data.location_to
            self.order.id = response.data.data.id
            self.order.price = response.data.data.price
            if (response.data.data.from_lat !== null && response.data.data.from_lng !== null) {
              myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
              iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
            } else {
              self.$toast.error({
                title: self.$ml.get('error'),
                message: 'Помилка визначення місця положення'
              })
            }
          } else if (response.data.status === 2) {
            self.cancel = true
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = true
            self.from = response.data.data.location_from
            self.to = response.data.data.location_to
            self.price = response.data.data.price
            myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
            iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
            self.driver.time = response.data.data.time
            self.driver.photo = self.$root.host + 'source/user/' + response.data.driver.photo
            self.driver.marka = response.data.auto.marka
            self.driver.color = response.data.auto.color
            self.driver.number = response.data.auto.number
            self.order.id = response.data.data.id
            // показуємо маркер шофера
            dMarker = L.marker(response.data.position, {icon: driverIcon}).addTo(myMap)
            myMap.fitBounds([[response.data.data.from_lat, response.data.data.from_lng], response.data.position])
            // запускаємо функцію, яка буде слідкувати за шофером
            setTimeout(function () {
              self.mMarker(dMarker, true)
            }, 10000)
          } else if (response.data.status === 3) {
            self.cancel = true
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = false
            self.step.step4 = true
            self.price = response.data.data.price
            self.driver.marka = response.data.auto.marka
            self.driver.color = response.data.auto.color
            self.driver.number = response.data.auto.number
            self.order.id = response.data.data.id
            self.order.type_pay = response.data.data.type_pay
            myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
            iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
            // показуємо маркер шофера
            dMarker = L.marker(response.data.position, {icon: driverIcon}).addTo(myMap)
            myMap.fitBounds([[response.data.data.from_lat, response.data.data.from_lng], response.data.position])
            // запускаємо функцію, яка буде слідкувати за шофером
            setTimeout(function () {
              self.mMarker(dMarker, true)
            }, 10000)
          } else if (response.data.status === 4) {
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = false
            self.step.step4 = false
            self.step.step5 = true
            self.order.id = response.data.data.id
            self.price = response.data.data.price
            myMap.setView([response.data.data.to_lat, response.data.data.to_lng], 15)
            if (response.data.data.type_pay === 1) {
              self.pay.first = false
            }
          } else if (response.data.status === 5) {
            self.activeOrder = false
            self.$router.push({path: '/home/end/' + response.data.id})
          } else {
            self.activeOrder = true
            self.citiesObject = response.data.data
          }
          self.isLoading = false
        })
    }, function (res) {
      self.isLoading = false
      self.center = [50.747137, 25.325389]
      self.my = [50.747137, 25.325389]
      myMap.setView(self.center, 18)
      L.tileLayer('http://85.10.194.125:4444/styles/osm-bright/{z}/{x}/{y}.png').addTo(myMap)
      iMarker = L.marker(self.center, {icon: myIcon}).addTo(myMap)
      axios
        .post(self.$root.apiLink + 'geo/street', {
          q: '',
          lang: self.$ml.current,
          place: self.city
        }, {
          headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
        })
        .then(function (response) {
          if (response.data.status === 1) {
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = true
            self.from = response.data.data.location_from
            self.to = response.data.data.location_to
            self.price = response.data.data.price
            self.order.id = response.data.data.id
            myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
            iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
          } else if (response.data.status === 2) {
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = true
            self.from = response.data.data.location_from
            self.to = response.data.data.location_to
            self.price = response.data.data.price
            myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
            iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
            self.driver.time = response.data.data.time
            self.driver.photo = self.$root.host + 'source/user/' + response.data.driver.photo
            self.driver.marka = response.data.auto.marka
            self.driver.color = response.data.auto.color
            self.driver.number = response.data.auto.number
            self.order.id = response.data.data.id
            // показуємо маркер шофера
            dMarker = L.marker(response.data.position, {icon: driverIcon}).addTo(myMap)
            myMap.fitBounds([[response.data.data.from_lat, response.data.data.from_lng], response.data.position])
            // запускаємо функцію, яка буде слідкувати за шофером
            setTimeout(function () {
              self.mMarker(dMarker, true)
            }, 10000)
          } else if (response.data.status === 3) {
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = false
            self.step.step4 = true
            self.price = response.data.data.price
            self.driver.marka = response.data.auto.marka
            self.driver.color = response.data.auto.color
            self.driver.number = response.data.auto.number
            self.order.id = response.data.data.id
            self.order.type_pay = response.data.data.type_pay
            myMap.setView([response.data.data.from_lat, response.data.data.from_lng], 15)
            iMarker.setLatLng([response.data.data.from_lat, response.data.data.from_lng])
            // показуємо маркер шофера
            dMarker = L.marker(response.data.position, {icon: driverIcon}).addTo(myMap)
            myMap.fitBounds([[response.data.data.from_lat, response.data.data.from_lng], response.data.position])
            // запускаємо функцію, яка буде слідкувати за шофером
            setTimeout(function () {
              self.mMarker(dMarker, true)
            }, 10000)
          } else if (response.data.status === 4) {
            self.activeOrder = false
            self.step.step1 = false
            self.step.step2 = false
            self.step.step3 = false
            self.step.step4 = false
            self.step.step5 = true
            self.order.id = response.data.data.id
            self.price = response.data.data.price
            myMap.setView([response.data.data.to_lat, response.data.data.to_lng], 15)
            if (response.data.data.type_pay === 1) {
              self.pay.first = false
            }
          } else if (response.data.status === 5) {
            self.activeOrder = false
            self.$router.push({path: '/home/end/' + response.data.id})
          } else {
            self.activeOrder = true
            self.citiesObject = response.data.data
          }
          self.isLoading = false
        })
    })
  },
  methods: {
    zoomUpdated (zoom) {
      this.zoom = zoom
    },
    centerUpdated (center) {
      this.center = center
    },
    boundsUpdated (bounds) {
      this.bounds = bounds
    },
    selectLocation: function (event) {
      this.activePoint = event
      this.select_address = !this.select_address
    },
    searchItems: function (event) {
      let self = this
      if (this.timer !== null) {
        clearTimeout(this.timer._id)
      }
      if ((event.q).indexOf(',') === -1) {
        self.q = event.q
        this.timer = setTimeout(function () {
          alert(event.q)
          self.isLoading = true
          axios.post(self.$root.apiLink + 'geo/street', {
            q: event.q,
            lang: self.$ml.current,
            place: self.city
          }, {
            headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
          }).then(function (response) {
            self.isLoading = false
            self.citiesObject = response.data.data
          })
        }, 1000)
      } else {
        self.citiesObject.forEach(function (e) {
          if (e.name.indexOf(event.q) === -1) {
            e.show = false
          } else {
            e.show = true
          }
        })
      }
    },
    saveAddress: function (event) {
      let self = this
      if ((event.name).indexOf(',') === -1) {
        axios
          .post(this.$root.apiLink + 'geo/home', {
            street: event.id,
            lang: this.$ml.current
          })
          .then(function (response) {
            self.citiesObject = response.data
          })
      } else {
        this.q = this.$ml.get('SEARCH')
        if (this.activePoint === 'from') {
          this.from = event.name
        } else {
          this.to = event.name
        }
        this.select_address = false
        if (this.from !== '' && this.from !== this.$ml.get('FROM') && this.to !== '' && this.to !== this.$ml.get('TO')) {
          this.order2step = true
          axios
            .post(this.$root.apiLink + 'geo/get-city-id', {
              id: this.city,
              lang: this.$ml.current
            })
            .then(function (response) {
              let cityName = response.data
              provider.search({query: self.$ml.get('COUNTRY') + ',+' + cityName + ',+' + self.from}).then(function (response) {
                if (response.length === 0) {
                  self.$toast.error({
                    title: self.$ml.get('error'),
                    message: self.$ml.get('ERROR_FROM_KOORDINATES')
                  })
                }
                self.coords.from.lat = response[0].y
                self.coords.from.lng = response[0].x

                provider.search({query: self.$ml.get('COUNTRY') + ',+' + cityName + ',+' + self.to}).then(function (response) {
                  if (response.length === 0) {
                    self.$toast.error({
                      title: self.$ml.get('error'),
                      message: self.$ml.get('ERROR_TO_KOORDINATES')
                    })
                  }
                  self.coords.to.lat = response[0].y
                  self.coords.to.lng = response[0].x

                  axios
                    .get('http://85.10.194.125:5000/route/v1/driving/' + self.coords.from.lng + ',' + self.coords.from.lat + ';' + self.coords.to.lng + ',' + self.coords.to.lat)
                    .then(function (response) {
                      if (response.data.code !== 'Ok') {
                        this.$toast.error({
                          title: self.$ml.get('error'),
                          message: self.$ml.get('ERROR_ROUTE')
                        })
                      }
                      self.distance = response.data.routes[0].distance
                    })
                })
              })
            })
        }
        axios
          .post(this.$root.apiLink + 'geo/street', {
            q: '',
            lang: this.$ml.current,
            place: this.city
          }, {
            headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
          })
          .then(response => (
            self.citiesObject = response.data.data
          ))
      }
    },
    showMenu: function () {
      this.show_menu = !this.show_menu
    },
    swipeHandler (direction) {
      if (direction === 'left') {
        this.show_menu = !this.show_menu
      }
    },
    showPrice: function () {
      this.recPrice = Math.ceil(Number(this.$root.settings[1]) + Number(this.$root.settings[2] * (this.distance / 1000)))
      this.showPanel = !this.showPanel
    },
    setPrice: function (price) {
      this.price = price + ' грн'
    },
    closePrice: function () {
      this.showPanel = !this.showPanel
    },
    setTypePay: function (type) {
      this.typePay = type
      this.price = this.price + ', ' + (type === 'cash' ? 'готівка' : 'картка')
      this.showPanel = false
    },
    saveOrder: function () {
      if (this.city_id === '' || this.from === '' || this.to === '' || this.price === '') {
        this.$toast.error({
          title: this.$ml.get('error'),
          message: this.$ml.get('writeDataRegister')
        })
      } else {
        this.isLoading = true
        let self = this
        axios
          .post(this.$root.apiLink + 'user/create-order', {
            city_id: this.city,
            from: this.from,
            to: this.to,
            coords: this.coords,
            distance: this.distance,
            price: this.price,
            comment: this.comment
          }, {
            headers: {
              'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)
            }
          })
          .then(function (response) {
            self.order.id = response.data
            self.isLoading = false
            self.step.step1 = false
            self.step.step2 = true
            self.order.price = ((self.price).split(' '))[0]
            self.activeOrder = true
            self.cancel = true
          })
          .catch(function (error) {
            self.isLoading = false
            self.$toast.error({
              title: self.$ml.get('error'),
              message: error.response.data.message
            })
          })
      }
    },
    confirmOrder: function (data) {
      let photo = data.driver.photo
      this.driver = data.driver
      this.driver.photo = this.$root.host + 'source/user/' + photo
      this.driver.time = data.order.time
      this.step.step1 = false
      this.step.step2 = false
      this.step.step3 = true
    },
    mMarker: function (marker, repeat) {
      marker.slideTo([50.767744, 25.372573], {duration: 2000})
    },
    driverHere: function (data) {
      this.driver = data.driver
      this.step.step1 = false
      this.step.step2 = false
      this.step.step3 = false
      this.step.step4 = true
    },
    idu: function () {
      let self = this
      axios
        .post(this.$root.apiLink + 'index/idu', {
          id: self.order.id
        }, {
          headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
        })
        .then(function (res) {
          if (self.order.type_pay === 1) {
            self.showIdu = false
            self.step.step4 = false
          } else {
            self.step.step4 = false
            self.step.step5 = true
          }
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    endOrder: function (data) {
      this.$router.push({path: '/home/end/' + data.id})
    },
    setComment: function () {
      let c = []
      if (this.order.comment !== '') {
        c.push(this.order.comment)
      }
      if (this.order.more.min_4) {
        c.push(this.$ml.get('MIN_4'))
      }
      if (this.order.more.buks) {
        c.push(this.$ml.get('BUKS'))
      }
      if (this.order.more.smok) {
        c.push(this.$ml.get('SMOK'))
      }
      if (this.order.more.delivery) {
        c.push(this.$ml.get('DELIVERY'))
      }
      this.comment = c.join(', ')
      this.comments.showComment = false
    },
    minus5: function () {
      let self = this
      axios
        .post(this.$root.apiLink + 'index/change-price', {
          id: self.order.id,
          price: self.order.price - 5
        }, {
          headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
        })
        .then(function () {
          self.order.price = Number(self.order.price) - 5
        })
        .catch(function (response) {
          self.$toast.error({
            title: self.$ml.get('error'),
            message: self.$ml.get(response.data)
          })
        })
    },
    plus5: function () {
      let self = this
      axios
        .post(this.$root.apiLink + 'index/change-price', {
          id: self.order.id,
          price: self.order.price + 5
        }, {
          headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
        })
        .then(function () {
          self.order.price = Number(self.order.price) + 5
        })
        .catch(function (response) {
          self.$toast.error({
            title: self.$ml.get('error'),
            message: response.data
          })
        })
    },
    cancelOrder: function () {
      let self = this
      if (this.reazon === '' && this.reazontext === '') {
        self.$toast.error({
          title: self.$ml.get('error'),
          message: self.$ml.get('NULL_CANCEL_TEXT')
        })
      } else {
        axios
          .post(this.$root.apiLink + 'index/cancel-order', {
            id: self.order.id,
            comment: self.reazon !== '' ? self.reazon : self.reazontext
          }, {
            headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
          })
          .then(function (response) {
            console.log(response)
            self.activeOrder = false
            self.cancel = false
            self.activeOrder = true
            self.step.step1 = true
            self.step.step2 = false
            self.step.step3 = false
            self.step.step4 = false
            self.step.step5 = false
            self.order2step = false
            self.from = self.$ml.get('FROM')
            self.to = self.$ml.get('TO')
            self.price = ''
            self.comment = ''
            axios
              .post(self.$root.apiLink + 'geo/street', {
                q: '',
                lang: self.$ml.current,
                place: self.city
              }, {
                headers: {'Authorization': 'Bearer ' + (JSON.parse(localStorage.user).auth_key)}
              })
              .then(response => (
                self.citiesObject = response.data.data
              ))
            self.showCancel = false
          })
          .catch(function (error) {
            self.$toast.error({
              title: self.$ml.get('error'),
              message: error.data
            })
          })
      }
    }
  }
}
</script>

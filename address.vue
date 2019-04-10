<template>
  <div class="order-trip-s" v-bind:class="{ open: showAddress }" style="z-index: 21">
    <div class="header">
      <a href="javascript:;" v-on:click="closeAddress" class="arrow-left-btn close-address-trip"><i class="fashion-4"></i></a>
      <div class="article-header">{{title}}</div>
    </div>
    <div class="address-page">
      <div class="address-trip">
        <div class="box-search clearfix">
          <button class="search-btn"><i class="fashion-20"></i></button>
          <input type="text" placeholder="" @keyup="searchItems(searchString)" v-model="searchString">
          <a href="#" class="clear-search">
            <i></i>
            <i></i>
          </a>
        </div>
        <ul class="list-address-trip">
          <li v-for="item in items" :key="item.id" v-show="item.show">
            <i class="fashion-7"></i>
            <a href="javascript:;" v-on:click="saveAddress(item.id, item.name)"  class="name-trip">{{item.name}}</a>
            <a href="#" class="btn-favorite-trip" v-bind:class="{hide: showFavorite}"><i class="fashion-30"></i></a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      title: this.$ml.get('select_city'),
      searchString: null,
      coma: this.com
    }
  },
  props: {
    showAddress: {
      default: false,
      type: Boolean
    },
    items: {
      default: null
    },
    showFavorite: {
      default: true,
      type: Boolean
    },
    com: {
      default: true,
      type: Boolean
    }
  },
  methods: {
    closeAddress: function () {
      this.$emit('closeAddress')
    },
    searchItems: function () {
      this.$emit('searchItems', {q: this.searchString})
    },
    saveAddress: function (id, name) {
      if (name.indexOf(',') === -1) {
        if (this.coma) {
          this.searchString = name + ', '
        } else {
          this.searchString = name
        }
      } else {
        this.searchString = ''
      }
      this.$emit('saveAddress', {id: id, name: name})
    }
  }
}
</script>

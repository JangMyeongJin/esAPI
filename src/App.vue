<template>
  <div>

    <app-header></app-header>

    <div class="layout-contents">

      <!-- <search-form v-bind:APISERVER="APIserver" v-bind:INDEXLIST="indexList"></search-form> -->

      <div class="common-box2 portal-search">
        <div class="portal-search-label response-hide"><img src="../images/common/icon_portal_search.png" alt="My Image"
            style="position:absolute;left:40px;">SEARCH</div>
        <div class="portal-search-field">

          <!-- SearchForm -->
          <form id="prosearch" @submit.prevent="goSearch">
            <fieldset>
              <legend>통합검색</legend>
              <select id="searchType" class="search_selectType01" v-model="setSearchIndex">
                <option value="TOTAL" >통합검색</option>
                <option v-for="(item, i) in indexList" v-bind:key="i" :value="item" >{{ item }}</option>
              </select>

              <input type="search" id="query" class="total_search" input:value="query" @input="inputQuery"
                autocomplete="off" style="outline-width: 0; width:400px;">
              <button id="searchbtn" class="common-btn btn-color-normal portal-search-btn response-block">검색</button>
              <button id="detail_search" class="common-btn detail_search">상세검색</button>
            </fieldset>
          </form>
        </div>
      </div>

      <!-- autokeyword -->
      <auto-keyword ref="autoQuery" v-bind:APISERVER="APIserver"></auto-keyword>

      <!-- detailbox -->
      <div id="detailbox" class="detailbox" style="display:none">

      </div>
      <div class="portal-result">
        <div class="portal-result-length">
          <br>"{{ showQuery }}"에 대한 전체 {{ totalCount }}건의 검색결과가 있습니다.
        </div>
        <div class="common-tabgroup response-tabgroup-column2">
          <span :class="[searchIndex === 'TOTAL' ? 'on' : '']">
            <a href="#" name="TOTAL" @click="goIndexSearch('TOTAL')" value="TOTAL">통합검색 ({{ totalCount }})</a>
          </span>
          <span v-for="item in indexList" v-bind:key="item" :class="[searchIndex === item ? 'on' : '']" >
            <a href="#" :name="item" @click="goIndexSearch(item)">{{ item }} ({{}})</a>
          </span>
        </div>

        <result-data v-if="searchIndex=='TOTAL' || searchIndex=='data'" v-bind:dataProps="resultData" v-bind:searchIndex="searchIndex" @goIndexSearch="goIndexSearch"></result-data>
        <result-entp v-if="searchIndex=='TOTAL' || searchIndex=='entp'" v-bind:entpProps="resultEntp" v-bind:searchIndex="searchIndex" @goIndexSearch="goIndexSearch"></result-entp>

      </div>

      <app-side v-bind:mySearchKeyword="mySearchKeyword" @removeMyKeyword="removeMyKeyword"></app-side>

    </div>

  </div>
</template>

<script>
import lodash from 'lodash'
import axios from 'axios'
import AppHeader from './components/AppHeader.vue'
import AppSide from './components/AppSide.vue'
import ResultEntp from './components/result/ResultEntp.vue'
import ResultData from './components/result/ResultData.vue'
import AutoKeyword from './components/AutoKeyword.vue'

export default {
  data: function () {
    return {
      APIserver: 'http://127.0.0.1:6502/',
      APIurl: 'sample6/restSearch.jsp',
      indexList: {
        1: 'data',
        2: 'entp'
      },
      countList: {

      },
      query: '',
      setSearchIndex: 'TOTAL',
      searchIndex: '',
      showQuery: '',

      totalSearch: '',
      totalCount: '',

      resultData: '',
      resultEntp: '',


    }
  },
  components: {
    // 'search-form': SearchForm,
    'app-header': AppHeader,
    'app-side': AppSide,
    'result-entp': ResultEntp,
    'result-data': ResultData,
    'auto-keyword': AutoKeyword

  },
  methods: {

    inputIndex: function(event) {
      this.setSearchIndex = event.target.value
    },
    inputQuery: function(event) {
      this.query = event.target.value
    },
    goIndexSearch: function(idx){
      this.setSearchIndex = idx
      return this.goSearch()

    },

    //goSearch
    async goSearch() {
      this.searchIndex = this.setSearchIndex
      this.showQuery = this.query

      var url = this.APIserver + this.APIurl + '?query=' + this.query + '&index=' + this.searchIndex
      var vm = this
      console.log(url)
      axios.get(url)
        .then(function (response) {
          console.log(response.data.result)
          vm.totalCount = response.data.totalCount

          if(vm.searchIndex == 'TOTAL'){
            vm.resultData = response.data.result[0]
            vm.resultEntp = response.data.result[1]
          }

          if(vm.searchIndex == 'data'){
            vm.resultData = response.data.result[0]
          }

          if(vm.searchIndex == 'entp'){
            vm.resultEntp = response.data.result[0]
          }


        })

        .catch(function (error) {
          console.log(error)
        })
        this.getSearchMyKeyword(this.query, this.totalCount)
    },

    goKeywordSearch: function (query) {
      this.query = query
      this.setSearchIndex = 'TOTAL'
      this.goSearch()
    },


    //쿠키값 조회
    getCookie: function (c_name) {
      var i, x, y, cookies = document.cookie.split(";");
      for (i = 0; i < cookies.length; i++) {
        x = cookies[i].substring(0, cookies[i].indexOf("="));
        y = cookies[i].substring(cookies[i].indexOf("=") + 1);
        x = x.replace(/^\s+|\s+$/g, "");

        if (x == c_name) {
          return lodash.unescape(y);
        }
      }
    },
    //쿠키값 설정
    setCookie: function (c_name, value, exdays) {
      var exdate = new Date();
      exdate.setDate(exdate.getDate() + exdays);
      var c_value = lodash.escape(value) + ((exdays == null) ? "" : "; expires=" + exdate.toUTCString());
      document.cookie = c_name + "=" + c_value;
    },

    getSearchMyKeyword: function (keyword, totCount) {
      var MYKEYWORD_COUNT = 10; //내가 찾은 검색어 갯수 + 1
      var myKeyword = this.getCookie("mySearchKeyword");
      if (myKeyword == null) {
        myKeyword = "";
      }

      var myKeywords = myKeyword.split("^%");

      if (totCount > 0) {
        var existsKeyword = false;
        for (var i = 0; i < myKeywords.length; i++) {
          if (myKeywords[i] == keyword) {
            existsKeyword = true;
            break;
          }
        }

        if (!existsKeyword) {
          myKeywords.push(keyword);
          if (myKeywords.length == MYKEYWORD_COUNT) {
            myKeywords = myKeywords.slice(1, MYKEYWORD_COUNT);
          }
        }
        this.setCookie("mySearchKeyword", myKeywords.join("^%"), 365);
      }

      this.searchMyKeyword(myKeywords.reverse());
    },

    searchMyKeyword: function (myKeywords) {
      var str = "<li class=\"tit\"><div class=\"ttxt\">내가 찾은 검색어</div></li>";

      for (var i = 0; i < myKeywords.length; i++) {
        if (myKeywords[i] == "")  continue;
        str += "<li class=\"searchkey\"><a href=\"#\"  @click=\"goKeywordSearch('" + myKeywords[i] + "')\">" + myKeywords[i] + "</a><a href=\"#\" class=\"btn_del\" @click=\"removeMyKeyword('" + myKeywords[i] + "')\" ></a></li>";
      }
      this.mySearchKeyword = str;
    },

    removeMyKeyword: function (keyword) {
      alert("1")
      var myKeyword = this.getCookie("mySearchKeyword");
      if( myKeyword == null) {
        myKeyword = "";
      }

      var myKeywords = myKeyword.split("^%");

      var i = 0;
      while (i < myKeywords.length) {
        if (myKeywords[i] == keyword) {
          myKeywords.splice(i, 1);
        } else {
          i++;
        }
      }

      this.setCookie("mySearchKeyword", myKeywords.join("^%"), 365);

      this.searchMyKeyword(myKeywords);
    }

  },
  created() {
    this.goSearch()
  },
  watch: {
    query: function () {
      this.$refs.autoQuery.reciveQuery(this.query);
    }
  }

}
</script>
<style>
@import '../css/common.css';
@import '../css/normalize.css';
@import '../css/sub_layout.css';
@import '../css/autoquery.css';
</style>
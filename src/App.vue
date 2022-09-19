<template>
  <div>

    <app-header></app-header>

    <div class="layout-contents">
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

              <input type="search" id="query" class="total_search" input:value="setQuery" @input="inputQuery" @focusin="autoShow" @focusout="autoHide"
                autocomplete="off" style="outline-width: 0; width:400px;">
              <button id="searchbtn" class="common-btn btn-color-normal portal-search-btn response-block" @click.self.prevent="goSearch">검색</button>
              <button id="detail_search" class="common-btn detail_search" @click.self.prevent="changeDetail">상세검색</button> <!--클릭할 때 submit 안되게 하려면 click.self.prevent-->
            </fieldset>
          </form>
        </div>
      </div>

      <!-- autokeyword -->
      <auto-keyword ref="autoQuery" v-bind:APISERVER="APIserver" @goKeywordSearch="goKeywordSearch" v-show="auto"></auto-keyword>

      <!-- detailbox -->
      <div id="detailbox" class="detailbox" v-show="detail">
        <div class="innerbox">
          <div class="listbox">
            <ul>
              <li class="item list">
                <span class="item title"><h4>검색기간</h4></span>

                <input type="text" id="sDate" class="sDate" v-model="sDate" autocomplete="off" > ~ <input type="text" id="eDate" class="eDate" v-model="eDate" autocomplete="off">

                <input type="radio" id="dateAll" name="date" @click="changeDate" value="TOTAL"  ><label for="dateAll">전체기간</label>
                <input type="radio" id="dateYear" name="date" @click="changeDate" value="year"><label for="dateYear">1년</label>
                <input type="radio" id="dateMonth" name="date" @click="changeDate" value="month"><label for="dateMonth">1개월</label>
                <input type="radio" id="dateWeek" name="date" @click="changeDate" value="week"><label for="dateWeek">1주</label>
              </li>
              <li class="item list">
                <span class="item title"><h4>검색범위</h4></span>
                <input type="checkbox" id="rangeAll" name="sfield"     v-model="sfieldTOTAL"  ><label for="rangeAll">전체</label>
                <input type="checkbox" id="rangeSubject" name="sfield" v-model="sfieldList" value="subject"><label for="rangeSubject">제목</label>
                <input type="checkbox" id="rangeContent" name="sfield" v-model="sfieldList" value="content"><label for="rangeContent">본문</label>
                <input type="checkbox" id="rangeFile" name="sfield"    v-model="sfieldList" value="file_nm"><label for="rangeFile">첨부파일</label>
              </li>
              <li class="item list">
                <span class="item title"><h4>정렬방식</h4></span>
                <input type="radio" id="sortScore" name="sort" @click="changeSort" value="SCORE/DESC"><label for="sortScore">정확도순</label>
                <input type="radio" id="sortDateDESC" name="sort" @click="changeSort" value="acct_year/DESC"><label for="sortDateDESC">최신순</label>
                <input type="radio" id="sortDateASC" name="sort" @click="changeSort" value="acct_year/ASC"><label for="sortDateASC">오래된순</label>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="portal-result">
        <div class="portal-result-length">
          <br>"{{ realQuery }}"에 대한 전체 {{ totalCount }}건의 검색결과가 있습니다.
        </div>
        <div class="common-tabgroup response-tabgroup-column2">
          <span :class="[searchIndex == 'TOTAL' ? 'on' : '']">
            <a href="#" name="TOTAL" @click="goIndexSearch('TOTAL')" value="TOTAL">통합검색 ({{ totalCount }})</a>
          </span>
          <span v-for="item in indexList" v-bind:key="item" :class="[searchIndex == item ? 'on' : '']" >
            <a href="#" :name="item" @click="goIndexSearch(item)">{{ item }} ({{}})</a>
          </span>
        </div>

        <result-data v-if="searchIndex=='TOTAL' || searchIndex=='data'" v-bind:dataProps="resultData" v-bind:searchIndex="searchIndex" @goIndexSearch="goIndexSearch"></result-data>
        <result-entp v-if="searchIndex=='TOTAL' || searchIndex=='entp'" v-bind:entpProps="resultEntp" v-bind:searchIndex="searchIndex" @goIndexSearch="goIndexSearch"></result-entp>

      </div>

      <app-side ref="appSide" v-bind:APISERVER="APIserver" @goKeywordSearch="goKeywordSearch"></app-side>

    </div>

  </div>
</template>

<script>
import axios from 'axios'
import AppHeader from './app-header/AppHeader.vue'
import AppSide from './app-side/AppSide.vue'
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

      //parameter setting
      setQuery: '',
      setSearchIndex: 'TOTAL',
      setDate: 'TOTAL',
      dateRangeField: '@acct_year,',
      sDate:'',
      eDate:'',
      sfieldRange: '',
      sfieldTOTAL: true,
      sfieldList: [],
      sortRange: 'SCORE/DESC',

      //real parameter
      searchIndex: '',
      realQuery: '',
      dateRange: '',
      sfield:'',
      sort:'',

      totalSearch: '',
      totalCount: '',

      resultData: '',
      resultEntp: '',

      auto: false,
      detail: false
    }
  },
  components: {
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
      this.setQuery = event.target.value
    },
    goIndexSearch: function(idx){
      this.setSearchIndex = idx
      return this.goSearch()
    },
    autoShow: function() {
      this.auto = true
    },
    autoHide: function() {
      this.auto = false
    },
    changeDetail: function(){
      if(this.detail) {
        this.detail = false
      }else if(!this.detail) {
        this.detail = true
      }
    },
    changeDate: function(event){
      this.setDate = event.target.value

      var now = new Date()
      var today = new Date().toJSON().slice(0,10).replace(/-/g,'/')

      if(this.setDate=='TOTAL'){
        this.sDate = ''
        this.eDate =''
      }else if(this.setDate=='year') {
        this.sDate = new Date(now.setFullYear(now.getFullYear() - 1)).toJSON().slice(0,10).replace(/-/g,'/')
        this.eDate = today
      }else if(this.setDate=='month'){
        this.sDate = new Date(now.setMonth(now.getMonth() - 1)).toJSON().slice(0,10).replace(/-/g,'/')
        this.eDate = today
      }else if(this.setDate=='week'){
        this.sDate = new Date(now.setDate(now.getDate() - 7)).toJSON().slice(0,10).replace(/-/g,'/')
        this.eDate = today
      }
    },
    changeSort: function(event) {
      this.sortRange = event.target.value
    },

    //goSearch
    async goSearch() {

      if(this.sfieldList.length > 0){
        this.sfieldRange = ''
        for(var field of this.sfieldList.values()){
          this.sfieldRange += field + ','
        }
      }

      this.searchIndex = this.setSearchIndex
      this.realQuery = this.setQuery

      this.dateRange = (this.sDate != '' && this.eDate != '') ? this.searchIndex + this.dateRangeField + this.sDate + ',' + this.eDate : ''
      this.sfield = (this.sfieldTOTAL) ? '' : this.searchIndex + '@' + this.sfieldRange
      this.sort = this.searchIndex + '@' + this.sortRange

      var url = this.APIserver + this.APIurl + '?query=' + this.realQuery + '&index=' + this.searchIndex + '&daterange=' + this.dateRange + "&sfield=" + this.sfield + '&sort=' + this.sort
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

    },

    goKeywordSearch: function (query) {
      this.query = query
      this.setSearchIndex = 'TOTAL'
      this.goSearch()
    },




  },
  created() {
    this.goSearch()
  },
  watch: {
    setQuery: function () {
      this.$refs.autoQuery.getQuery(this.setQuery);
    },
    sfieldList: function() {
      this.sfieldTOTAL = (this.sfieldList.length > 0) ? false : true

    }

  },
  mounted() {
    //created()에서 자식 함수를 호출하면 호출X
    this.$refs.appSide.getSearchMyKeyword(this.query, this.totalCount)
  }

}
</script>
<style>
@import '../css/common.css';
@import '../css/normalize.css';
@import '../css/sub_layout.css';
@import '../css/autoquery.css';
</style>
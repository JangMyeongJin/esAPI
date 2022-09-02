<template>
    <div>
        <div class="common-box2 portal-search">
            <div class="portal-search-label response-hide"><img src="../../images/common/icon_portal_search.png" alt="My Image" style="position:absolute;left:40px;">SEARCH</div>
            <div class="portal-search-field">

                <!-- SearchForm -->
                <form id="prosearch" v-on:submit.prevent="goSearch">
                    <fieldset>
                        <legend>통합검색</legend>
                        <select id="searchType" class="search_selectType01" @change="inputIndex">
                            <option value="ALL">통합검색</option>
                            <option v-for="(item, i) in indexList" v-bind:key="i" :value="item">{{item}}</option>
                        </select>

                        <input type="search" id="query" class="total_search" input:value="query" @input="inputQuery" autocomplete="off"  style="outline-width: 0; width:400px;">
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
                <br>"{{showQuery}}"에 대한 전체 {{totalCount}}건의 검색결과가 있습니다.
            </div>
            <div class="common-tabgroup response-tabgroup-column2">
                <span><a href="#" name="ALL" onClick="" >통합검색 ({{totalCount}})</a></span>
                <span v-for="item in indexList" v-bind:key="item" ><a href="#">{{item}} ()</a></span>
            </div>
            <result-data v-bind:dataProps="resultData" v-bind:dataCount="countData"></result-data>
            <result-entp v-bind:entpProps="resultEntp" v-bind:entpCount="countEntp"></result-entp>

        </div>
    </div>
</template>

<script>
import axios from 'axios'
import ResultEntp from './result/ResultEntp.vue';
import ResultData from './result/ResultData.vue';
import AutoKeyword from './AutoKeyword.vue';

export default {
    data: function () {
        return {
            APIserver : this.APISERVER,
            APIurl : 'sample6/restSearch.jsp',
            query: '',
            indexList: this.INDEXLIST,
            searchIndex: '',
            showQuery: '',

            totalSearch: '',
            totalCount: '',

            resultData: '',
            countData: '',

            resultEntp: '',
            countEntp: ''

        }
    },
    props: ['APISERVER', 'INDEXLIST'],
    components: {
        'result-entp': ResultEntp,
        'result-data': ResultData,
        'auto-keyword': AutoKeyword
    },
    methods: {
        inputIndex(event){
            this.searchIndex = event.target.value
        },
        inputQuery(event){
            this.query = event.target.value
        },
        async goSearch() {
            this.showQuery = this.query;

            var url = this.APIserver + this.APIurl + '?query=' + this.query + '&index=' + this.searchIndex;
            var vm = this;
            console.log(url);
            axios.get(url)
            .then(function(response){
                console.log(response.data.result);
                vm.totalSearch = response.data.result;
                vm.totalCount = response.data.totalCount;

                vm.resultData = response.data.result[0].items;
                vm.countData = response.data.result[0].indexTotalCount;

                vm.resultEntp = response.data.result[2].items;
                vm.countEntp = response.data.result[2].indexTotalCount;
            })
            .catch(function(error){
                console.log(error);
            })

        }
    },
    mounted(){
        this.goSearch()
    },
    watch: {
        query: function() {
            this.$refs.autoQuery.reciveQuery(this.query);
        }
    }

}

</script>

<style>

</style>
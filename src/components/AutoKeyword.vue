<template>
    <div id="auto" name="auto">
        <!--  -->
        <div class="autocomplete" id="autocomplete">
            <ul class="autocomplete_ul">
                <li class="autocomplete_content" id="autocomplete_content">
                    <ul class="recomm" id="autocomplete_content_list" style="">
                        <!-- eslint-disable vue/no-use-v-if-with-v-for,vue/no-confusing-v-for-v-if -->
                        <li id="bg0" style=";" v-for="item in result" v-bind:key="item" v-if="result.length > 0" @click="goKeywordSearch(item.word)">
                            <img src="../../images/common/icon_portal_search.png" alt="My Image"  style="filter:grayscale(100%);width:17px; height:16px; ">
                            <span v-html="item.highlight" > </span>
                        </li>
                        <li id="bg0" style="background-color: rgb(255, 255, 255); cursor: pointer;" v-else>
                            해당 단어로 시작하는 검색어가 없습니다.<span id="f0" style="display:none;"></span>
                        </li>

                    </ul>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    data: function () {
        return {
            APIserver: this.APISERVER,
            APIurl: 'sample6/api/autoQuery.jsp',
            query: '',
            service: 'testsecond',
            result: ''
        }
    },
    props: ['APISERVER'],
    methods: {
        getQuery(query){
            this.query = query
        },
        goKeywordSearch: function (keyword){
            this.$emit('goKeywordSearch',keyword)
        }
    },
    watch: {
        query: async function() {

            var url = this.APIserver + this.APIurl + '?query=' + this.query
            var vm = this;

            axios.get(url)
            .then(function(response){

                vm.result = vm.query != '' ? response.data.result : response.data

            })
            .catch(function(error){
                console.log(error);
            })

        }
    }

}

</script>

<style>

</style>
<template>
    <div class="rank">
        <!--인기검색어 -->
        <h2>인기검색어</h2>
        <div class="tab_menu">
            <div class="tab_nav">
                <a href="#" :class="[showType == 'day' ? 'active' : '']" @click="changeType('day')">일간</a>
                <a href="#" :class="[showType == 'week' ? 'active' : '']" @click="changeType('week')">주간</a>
            </div>
            <div class="tab_contents">
                <div id="day" class="tab_content active">
                    <ol id="popword_d">
                        <li v-for="item in result" v-bind:key="item" @click="goKeywordSearch(item.word)"><a href="#" >{{item.word}}</a></li>
                    </ol>
                </div>
                <div id="week" class="tab_content">
                    <ol id="popword_w" name="popword_w"></ol>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    data:function() {
        return {
            APIserver : this.APISERVER,
            APIurl : 'sample6/api/popQuery.jsp',
            result : '',

            showType : 'day',
            type :  'PWD'
        }
    },
    props:['APISERVER'],
    methods: {

        changeType: function(type) {
            if(type == 'day') {
                this.showType = 'day'
                this.type = 'PWD'
            }
            if(type == 'week') {
                this.showType = 'week'
                this.type = 'PWW'
            }
        },

        goKeywordSearch: function(keyword) {
            this.$emit('goKeywordSearch',keyword)
        },

        async getPopWord(){
            var url = this.APIserver + this.APIurl + "?type=" + this.type
            var vm = this

            axios.get(url)
            .then(function(response){
                vm.result = response.data.result
            })
        }
    },
    mounted(){
        this.getPopWord()
    },
    watch: {
        type: async function(){
            this.getPopWord()
        }
    }

}
</script>

<style>

</style>
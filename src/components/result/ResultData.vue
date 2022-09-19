<template>
    <div class="portal-result-list">
        <div class="portal-result-header">
             <h4>data <span>검색결과 <em>{{count}}</em> 건</span></h4>
             <div class="portal-result-more" @click="goIndexSearch"><a>{{count>3 && searchIndex=='TOTAL'? '더보기' : ''}}</a></div>

        </div>

        <ul>
            <p v-if="count == 0"> 검색결과가 없습니다. </p>
            <li v-for="(item,i) in result" v-bind:key="i">
                <h5 span v-html="item.subject"></h5>
                <p v-html="item.content"></p>
                <div class="result-list-category">
                    <span v-html="item.file_nm"></span> <br>
                    [<span v-html="item.acct_year"></span>]
                </div>

            </li>
        </ul>

        <div class="common-pagination" v-if="count > 10 && searchIndex=='data'">

        </div>
    </div>

</template>

<script>
export default {
    data:function () {
        return {
            result: '',
            count: '',
            indexName: ''

        }
    },
    props: ['dataProps', 'searchIndex'],
    methods: {
        goIndexSearch: function(){
            this.$emit('goIndexSearch', this.indexName)
        }

    },
    updated() {
        this.result = this.dataProps.items
        this.count = this.dataProps.indexTotalCount
        this.indexName = this.dataProps.indexName
    },

}
</script>

<style>

</style>
<template>
  <div class="my">
        <div class="my_content">
            <ul class="bu clearfix" id="mykeyword">
                <li class="tit"><div class="ttxt">내가 찾은 검색어</div></li>
                <li class="searchkey" v-for="item in searchKeyword" v-bind:key="item"><a href="#"  @click="goKeywordSearch(item)"> {{item}} </a><a href="#" class="btn_del" @click="removeMyKeyword(item)" ></a></li>

            </ul>
        </div>
    </div>
</template>

<script>
export default {
    data: function() {
        return {

        }
    },
    props: ['searchKeyword'],
    methods: {
        removeMyKeyword: function(keyword) {
            this.$emit('removeMyKeyword', keyword)
        },

        goKeywordSearch: function(keyword) {
            this.$emit('goKeywordSearch',keyword)
        },
    }

}
</script>

<style>

</style>
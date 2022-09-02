<template>
  <div class="common-aside">
    <div class="rank">
		<!--인기검색어 -->
         <h2>인기검색어</h2>
        <div class="tab_menu">
                <div class="tab_nav">
                    <a href="#" class="active">일간</a>
                    <a href="#" >주간</a>
                </div>
                <div class="tab_contents">
                    <div id="day" class="tab_content active">
                        <ol id="popword_d" ></ol>
                    </div>
                    <div id="week" class="tab_content">
                        <ol id="popword_w" name="popword_w"></ol>
                    </div>
                </div>
            </div>
        </div>
	<div class="my">
        <div class="my_content">
            <ul class="bu clearfix" id="mykeyword" v-html="mySearchKeyword">

            </ul>
        </div>
    </div>

  </div>
</template>

<script>

export default {
    data: function () {
        return {

        }

    },
    props: ['mySearchKeyword'],
    methods: {
        removeMyKeyword: function(keyword) {
            alert("11")
            this.$emit('removeMyKeyword', keyword)
        },

        goKeywordSearch: function(keyword) {
            this.$emit.goKeywordSearch(keyword)
        }
    },
    mounted() {

    }


}
</script>

<style>

</style>
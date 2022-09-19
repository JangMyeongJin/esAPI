<template>
  <div class="common-aside">

    <pop-word v-bind:APISERVER="APISERVER" @goKeywordSearch="goKeywordSearch"></pop-word>

	<my-search-keyword v-bind:searchKeyword="searchKeyword" @removeMyKeyword="removeMyKeyword" @goKeywordSearch="goKeywordSearch"></my-search-keyword>

  </div>
</template>

<script>
import MySearchKeyword from './MySearchKeyword.vue'
import PopWord from './PopWord.vue'
import lodash from 'lodash'

export default {
    data: function () {
        return {
            searchKeyword : ''
        }

    },
    components: {
        'my-search-keyword' : MySearchKeyword,
        'pop-word' : PopWord
    },
    props: ['APISERVER'],
    methods: {

        goKeywordSearch: function(keyword) {
            this.$emit('goKeywordSearch',keyword)
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
            var str = []

            for (var i = 0; i < myKeywords.length; i++) {
                if (myKeywords[i] == "") continue
                str.push(myKeywords[i])
            }
            this.searchKeyword = str

        },

        removeMyKeyword: function (keyword) {
            var myKeyword = this.getCookie("mySearchKeyword");
            if (myKeyword == null) {
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
            window.location.reload(true);
        }




    }


}
</script>

<style>

</style>
<template>
    <div class="search">
        <div class="search-box-wrapper">
            <search-box ref="searchBox" @query="onQueryChange"></search-box>
        </div>
        <div class="shortcut-wrapper" v-show="!query">
            <div class="shortcut">
                <div class="hot-key">
                    <h1 class="title">热门搜索</h1>
                    <ul>
                        <li @click="addQuery(item.k)" v-for="(item, index) in hotkey" :key="index" class="item">
                            <span>{{item.k}}</span>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <div class="search-result" v-show="query" ref="result">
            <suggest :query="query" ref="suggest" @listScroll="blurInput"></suggest>
        </div>
        <router-view></router-view>
    </div>
</template>
<script>
import SearchBox from 'base/search-box';
import Suggest from 'components/suggest';

import {playlistMixin} from '@/assets/js/mixin';
import {getHotKey} from 'api/search';
import {ERR_OK} from 'api/config';

export default {
    mixins: [playlistMixin],
    data() {
        return {
            hotkey: [],
            query: ''
        }
    },
    components: {
        SearchBox,
        Suggest
    },
    created() {
        this._getHotKey();
    },
    methods: {
        addQuery(query) {
            this.$refs.searchBox.setQuery(query);
        },
        _getHotKey() {
            getHotKey().then(res => {
                if(res.code === ERR_OK){
                    this.hotkey = res.data.hotkey.slice(0, 10);
                }
            });
        },
        onQueryChange(query) {
            this.query = query;
        },
        blurInput() {
            this.$refs.searchBox.blur();
        },
        handlePlayList(playlist) {
            const bottom = playlist.length > 0 ? '60px' : '';
            this.$refs.result.style.bottom = bottom;
            this.$refs.suggest.refresh();
        }
        
    }
}
</script>
<style lang="scss" scoped>
@import '@/assets/css/_variable.scss';
@import "@/assets/css/_mixin.scss";
.search {
  .search-box-wrapper {
    margin: 20px;
  }
  .shortcut-wrapper {
    position: fixed;
    top: 178px;
    bottom: 0;
    width: 100%;
    .shortcut {
      height: 100%;
      overflow: hidden;
      .hot-key {
        margin: 0 20px 20px 20px;
        .title {
          margin-bottom: 20px;
          font-size: $font-size-medium;
          color: $color-text-l;
        }
        .item {
          display: inline-block;
          padding: 5px 10px;
          margin: 0 20px 10px 0;
          border-radius: 6px;
          background: $color-highlight-background;
          font-size: $font-size-medium;
          color: $color-text-d;
        }
      }
      .search-history {
        position: relative;
        margin: 0 20px;
        .title {
          display: flex;
          align-items: center;
          height: 40px;
          font-size: $font-size-medium;
          color: $color-text-l;
          .text {
            flex: 1;
          }
          .clear {
            @include extend-click();
            .icon-clear {
              font-size: $font-size-medium;
              color: $color-text-d;
            }
          }
        }
      }
    }
  }
  .search-result {
    position: fixed;
    width: 100%;
    top: 178px;
    bottom: 0;
  }
}
</style>

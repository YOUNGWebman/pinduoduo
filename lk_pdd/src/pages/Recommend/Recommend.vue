<template>
  <scroll class="scroller" :upCallback="upCallback" ref="mescroll" warpId="index_scroll"
          id="index_scroll">
     <div class="recommend">
       <shop-list
         v-for="(item, index) in recommendshoplist"
         :item=item
         :key="index"
         :clickCellBtn="dealWithCellBtnClick"
       />
     </div>
   </scroll>
</template>

<script>
  import {mapState} from 'vuex';
  import ShopList from './../../components/ShopList/ShopList';
  import {Indicator} from 'mint-ui';
  import {addGoodsToCart} from './../../api/index';
  import SelectLogin from './../Login/SelectLogin';

  import Scroll from './../../components/mescroll/Scroll';

  export default {
    name: "Recommend",
    data() {
      return {
        page: 1,
        count: 10
      }
    },
    mounted() {
      Indicator.open('正在加载数据...');
    },
    computed: {
      ...mapState(['recommendshoplist', 'userInfo'])
    },
    components: {
      ShopList,
      SelectLogin,
      Scroll
    },
    methods: {
      upCallback: function (page) {
        const SIZE = 10;
        this.$store.dispatch('reqRecommendShopList',{
           'app_name': 'rectab_sim_gyl',
           'offset': (page.num - 1) * SIZE,
           'count': SIZE,
           'scb': (result)=>{
               console.log(result);
               Indicator.close();
               this.$refs.mescroll.endSuccess(result.length);
           },
           'ecb': (err)=>{
              console.log(err);
              this.$refs.mescroll.endErr();
           }
        })

        /*this.$store.dispatch('getData', {
          page: page.num,
          scb: (result) => {
            this.$refs.mescroll.endSuccess(result.length);
            this.list = this.list.concat(result)
          },
          ecb:(err)=>{
            this.$vux.toast.show({text: err, type: 'warn'})
            this.$refs.mescroll.endErr();
          }
        });*/
      },
      // 监听商品点击
      async dealWithCellBtnClick(goods){
         // 1. 发送请求
        // user_id, goods_id, goods_name, thumb_url, price
         let result = await addGoodsToCart(this.userInfo.id, goods.goods_id, goods.goods_name, goods.thumb_url, goods.price);
         console.log(result);
      }
    },
  }
</script>

<style scoped lang="stylus" ref="stylesheet/stylus">
  .scroller
    padding-bottom 50px
    .recommend
      display flex
      flex-direction row
      flex-wrap wrap
      background #F5F5F5
</style>

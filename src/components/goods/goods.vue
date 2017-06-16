<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="item in goods" class="menu-item">
          <span class="text border-1px"><span v-show="item.type>0" class="icon"
                                              :class="classMap[item.type]"></span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item">
              <div class="icon">
                <img :src="food.icon" alt="" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">{{food.oldPrice}}</span>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
  import Bscroll from 'better-scroll'
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: []

      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$http.get('/api/goods').then((res) => {
        this.goods = res.data.data
        this.$nextTick(() => {
          this._initScroll()
        })
      })
    },
    methods: {
      _initScroll () {
        this.menuScroll = new Bscroll(this.$refs.menuWrapper, {})
        this.foodsScroll = new Bscroll(this.$refs.foodsWrapper, {})
      },
      _calculateHeight () {

      }
    }
  }
</script>
<style lang="less">
  @import url('../../common/stylus/mixin.less');

  .goods {
    display: flex;
    position: absolute;
    width: 100%;
    top: 174px;
    bottom: 46px;
    overflow: hidden;
    & > .menu-wrapper {
      flex: 0 0 80px;
      width: 80px;
      background: #f3f5f7;
      & > ul {
        & > .menu-item {
          display: table;
          width: 56px;
          height: 54px;
          line-height: 14px;
          margin: 0 12px;
          .border-1px(rgba(7, 17, 27, 0.2));

          & > .text {
            display: table-cell;
            font-size: 12px;
            width: 56px;
            vertical-align: middle;

            & > .icon {
              display: inline-block;
              vertical-align: top;
              width: 12px;
              height: 12px;
              margin-right: 2px;
              background-size: 12px 12px;
              background-repeat: no-repeat;
            }
            & > .decrease {
              .bg-image('../.././assets/decrease_1')
            }
            & > .discount {
              .bg-image('../.././assets/discount_1')
            }
            & > .guarantee {
              .bg-image('../.././assets/guarantee_1')
            }
            & > .invoice {
              .bg-image('../.././assets/invoice_1')
            }
            & > .special {
              .bg-image('../.././assets/special_1')
            }
          }
        }
      }
    }
    & > .foods-wrapper {
      flex: 1;
      & > ul {
        & > li {
          & > .title {
            padding-left: 14px;
            height: 26px;
            line-height: 26px;
            border-left: 2px solid #d9dde1;
            font-size: 12px;
            color: rgb(147, 153, 159);
            background: #f3f5f7;
          }
          & > ul {
            & > .food-item {
              display: flex;
              margin: 18px;
              padding-bottom: 18px;
              .border-1px(rgba(7, 17, 27, 0.1));
              & > .icon {
                flex: 0 0 57px;
                margin-right: 10px;

              }
              & > .content {
                flex: 1;
                color: rgb(7, 17, 27);
                & > .name {
                  margin: 2px 0 8px 0;
                  line-height: 14px;
                  height: 14px;
                  font-size: 14px;
                }
                & > .desc {
                  line-height: 12px;
                  font-size: 10px;
                  margin-bottom: 8px;
                }
                & > .extra {
                  line-height: 10px;
                  font-size: 10px;
                  & > .count {
                    margin-right: 10px;
                  }
                }
                & > .price {
                  font-weight: 700;
                  line-height: 24px;
                  color: #000;
                  & > .now {
                    margin-right: 8px;
                    font-size: 14px;
                    color: rgb(240, 20, 20);
                  }
                  & > .old {
                    text-decoration: line-through;
                    font-size: 10px;
                    color:;
                  }
                }
              }

            }
            & > :last-child {
              .border-none();
              margin-bottom: 0;
            }
          }

        }
      }
    }
  }
</style>

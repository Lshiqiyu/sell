<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}"
            @click="selectMenu(index,$event)">
          <span class="text border-1px"><span v-show="item.type>0" class="icon"
                                              :class="classMap[item.type]"></span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selecttFood(food,$event)" v-for="food in item.foods" class="food-item">
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
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="carcontrol-wrapper">
                  <car-control @cartAdd="addFood" :food="food"></car-control>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shop-cart ref="shopcart" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"
               :selectFoods="selectFoods"></shop-cart>
    <food :food="selectedFoods" ref="food"></food>
  </div>
</template>
<script>
  import Bscroll from 'better-scroll'
  import ShopCart from '.././shopcart/shopcart.vue'
  import CarControl from '.././carcontrol/carcontrol.vue'
  import food from '../food.vue'
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFoods: {}
      }
    },
    components: {
      ShopCart,
      CarControl,
      food
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$http.get('/api/goods').then((res) => {
        this.goods = res.data.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      })
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods () {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    methods: {
      selectMenu (index, event) {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodsScroll.scrollToElement(el, 300)
      },
      _drop (target) {
        this.$refs.shopcart.drop(target)
      },
      addFood (target) {
        this._drop(target)
      },
      _initScroll () {
        this.menuScroll = new Bscroll(this.$refs.menuWrapper, {
          click: true
        })
        this.foodsScroll = new Bscroll(this.$refs.foodsWrapper, {
          probeType: 3,
          click: true
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      selecttFood (food, event) {
//        if (!event._construcred) {
//          return
//        }
        this.selectedFoods = food
        console.log(this.$refs.food)
        this.$refs.food.show()
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
        & > .current {
          position: relative;
          z-index: 10;
          margin-top: -1px;
          background: #fff;
          color: #000000;
          font-weight: 700;
          .border-none();
          & > .text {
          }
        }
        & > .menu-item {
          box-sizing: border-box;
          display: table;
          width: 80px;
          height: 54px;
          line-height: 14px;
          padding: 0 12px;
          .border-1px(rgba(7, 17, 27, 0.2));

          & > .text {
            display: table-cell;
            font-size: 12px;
            width: 56px;
            vertical-align: middle;
            text-align: center;

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
        & > :last-child {
          .border-none();
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
                    color: rgb(7, 17, 27);
                  }
                }
                & > .carcontrol-wrapper {
                  position: absolute;
                  right: 0;
                  bottom: 12px;
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

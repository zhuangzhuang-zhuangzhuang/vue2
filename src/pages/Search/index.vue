<template>
  <div>
    <Typenav> </Typenav>

    <div class="main">
      <div class="py-container">
        <!--bread-->
        <div class="bread">
          <ul class="fl sui-breadcrumb">
            <li>
              <a href="#">全部结果</a>
            </li>
          </ul>
          <ul class="fl sui-tag">
            <li class="with-x" v-if="searchParams.categoryName">
              {{ searchParams.categoryName
              }}<i @click="removeCategoryName">×</i>
            </li>
            <li class="with-x" v-if="searchParams.keyword">
              {{ searchParams.keyword }}<i @click="removeKeyword">×</i>
            </li>
            <li class="with-x" v-if="searchParams.trademark">
              {{ searchParams.trademark.split(":")[1]
              }}<i @click="removeTrademark">×</i>
            </li>
            <li
              class="with-x"
              v-for="(prop, index) in searchParams.props"
              :key="prop"
            >
              {{ prop.split(":")[1] }}<i @click="removeProp(index)">×</i>
            </li>
          </ul>
        </div>

        <!--selector-->
        <SearchSelector
          @searchForTrademark="searchForTrademark"
          @searchForattr="searchForattr"
        />

        <!--details-->
        <div class="details clearfix">
          <div class="sui-navbar">
            <div class="navbar-inner filter">
              <ul class="sui-nav">
                <li
                  :class="{ active: searchParams.order.split(':')[0] === '1' }"
                >
                  综合
                  <!--  <a href="javascript:;"
                    >综合
                    <i
                      :v-if="searchParams.order.split(':')[1] == 'desc'"
                      class="iconfont"
                      :class="{
                        iconarrowup:
                          searchParams.order.split(':')[1] === 'desc',
                        iconarrowdown:
                          searchParams.order.split(':')[1] === 'asc',
                      }"
                    ></i>
                  </a> -->
                </li>

                <li
                  :class="{ active: searchParams.order.split(':')[0] === '2' }"
                >
                  <!-- <a href="javascript:;" )
                    >价格
                    <i
                      :v-if="searchParams.order.split(':')[1] == 'asc'"
                      class="iconfont"
                      :class="{
                        iconarrowup:
                          searchParams.order.split(':')[1] === 'desc',
                        iconarrowdown:
                          searchParams.order.split(':')[1] === 'asc',
                      }"
                    ></i>
                  </a> -->
                </li>
              </ul>
            </div>
          </div>
          <div class="goods-list">
            <ul class="yui3-g">
              <li class="yui3-u-1-5" v-for="goods in goodsList" :key="goods.id">
                <div class="list-wrap">
                  <div class="p-img">
                    <router-link :to="'/detail/' + goods.id"
                      ><img :src="goods.defaultImg"
                    /></router-link>
                    <!--  <a href="item.html" target="_blank"
                      ><img :src="goods.defaultImg"
                    /></a> -->
                  </div>
                  <div class="price">
                    <strong>
                      <em>¥</em>
                      <i>{{ goods.price }}</i>
                    </strong>
                  </div>
                  <div class="attr">
                    <router-link :to="'/detail/' + goods.id">{{
                      goods.title
                    }}</router-link>
                    <!--  <a target="_blank" href="item.html" :title="goods.title">{{
                      goods.title
                    }}</a> -->
                  </div>
                  <div class="commit">
                    <i class="command">已有<span>2000</span>人评价</i>
                  </div>
                  <div class="operate">
                    <a
                      href="success-cart.html"
                      target="_blank"
                      class="sui-btn btn-bordered btn-danger"
                      >加入购物车</a
                    >
                    <a href="javascript:void(0);" class="sui-btn btn-bordered"
                      >收藏</a
                    >
                  </div>
                </div>
              </li>
            </ul>
          </div>
          <div class="fr page">
            <div class="sui-pagination clearfix">
              <ul>
                <li class="prev disabled">
                  <a href="#">«上一页</a>
                </li>
                <li class="active">
                  <a href="#">1</a>
                </li>
                <li>
                  <a href="#">2</a>
                </li>
                <li>
                  <a href="#">3</a>
                </li>
                <li>
                  <a href="#">4</a>
                </li>
                <li>
                  <a href="#">5</a>
                </li>
                <li class="dotted"><span>...</span></li>
                <li class="next">
                  <a href="#">下一页»</a>
                </li>
              </ul>
              <div><span>共10页&nbsp;</span></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import SearchSelector from "./SearchSelector/SearchSelector";
export default {
  name: "Search",
  components: {
    SearchSelector,
  },
  computed: {
    ...mapGetters(["goodsList"]),
  },

  data() {
    return {
      searchParams: {
        //这个对象我们称作叫初始化所有的搜索参数,只不过一上来所有的搜索条件，我们都是空的
        // 今后只要是作为搜索条件的，所有相关数据，全部先在这个对象内部初始化好
        category1Id: "",
        category2Id: "",
        category3Id: "",
        categoryName: "",
        keyword: "",
        props: [],
        trademark: "",
        // 默认的搜索条件
        order: "1:desc", //排序规则，排序是后台排序的，我们搜索的时候得给后台一个默认的排序规则
        pageNo: 1, //搜素第几页的商品，分页也是后台做好的，我们也是得告诉后台我们要第几页数据
        pageSize: 1000, //每页多少个商品，告诉后台，每页回来多少个商品 默认10个
      },
    };
  },
  beforeMount() {
    // let { category1Id, category2Id, category3Id, categoryName } =
    //   this.$route.query;
    // let { keyword } = this.$route.params;
    // let searchParams = {
    //   ...this.searchParams,
    //   category1Id,
    //   category2Id,
    //   category3Id,
    //   categoryName,
    //   keyword,
    // };
    // this.searchParams = searchParams;
    this.handelSearch();
  },
  watch: {
    $route(newVal, oldVal) {
      this.getsearchInfo();
      this.handelSearch();
    },
  },
  mounted() {
    this.getsearchInfo();
  },
  methods: {
    getsearchInfo() {
      this.$store.dispatch("getsearchInfo", this.searchParams);
    },
    handelSearch() {
      let { category1Id, category2Id, category3Id, categoryName } =
        this.$route.query;
      let { keyword } = this.$route.params;

      let searchParams = {
        ...this.searchParams,
        category1Id,
        category2Id,
        category3Id,
        categoryName,
        keyword,
      };
      Object.keys(searchParams).forEach((item) => {
        if (searchParams[item] === "") {
          delete searchParams[item];
        }
      });

      this.searchParams = searchParams;
    },
    //删除关键字，是从三级分类跳过来的关键字
    removeCategoryName() {
      this.searchParams.categoryName = undefined;
      this.searchParams.category1Id = undefined;
      this.searchParams.category2Id = undefined;
      this.searchParams.category3Id = undefined;
      // this.getsearchInfo();
      // this.$router.push({ name: "search", query: this.$route.query }); //目的是删除的时候让路径重置/
      this.$router.replace({ name: "search", params: this.$route.params }); //目的是让路径变化
    },
    //删除搜索页面传过来的关键字
    removeKeyword() {
      this.searchParams.keyword = undefined;
      this.$bus.$emit("clearKeyword"); //使用全局事件总线通知搜索框清空
      // this.getsearchInfo();
      this.$router.push({ name: "search", query: this.$route.query });
    },
    removeTrademark() {
      this.searchParams.trademark = undefined;
      this.getsearchInfo();
    },
    // 用户点击品牌后，根据品牌搜索重新发送请求
    searchForTrademark(trademark) {
      this.searchParams.trademark = `${trademark.tmId}:${trademark.tmName}`;
      this.getsearchInfo();
    },
    //用户点击属性后，根据平台属性重新发请求
    searchForattr(attrValue, attrs) {
      let prop = `${attrs.attrId}:${attrValue}:${attrs.attrName}`;
      //点击跳转之前做个判断
      let isRepeate = this.searchParams.props.some((item) => item === prop);
      if (isRepeate) {
        return;
      }

      this.searchParams.props.push(prop);
      this.getsearchInfo();
    },
    removeProp(index) {
      this.searchParams.props.splice(index, 1);
      this.getsearchInfo();
    },
    //点击综合或者价格的排序回调
    /*  changeSort(sortFlag) {
      //首先获取到原来的排序标志和排序类型
      let originSortFlag = this.searchParams.order.split(":")[0];
      let originSortType = this.searchParams.order.split(":")[1];
      let newOrder = "";
      if (sortFlag === originSortFlag) {
        //假设用户点击的排序标志和原来的是一样的，证明点击的还是同一个排序，那么我们需要把排序类型改变
        newOrder = `${originSortFlag}:${
          originSortType === "asc" ? "desc" : "asc"
        }`;
      } else {
        //假设用户点击的排序标志和原来的是不一样的，证明点击的不是同一个排序，那么我们需要把排序标志改变，排序类型默认
        newOrder = `${sortFlag}:desc`;
      }
      this.searchParams.order = newOrder;
      this.getsearchInfo();
    }, */
  },
};
</script>

<style lang="less" scoped>
.main {
  margin: 10px 0;

  .py-container {
    width: 1200px;
    margin: 0 auto;

    .bread {
      margin-bottom: 5px;
      overflow: hidden;

      .sui-breadcrumb {
        padding: 3px 15px;
        margin: 0;
        font-weight: 400;
        border-radius: 3px;
        float: left;

        li {
          display: inline-block;
          line-height: 18px;

          a {
            color: #666;
            text-decoration: none;

            &:hover {
              color: #4cb9fc;
            }
          }
        }
      }

      .sui-tag {
        margin-top: -5px;
        list-style: none;
        font-size: 0;
        line-height: 0;
        padding: 5px 0 0;
        margin-bottom: 18px;
        float: left;

        .with-x {
          font-size: 12px;
          margin: 0 5px 5px 0;
          display: inline-block;
          overflow: hidden;
          color: #000;
          background: #f7f7f7;
          padding: 0 7px;
          height: 20px;
          line-height: 20px;
          border: 1px solid #dedede;
          white-space: nowrap;
          transition: color 400ms;
          cursor: pointer;

          i {
            margin-left: 10px;
            cursor: pointer;
            font: 400 14px tahoma;
            display: inline-block;
            height: 100%;
            vertical-align: middle;
          }

          &:hover {
            color: #28a3ef;
          }
        }
      }
    }

    .details {
      margin-bottom: 5px;

      .sui-navbar {
        overflow: visible;
        margin-bottom: 0;

        .filter {
          min-height: 40px;
          padding-right: 20px;
          background: #fbfbfb;
          border: 1px solid #e2e2e2;
          padding-left: 0;
          border-radius: 0;
          box-shadow: 0 1px 4px rgba(0, 0, 0, 0.065);

          .sui-nav {
            position: relative;
            left: 0;
            display: block;
            float: left;
            margin: 0 10px 0 0;

            li {
              float: left;
              line-height: 18px;

              a {
                display: block;
                cursor: pointer;
                padding: 11px 15px;
                color: #777;
                text-decoration: none;
              }

              &.active {
                a {
                  background: #e1251b;
                  color: #fff;
                }
              }
            }
          }
        }
      }

      .goods-list {
        margin: 20px 0;

        ul {
          display: flex;
          flex-wrap: wrap;

          li {
            height: 100%;
            width: 20%;
            margin-top: 10px;
            line-height: 28px;

            .list-wrap {
              .p-img {
                padding-left: 15px;
                width: 215px;
                height: 255px;

                a {
                  color: #666;

                  img {
                    max-width: 100%;
                    height: auto;
                    vertical-align: middle;
                  }
                }
              }

              .price {
                padding-left: 15px;
                font-size: 18px;
                color: #c81623;

                strong {
                  font-weight: 700;

                  i {
                    margin-left: -5px;
                  }
                }
              }

              .attr {
                padding-left: 15px;
                width: 85%;
                overflow: hidden;
                margin-bottom: 8px;
                min-height: 38px;
                cursor: pointer;
                line-height: 1.8;
                display: -webkit-box;
                -webkit-box-orient: vertical;
                -webkit-line-clamp: 2;

                a {
                  color: #333;
                  text-decoration: none;
                }
              }

              .commit {
                padding-left: 15px;
                height: 22px;
                font-size: 13px;
                color: #a7a7a7;

                span {
                  font-weight: 700;
                  color: #646fb0;
                }
              }

              .operate {
                padding: 12px 15px;

                .sui-btn {
                  display: inline-block;
                  padding: 2px 14px;
                  box-sizing: border-box;
                  margin-bottom: 0;
                  font-size: 12px;
                  line-height: 18px;
                  text-align: center;
                  vertical-align: middle;
                  cursor: pointer;
                  border-radius: 0;
                  background-color: transparent;
                  margin-right: 15px;
                }

                .btn-bordered {
                  min-width: 85px;
                  background-color: transparent;
                  border: 1px solid #8c8c8c;
                  color: #8c8c8c;

                  &:hover {
                    border: 1px solid #666;
                    color: #fff !important;
                    background-color: #666;
                    text-decoration: none;
                  }
                }

                .btn-danger {
                  border: 1px solid #e1251b;
                  color: #e1251b;

                  &:hover {
                    border: 1px solid #e1251b;
                    background-color: #e1251b;
                    color: white !important;
                    text-decoration: none;
                  }
                }
              }
            }
          }
        }
      }

      .page {
        width: 733px;
        height: 66px;
        overflow: hidden;
        float: right;

        .sui-pagination {
          margin: 18px 0;

          ul {
            margin-left: 0;
            margin-bottom: 0;
            vertical-align: middle;
            width: 490px;
            float: left;

            li {
              line-height: 18px;
              display: inline-block;

              a {
                position: relative;
                float: left;
                line-height: 18px;
                text-decoration: none;
                background-color: #fff;
                border: 1px solid #e0e9ee;
                margin-left: -1px;
                font-size: 14px;
                padding: 9px 18px;
                color: #333;
              }

              &.active {
                a {
                  background-color: #fff;
                  color: #e1251b;
                  border-color: #fff;
                  cursor: default;
                }
              }

              &.prev {
                a {
                  background-color: #fafafa;
                }
              }

              &.disabled {
                a {
                  color: #999;
                  cursor: default;
                }
              }

              &.dotted {
                span {
                  margin-left: -1px;
                  position: relative;
                  float: left;
                  line-height: 18px;
                  text-decoration: none;
                  background-color: #fff;
                  font-size: 14px;
                  border: 0;
                  padding: 9px 18px;
                  color: #333;
                }
              }

              &.next {
                a {
                  background-color: #fafafa;
                }
              }
            }
          }

          div {
            color: #333;
            font-size: 14px;
            float: right;
            width: 241px;
          }
        }
      }
    }
  }
}
</style>
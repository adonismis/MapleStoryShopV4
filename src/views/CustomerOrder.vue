<template>
  <div>
    <Header></Header>
    <Alert></Alert>
    <main>
      <div class="container py-5 cart">
        <div class="text-center">
          <h2 class="font-weight-bold mb-4 pb-2">購物車內容</h2>
        </div>
        <div class="row d-flex justify-content-center">
          <div class="col-lg-10" v-if="cart.carts.length !== 0">
            <div class="table-responsive-sm">
              <table class="table mb-0">
                <thead class="thead-light">
                  <th></th>
                  <th class="text-nowrap">商品名稱</th>
                  <th class="text-nowrap">數量</th>
                  <th class="text-nowrap">小計</th>
                </thead>
                <tbody>
                  <tr v-for="item in cart.carts" :key="item.id">
                    <td class="align-middle">
                      <button
                        type="button"
                        class="btn btn-outline-maple btn-sm"
                        @click="removeCartItem(item.id)"
                      >
                        <i class="far fa-trash-alt"></i>
                      </button>
                    </td>
                    <td class="align-middle">
                      {{ item.product.title }}
                      <div class="text-success" v-if="item.coupon">已套用優惠券</div>
                    </td>
                    <td class="align-middle">
                      <div class="input-group">
                        <button class="btn btn-outline-moderate btn-sm d-none d-sm-block mr-2"
                        @click="minusQty(item.id, item.product.id, item.qty)">
                          <i class="fas fa-minus"></i>
                        </button>
                        <select class="select-text-center form-control border-moderate"
                        id="qtySelect"
                        @change="updateQtyBySelect(item.id, item.product.id, $event)">
                          <option selected disabled>{{ item.qty }}</option>
                          <option :value="number" v-for="number in 10" :key="number">
                            {{ number }}
                          </option>
                        </select>
                        <button class="btn btn-outline-moderate btn-sm d-none d-sm-block ml-2"
                        @click="addQty(item.id, item.product.id, item.qty)">
                          <i class="fas fa-plus"></i>
                        </button>
                      </div>
                    </td>
                    <td
                      class="align-middle text-right"
                      :class="{'text-success': item.coupon}"
                    >{{ item.final_total | currency }}</td>
                  </tr>
                </tbody>
                <tfoot>
                  <tr>
                    <td colspan="3" class="text-right">總金額</td>
                    <td class="text-right">{{ cart.total | currency }}</td>
                  </tr>
                  <tr v-if="cart.total !== cart.final_total">
                    <td colspan="3" class="text-right text-success">折扣價</td>
                    <td class="text-right text-success">{{ cart.final_total | currency }}</td>
                  </tr>
                </tfoot>
              </table>
            </div>
            <div class="input-group input-group">
              <input type="text" class="form-control" placeholder="請輸入優惠碼" v-model="coupon_code" />
              <div class="input-group-append">
                <button class="btn btn-outline-maple" type="button" @click="addCouponCode">
                  套用優惠碼
                </button>
              </div>
            </div>
          </div>
          <div class="col-lg-10" v-else>
            <div class="empty-cart text-center">
              <p class="mb-1">購物車沒有東西哦！快去逛逛吧！</p>
              <router-link to="/category" class="text-maple text-decoration-none">
                進入商城
              </router-link>
            </div>
          </div>
        </div>
      </div>
      <div class="container cart-form pb-5">
        <div class="text-center">
          <h2 class="font-weight-bold mb-4 pb-2">購買資訊</h2>
        </div>
        <div class="row justify-content-center">
          <form class="col-lg-10" @submit.prevent="createOrder">
            <div class="form-group">
              <label for="useremail">
                <i class="fas fa-envelope"></i>
                Email (*)
              </label>
              <input
                type="email"
                class="form-control"
                name="email"
                id="useremail"
                v-model="form.user.email"
                placeholder="請輸入 Email"
                v-validate="'required|email'"
                :class="{'is-invalid': errors.has('email')}"
                :disabled="isDisabled"
              />
              <span class="text-danger" v-if="errors.has('email')">
                {{ errors.first('email') }}
              </span>
            </div>

            <div class="form-group">
              <label for="username">
                <i class="fas fa-user"></i>
                收件人姓名 (*)
              </label>
              <input
                type="text"
                class="form-control"
                name="name"
                id="username"
                v-model="form.user.name"
                placeholder="輸入姓名"
                v-validate="'required'"
                :class="{'is-invalid': errors.has('name')}"
                :disabled="isDisabled"
              />
              <span class="text-danger" v-if="errors.has('name')">必須輸入姓名</span>
            </div>

            <div class="form-group">
              <label for="usertel">
                <i class="fas fa-phone"></i>
                收件人電話 (*)
              </label>
              <input
                type="tel"
                class="form-control"
                id="usertel"
                v-model="form.user.tel"
                placeholder="請輸入電話"
                name="userTel"
                v-validate="'required'"
                :class="{'is-invalid': errors.has('userTel')}"
                :disabled="isDisabled"
              />
              <span class="text-danger" v-if="errors.has('userTel')">電話欄位不得留空</span>
            </div>

            <div class="form-group">
              <label for="useraddress">
                <i class="fas fa-map-marker-alt"></i>
                收件人地址 (*)
              </label>
              <input
                type="text"
                class="form-control"
                name="address"
                id="useraddress"
                v-model="form.user.address"
                placeholder="請輸入地址"
                v-validate="'required'"
                :class="{'is-invalid': errors.has('address')}"
                :disabled="isDisabled"
              />
              <span class="text-danger" v-if="errors.has('address')">地址欄位不得留空</span>
            </div>

            <div class="form-group">
              <label for="comment">
                <i class="fas fa-comment-dots"></i>
                留言
              </label>
              <textarea
                name
                id="comment"
                class="form-control"
                cols="30"
                rows="10"
                v-model="form.message"
                :disabled="isDisabled"
              ></textarea>
            </div>
            <div class="text-right">
              <button class="btn btn-maple btn-lg" :disabled="isDisabled">送出訂單</button>
            </div>
          </form>
        </div>
      </div>
    </main>
    <Footer></Footer>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import Header from '../components/Header.vue';
import Footer from '../components/Footer.vue';
import Alert from '../components/shared/AlertMessage.vue';

export default {
  name: 'CustomerOrder',
  components: {
    Header,
    Footer,
    Alert,
  },
  data() {
    return {
      coupon_code: '',
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: '',
        },
        message: '',
      },
      isDisabled: true,
    };
  },
  methods: {
    removeCartItem(id) {
      this.$store.dispatch('removeCart', id);
    },
    addCouponCode() {
      this.$store.dispatch('addCouponCode', this.coupon_code);
    },
    createOrder() {
      const vm = this;
      const url = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/order`;
      const order = vm.form;
      vm.$validator.validate().then((result) => {
        if (result) {
          vm.$http.post(url, { data: order }).then((response) => {
            if (response.data.success) {
              vm.$router.push(`/customerCheckout/${response.data.orderId}`);
            }
            vm.$store.dispatch('updateLoading', false);
          });
        }
      });
    },
    addQty(cid, pid, qty) {
      const newQty = Number(qty) + 1;
      this.$store.dispatch('updateQty', { cid, pid, newQty });
    },
    minusQty(cid, pid, qty) {
      const newQty = Number(qty) - 1;
      this.$store.dispatch('updateQty', { cid, pid, newQty });
    },
    updateQtyBySelect(cid, pid, $event) {
      const newQty = Number($event.target.value);
      this.$store.dispatch('updateQtyBySelect', { cid, pid, newQty });
    },
    ...mapActions(['getCart']),
  },
  computed: {
    ...mapGetters(['cart']),
  },
  created() {
    this.getCart();
  },
  watch: {
    cart() {
      const vm = this;
      if (vm.cart.carts.length !== 0) {
        vm.isDisabled = false;
      } else {
        vm.isDisabled = true;
      }
    },
  },
};
</script>

<style scope lang="scss">
main {
  margin-top: 69px;
}

.loading-image {
  background-image: url(../assets/images/GIFs/KingSlime.gif);
  background-size: cover;
  width: 219px;
  height: 230px;
}

.cart {
  h2 {
    display: inline-block;
    font-weight: 600;
    border-bottom: 3px solid #c1170c;
  }
  .empty-cart {
    background-color: #ededed;
    border-radius: 10px;
    padding: 30px 0px;
  }
  .select-text-center {
    width: 100px !important;
    text-align: justify;
    text-align-last: center;
    border-radius: 0.2rem !important;
    flex: none;
  }
}

.cart-form {
  h2 {
    display: inline-block;
    font-weight: 600;
    border-bottom: 3px solid #c1170c;
  }
}
</style>

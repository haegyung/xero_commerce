<template>
    <div>
        <order-item-list-component :order-item-list="orderItemList"></order-item-list-component>
        <div class="xe-row">
            <div class="xe-col-lg-8">
                <div class="card">
                    <div class="card-header">
                        <h4>주문고객 정보</h4>
                    </div>
                    <div class="card-content">
                        <table class="table table-bordered">
                            <tr>
                                <th>이름</th>
                                <td>{{userInfo.name}}</td>
                            </tr>
                            <tr>
                                <th>연락처</th>
                                <td>{{userInfo.phone}}</td>
                            </tr>
                            <tr>
                                <th>이메일</th>
                                <td>{{user.email}}</td>
                            </tr>
                        </table>
                    </div>
                </div>
                <order-delivery-component :user-info="userInfo" v-model="delivery"></order-delivery-component>
                <div class="card">
                    <div class="card-header">
                        <h4>결제 정보 입력</h4>
                    </div>
                    <div class="card-content">
                        <table class="table table-bordered">
                            <tr>
                                <th>간편 결제</th>
                                <td></td>
                                <th>일반 결제</th>
                                <td></td>
                            </tr>
                        </table>
                    </div>
                </div>
                <div class="card">
                    <div class="card-header">
                        <h4>할인 정보</h4>
                    </div>
                    <div class="card-content">
                        <table class="table table-bordered">
                            <tr>
                                <th>할인 쿠폰</th>
                                <td><input type="text" value="0">원</td>
                            </tr>
                            <tr>
                                <th>적립금 사용</th>
                                <td><input type="text" value="0">원</td>
                            </tr>
                        </table>
                    </div>
                </div>
            </div>
            <div class="xe-col-lg-4">
                <order-bill-component :summary="orderSummary" @pay="register"></order-bill-component>
                <order-agreement-component :agreements="agreements"></order-agreement-component>
            </div>
        </div>
    </div>
</template>

<script>
  import OrderItemListComponent from './OrderItemListComponent'
  import OrderDeliveryComponent from './OrderDeliveryComponent'
  import OrderBillComponent from './OrderBillComponent'
  import OrderAgreementComponent from './OrderAgreementComponent'

  export default {
    name: "OrderRegisterComponent",
    components: {
      OrderDeliveryComponent, OrderBillComponent, OrderAgreementComponent, OrderItemListComponent
    },
    props: [
      'orderItemList', 'orderSummary', 'user', 'userInfo', 'order_id', 'dashUrl', 'successUrl', 'failUrl', 'agreements'
    ],
    computed: {
      defaultDelivery() {
        if (this.userInfo.user_delivery.length > 0)
        {
          var delivery = this.userInfo.user_delivery.find(v=>{return v.seq = 1});
          return {
            name: delivery.name,
            contact: [
                delivery.phone.slice(0,3),
                delivery.phone.slice(3,4),
                delivery.phone.slice(4,4)
            ],
            address: delivery.addr,
            address_detail: delivery.addr_detail,
            msg: delivery.msg
          }
        }
        return {
          name: this.userInfo.name,
          contact: [
            this.userInfo.phone.slice(0,3),
            this.userInfo.phone.slice(3,7),
            this.userInfo.phone.slice(7,11)
          ],
          address: '',
          address_detail: '',
          msg: ''
        }
      }
    },
    data() {
      return {
        delivery: null
      }
    },
    methods: {
      register(pay) {
        if (pay.status) {
          $.ajax({
            url: this.successUrl,
            method: 'post',
            data: {
              delivery: this.delivery,
              _token: document.getElementById('csrf_token').value
            }
          }).done(this.complete).fail(this.fail())
        } else {
          document.location.href = this.failUrl
        }
      },
      complete(res) {
        document.location.href = this.dashUrl
      },
      fail(error) {
        document.location.href = this.failUrl
      }
    },
    mounted () {
      console.log(this.userInfo)
    }
  }
</script>

<style scoped>
    .card-content table {
        margin: 0
    }
</style>
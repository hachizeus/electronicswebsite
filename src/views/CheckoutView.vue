<template>
  <div class="checkout">
    <h2>Checkout</h2>
    <div v-if="!isLoggedIn" class="login-required">
      <p>Please login to continue with checkout</p>
      <router-link to="/profile-page" class="login-btn">Login</router-link>
    </div>
    <div v-else class="checkout-content">
      <div class="order-summary">
        <h3>Order Summary</h3>
        <div v-if="cartItems.length === 0" class="empty-cart">
          <p>Your cart is empty</p>
          <router-link to="/catalog">Continue Shopping</router-link>
        </div>
        <div v-else>
          <div v-for="item in cartItems" :key="item.id" class="cart-item">
            <img :src="item.firstImg" :alt="item.title" />
            <div class="item-details">
              <h4>{{ item.title }}</h4>
              <p>Quantity: {{ item.count || 1 }}</p>
              <p class="price">
                KSH
                {{
                  Math.floor(item.price - (item.price * item.discount) / 100)
                }}.00
              </p>
            </div>
          </div>
          <div class="total">
            <h3>Total: KSH {{ calculateTotal() }}.00</h3>
          </div>
          <button class="place-order-btn" @click="placeOrder">
            Place Order
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isLoggedIn: false,
      cartItems: [],
    };
  },
  methods: {
    checkLoginStatus() {
      const user = localStorage.getItem("currentUser");
      this.isLoggedIn = !!user;
    },
    loadCartItems() {
      const cart = localStorage.getItem("cart");
      this.cartItems = cart ? JSON.parse(cart) : [];
    },
    calculateTotal() {
      return this.cartItems.reduce((total, item) => {
        const discountedPrice = Math.floor(
          item.price - (item.price * item.discount) / 100
        );
        return total + discountedPrice * (item.count || 1);
      }, 0);
    },
    placeOrder() {
      alert("Order placed successfully!");
      localStorage.removeItem("cart");
      this.$router.push("/");
    },
  },
  mounted() {
    this.checkLoginStatus();
    this.loadCartItems();
  },
};
</script>

<style lang="scss" scoped>
.checkout {
  flex: 1;
  padding: 30px 15px;
  width: 80%;
  @media (max-width: 1199px) {
    width: 100%;
    padding: 30px 0;
  }
  h2 {
    font-size: 24px;
    margin-bottom: 40px;
  }
  .login-required {
    text-align: center;
    padding: 50px;
    .login-btn {
      display: inline-block;
      padding: 15px 30px;
      background-color: var(--yellow);
      color: var(--bg-color);
      text-decoration: none;
      border-radius: 6px;
      font-weight: 500;
      margin-top: 20px;
    }
  }
  .checkout-content {
    .order-summary {
      background-color: white;
      padding: 30px;
      border-radius: 6px;
      h3 {
        margin-bottom: 20px;
      }
      .empty-cart {
        text-align: center;
        padding: 50px;
        a {
          display: inline-block;
          padding: 15px 30px;
          background-color: var(--yellow);
          color: var(--bg-color);
          text-decoration: none;
          border-radius: 6px;
          font-weight: 500;
          margin-top: 20px;
        }
      }
      .cart-item {
        display: flex;
        align-items: center;
        padding: 15px 0;
        border-bottom: 1px solid #eee;
        img {
          width: 80px;
          height: 80px;
          object-fit: cover;
          margin-right: 20px;
        }
        .item-details {
          flex: 1;
          h4 {
            margin: 0 0 10px 0;
            font-size: 16px;
          }
          p {
            margin: 5px 0;
            &.price {
              font-weight: bold;
              color: var(--yellow);
            }
          }
        }
      }
      .total {
        margin-top: 20px;
        padding-top: 20px;
        border-top: 2px solid #eee;
        text-align: right;
      }
      .place-order-btn {
        width: 100%;
        padding: 15px;
        background-color: var(--yellow);
        color: var(--bg-color);
        border: none;
        border-radius: 6px;
        font-size: 18px;
        font-weight: 500;
        cursor: pointer;
        margin-top: 20px;
        transition: 0.3s;
        &:hover {
          background-color: #e6b800;
        }
      }
    }
  }
}
</style>

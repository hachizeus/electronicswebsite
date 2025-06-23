<template>
  <div class="account">
    <h2>Profile Page</h2>
    <div class="user-profile" v-if="isLoggedIn">
      <h4>Welcome, {{ currentUser.name }}!</h4>
      <div class="profile-info">
        <p><strong>Name:</strong> {{ currentUser.name }}</p>
        <p><strong>Email:</strong> {{ currentUser.email }}</p>
      </div>
      <button class="logout-btn" @click="handleLogout">LOGOUT</button>
    </div>
    <div class="login" v-else-if="!showSignup">
      <h4>Login</h4>
      <div v-if="message" class="message">{{ message }}</div>
      <form @submit="handleLogin">
        <div class="form-floating first">
          <input
            class="form-control"
            type="email"
            placeholder=" "
            v-model="loginForm.email"
            required
          />
          <label>Email</label>
        </div>
        <div class="form-floating">
          <input
            class="form-control"
            type="password"
            placeholder=" "
            v-model="loginForm.password"
            required
          />
          <label>Password</label>
        </div>
        <span class="forgot-pw">Forgot Password?</span>
        <input class="submit" type="submit" value="LOGIN" />
        <p class="create-acc">
          Don't have an account?
          <span
            class="sign-up"
            @click="
              showSignup = true;
              message = '';
            "
            >Sign up</span
          >
        </p>
      </form>
    </div>
    <div class="signup" v-else>
      <h4>Sign Up</h4>
      <div v-if="message" class="message">{{ message }}</div>
      <form @submit="handleSignup">
        <div class="form-floating first">
          <input
            class="form-control"
            type="text"
            placeholder=" "
            v-model="signupForm.name"
            required
          />
          <label>Full Name</label>
        </div>
        <div class="form-floating">
          <input
            class="form-control"
            type="email"
            placeholder=" "
            v-model="signupForm.email"
            required
          />
          <label>Email</label>
        </div>
        <div class="form-floating">
          <input
            class="form-control"
            type="password"
            placeholder=" "
            v-model="signupForm.password"
            required
          />
          <label>Password</label>
        </div>
        <div class="form-floating">
          <input
            class="form-control"
            type="password"
            placeholder=" "
            v-model="signupForm.confirmPassword"
            required
          />
          <label>Confirm Password</label>
        </div>
        <input class="submit" type="submit" value="SIGN UP" />
        <p class="create-acc">
          Already have an account?
          <span
            class="sign-up"
            @click="
              showSignup = false;
              message = '';
            "
            >Login</span
          >
        </p>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showSignup: false,
      loginForm: {
        email: "",
        password: "",
      },
      signupForm: {
        name: "",
        email: "",
        password: "",
        confirmPassword: "",
      },
      message: "",
      currentUser: null,
      isLoggedIn: false,
    };
  },
  mounted() {
    this.checkLoginStatus();
  },
  methods: {
    handleLogin(e) {
      e.preventDefault();
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      const user = users.find(
        (u) =>
          u.email === this.loginForm.email &&
          u.password === this.loginForm.password
      );

      if (user) {
        localStorage.setItem("currentUser", JSON.stringify(user));
        this.currentUser = user;
        this.isLoggedIn = true;
        this.message = "Login successful!";
        setTimeout(() => (this.message = ""), 1500);
      } else {
        this.message = "Invalid email or password";
      }
    },
    handleSignup(e) {
      e.preventDefault();
      if (this.signupForm.password !== this.signupForm.confirmPassword) {
        this.message = "Passwords do not match";
        return;
      }

      const users = JSON.parse(localStorage.getItem("users") || "[]");
      if (users.find((u) => u.email === this.signupForm.email)) {
        this.message = "Email already exists";
        return;
      }

      const newUser = {
        name: this.signupForm.name,
        email: this.signupForm.email,
        password: this.signupForm.password,
      };

      users.push(newUser);
      localStorage.setItem("users", JSON.stringify(users));
      this.message = "Account created successfully!";
      setTimeout(() => (this.showSignup = false), 1500);
    },
    checkLoginStatus() {
      const user = localStorage.getItem("currentUser");
      if (user) {
        this.currentUser = JSON.parse(user);
        this.isLoggedIn = true;
      }
    },
    handleLogout() {
      localStorage.removeItem("currentUser");
      this.currentUser = null;
      this.isLoggedIn = false;
      this.message = "Logged out successfully!";
      setTimeout(() => {
        this.message = "";
        this.$router.push("/");
      }, 1500);
    },
  },
};
</script>

<style lang="scss" scoped>
.account {
  flex: 1;
  padding: 30px 15px;
  @media (max-width: 1199px) {
    width: 100%;
    padding: 30px 0;
  }
  h2 {
    font-size: 24px;
  }
  .login,
  .signup {
    max-width: 55%;
    margin: 50px auto;
    background-color: white;
    padding: 30px;
    border-radius: 6px;
    @media (max-width: 1199px) {
      max-width: 100%;
    }
    h4 {
      text-align: center;
      font-size: 22px;
    }
    .form-floating {
      margin-bottom: 25px;
      &.first {
        margin-top: 25px;
      }
      .form-control {
        height: 44px;
        background-color: rgb(232, 240, 254);
        &:focus {
          border-color: #ced4da;
          box-shadow: none;
        }
      }
      label {
        color: grey;
        padding: 10px;
      }
    }
    .forgot-pw {
      cursor: pointer;
      margin-top: 10px;
      display: inline-block;
      &:hover {
        color: var(--yellow);
      }
    }
    .submit {
      display: block;
      margin: auto;
      margin-top: 25px;
      border: none;
      padding: 15px 20px;
      border-radius: 6px;
      background-color: rgb(232, 240, 254);
      transition: 0.3s;
      &:hover {
        background-color: var(--yellow);
      }
    }
    .create-acc {
      margin-top: 15px;
      margin-bottom: 0;
      text-align: center;
      span {
        cursor: pointer;
        &:hover {
          color: var(--yellow);
        }
      }
    }
  }
  .message {
    padding: 10px;
    margin-bottom: 15px;
    border-radius: 4px;
    text-align: center;
    background-color: #d4edda;
    color: #155724;
    border: 1px solid #c3e6cb;
  }
  .user-profile {
    max-width: 55%;
    margin: 50px auto;
    background-color: white;
    padding: 30px;
    border-radius: 6px;
    text-align: center;
    @media (max-width: 1199px) {
      max-width: 100%;
    }
    h4 {
      font-size: 22px;
      margin-bottom: 20px;
      color: var(--bg-color);
    }
    .profile-info {
      margin-bottom: 30px;
      p {
        margin: 10px 0;
        font-size: 16px;
      }
    }
    .logout-btn {
      border: none;
      padding: 15px 20px;
      border-radius: 6px;
      background-color: #dc3545;
      color: white;
      font-weight: 500;
      cursor: pointer;
      transition: 0.3s;
      &:hover {
        background-color: #c82333;
      }
    }
  }
  @media (max-width: 1199px) {
    flex-direction: column-reverse;
    padding: 30px 0;
  }
}
</style>

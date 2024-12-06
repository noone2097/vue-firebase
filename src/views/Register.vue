<script setup>
import { ref } from "vue";
import { getAuth, createUserWithEmailAndPassword } from "firebase/auth";
import { useRouter } from "vue-router";
import { db } from "../firebase/init.js";
import { collection, addDoc } from "firebase/firestore";

const email = ref("");
const password = ref("");
const confirmPassword = ref("");
const gender = ref("");
const errMsg = ref("");
const router = useRouter();
const isLoading = ref(false);

// creating a users

const createUser = async () => {
  const colRef = collection(db, "users");

  const userObj = {
    name: email.value,
    password: password.value,
    gender: gender.value,
  };

  await addDoc(colRef, userObj);
  // isLoading.value = true;
};

const register = async (e) => {
  e.preventDefault();

  if (password.value !== confirmPassword.value) {
    alert("password dont match");
    confirmPassword.value = "";
    password.value = "";
    return;
  }

  createUserWithEmailAndPassword(getAuth(), email.value, password.value)
    .then((data) => {
      router.push("/authors");
    })
    .catch((err) => (errMsg.value = extractValueInParenthesis(err.message)));
  isLoading.value = true;
  createUser();
};

function extractValueInParenthesis(str) {
  const regex = /\((.*?)\)/; // regex to match text inside parentheses
  const matches = str.match(regex);
  if (matches) {
    const value = matches[1].replace("auth/", ""); // remove "auth/" prefix
    return value;
  }
  return ""; // return empty string if no match found
}
</script>
<template>
  <div class="container">
    <form class="form" @submit.prevent="register">
      <p class="form-title">Register account</p>
      <div class="input-container">
        <input
          type="email"
          placeholder="Enter email"
          v-model="email"
          required
        />
        <span> </span>
      </div>
      <div class="input-container">
        <input
          type="password"
          placeholder="Enter password"
          v-model="password"
          required
        />
      </div>
      <div class="input-container">
        <input
          type="password"
          placeholder="Re-enter password"
          v-model="confirmPassword"
          required
        />
      </div>
      <div class="radio-style">
        <div>
          <input type="radio" id="male" value="male" v-model="gender" />
          <label for="male">Male</label>
        </div>
        <div>
          <input type="radio" id="female" value="female" v-model="gender" />
          <label for="female">Female</label>
        </div>
      </div>
      <p v-if="errMsg" class="errMsg">{{ errMsg }}</p>
      <button type="submit" class="submit">Register</button>

      <p class="signup-link">
        Already have an account ?
        <router-link to="/sign-in">Sign In</router-link>
      </p>
    </form>
  </div>
</template>

<style scoped>
.form {
  background-color: #fff;
  display: block;
  padding: 1rem;
  max-width: 370px;
  border-radius: 0.5rem;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1),
    0 4px 6px -2px rgba(0, 0, 0, 0.05);
}

.form-title {
  font-size: 1.25rem;
  line-height: 1.75rem;
  font-weight: 600;
  text-align: center;
  color: #000;
}

.input-container {
  position: relative;
}

.input-container input,
.form button {
  outline: none;
  border: 1px solid #e5e7eb;
  margin: 8px 0;
}

.input-container input {
  background-color: #fff;
  padding: 1rem;
  padding-right: 3rem;
  font-size: 1.2rem;
  line-height: 1.25rem;
  width: 300px;
  border-radius: 0.5rem;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
}

.submit {
  display: block;
  padding-top: 0.75rem;
  padding-bottom: 0.75rem;
  padding-left: 1.25rem;
  padding-right: 1.25rem;
  background-color: #4f46e5;
  color: #ffffff;
  font-size: 0.875rem;
  line-height: 1.25rem;
  font-weight: 500;
  width: 100%;
  border-radius: 0.5rem;
  text-transform: uppercase;
  cursor: pointer;
}

.signup-link {
  color: #6b7280;
  font-size: 0.875rem;
  line-height: 1.25rem;
  text-align: center;
}

.signup-link a {
  text-decoration: underline;
}
.errMsg {
  color: red;
  text-align: center;
}
/* radio style */

.radio-style {
  padding: 10px;
  display: flex;
  align-items: start;
  justify-content: space-between;
  flex-direction: column;
  cursor: pointer;
}
.radio-style div > label {
  cursor: pointer;
}
</style>

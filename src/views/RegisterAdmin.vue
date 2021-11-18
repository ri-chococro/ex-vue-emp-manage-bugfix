<template>
  <div class="container">
    <div class="row register-page">
      <form class="col s12" id="reg-form">
        <div class="row">
          <div class="error-message">{{ registerError }}</div>
          <div class="input-field col s6">
            <input
              id="last_name"
              type="text"
              class="validate"
              v-model="lastName"
              required
            />
            <div class="error-message">{{ lastNameError }}</div>
            <label for="last_name">姓</label>
          </div>
          <div class="input-field col s6">
            <input
              id="first_name"
              type="text"
              class="validate"
              v-model="firstName"
              required
            />
            <div class="error-message">{{ firstNameError }}</div>
            <label for="first_name">名</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <input
              id="email"
              type="email"
              class="validate"
              v-model="mailAddress"
              required
            />
            <div class="error-message">{{ mailAddressError }}</div>
            <label for="email">メールアドレス</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <input
              id="password"
              type="password"
              class="validate"
              minlength="8"
              v-model="password"
              required
            />
            <div class="error-message">{{ passwordError }}</div>
            <label for="password">パスワード</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <input
              id="confirmation-password"
              type="password"
              class="validate"
              minlength="8"
              v-model="confirmationPassword"
              required
            />
            <div class="error-message">{{ confirmationPasswordError }}</div>
            <label for="confirmation-password">確認用パスワード</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s6">
            <button
              class="btn btn-large btn-register waves-effect waves-light"
              type="button"
              v-on:click="registerAdmin"
            >
              登録
              <i class="material-icons right">done</i>
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import config from "@/const/const";
import axios from "axios";

/**
 * 管理者登録をする画面.
 */
@Component
export default class RegisterAdmin extends Vue {
  // 姓
  private lastName = "";
  // 名
  private firstName = "";
  // メールアドレス
  private mailAddress = "";
  // パスワード
  private password = "";
  // 確認用パスワード
  private confirmationPassword = "";
  // 姓エラーメッセージ
  private lastNameError = "";
  // 名エラーメッセージ
  private firstNameError = "";
  // メールアドレスエラーメッセージ
  private mailAddressError = "";
  // パスワードエラーメッセージ
  private passwordError = "";
  // 確認用パスワードエラーメッセージ
  private confirmationPasswordError = "";
  // エラー有無のフラグ
  private hasError = false;
  // 登録失敗時のエラーメッセージ
  private registerError = "";

  /**
   * 管理者情報を登録する.
   *
   * @remarks
   * 本メソッドは非同期でWebAPIを呼び出し管理者登録をするためasync/await axiosを利用しています。これらを利用する場合は明示的に戻り値にPromiseオブジェクト型を指定する必要があります。
   *
   *
   * @returns Promiseオブジェクト
   */
  async registerAdmin(): Promise<void> {
    this.lastNameError = "";
    this.firstNameError = "";
    this.mailAddressError = "";
    this.passwordError = "";
    this.confirmationPasswordError = "";
    this.hasError = false;
    this.registerError = "";

    if (this.lastName === "") {
      this.lastNameError = "姓を入力してください";
      this.hasError = true;
    }
    if (this.firstName === "") {
      this.firstNameError = "名を入力してください";
      this.hasError = true;
    }
    if (this.mailAddress === "") {
      this.mailAddressError = "メールアドレスを入力してください";
      this.hasError = true;
    }
    if (this.password === "") {
      this.passwordError = "パスワードを入力してください";
      this.hasError = true;
    }
    if (this.confirmationPassword === "") {
      this.confirmationPasswordError = "確認用パスワードを入力してください";
      this.hasError = true;
    } else if (this.confirmationPassword !== this.password) {
      this.confirmationPasswordError = "パスワードが一致しません";
      this.hasError = true;
    }

    if (this.hasError) {
      return;
    }

    // 管理者登録処理
    const response = await axios.post(`${config.EMP_WEBAPI_URL}/insert`, {
      name: this.lastName + " " + this.firstName,
      mailAddress: this.mailAddress,
      password: this.password,
    });
    console.dir("response:" + JSON.stringify(response));
    if (response.data.status === "success") {
      this.$router.push("/loginAdmin");
    } else {
      this.registerError = "登録できませんでした";
      this.mailAddressError = "このメールアドレスは既に登録されています";
    }
  }
}
</script>

<style scoped>
.register-page {
  width: 600px;
}
.error-message {
  color: red;
}
</style>

<template>
    <div class="ui middle aligned center aligned grid">
        <div class="column">
            <h2 class="ui teal image header">
                <img src="../assets/banner.jpg" class="image">
                <div class="content">
                    Trung Tâm Ngoại Ngữ Tin Học Đăk Nông
                </div>
            </h2>
            <form class="ui large form ">
                <div class="ui stacked segment">
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="user icon"></i>
                            <input type="text" name="username" placeholder="username" v-model="username">
                        </div>
                    </div>
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="lock icon"></i>
                            <input type="password" name="password" placeholder="Password" v-model="password">
                        </div>
                    </div>
                    <button id="login" class="ui fluid large teal submit button">Đăng Nhập</button>
                </div>

                <div class="ui error message"></div>

            </form>

            <div class="ui message">
                Chưa có tài khoản đăng nhập, liên hệ với chúng tôi!
                <p>tài khoản quản trị
                    <code>admin/123</code>
                </p>
                <p>tài khoản thí sinh
                    <code>user01/123</code>
                    <code>user02/123</code>
                </p>
                <!--New to us? <a href="#">Sign Up</a>-->
            </div>
        </div>
    </div>
</template>
<script>
import toastr from 'toastr'
import { AuthServices } from '../../services/auth.js'
import Promise from 'bluebird'
const _authServices = new AuthServices()

export default {
  mounted: function () {
    var me = this
    $('#login').api({
      mockResponseAsync: Promise.coroutine(function* (st, cb) {
        yield me.login()
        cb(true)
      }),
      on: 'click'
    })
  },
  data: function () { return { username: '', password: '' } },
  methods: {
    login: Promise.coroutine(function* () {
      try {
        let ret = yield _authServices.login({
          'username': this.username,
          'password': this.password
        })
        console.log('ussername Hiệp', this.username)
        if (ret) {
          toastr.info('Đăng nhập thành công')
          yield this.$store.dispatch('login', ret.userId)
          try {
            let roles = yield _authServices.getUserRoles()
            this.$log.debug('getUserRoles()', roles)
            if (roles.roleId == 1) {
              this.$router.push({ path: 'admin' })
            } else if (roles.roleId == 2) {
              this.$router.push({ path: 'userQuiz' })
            } else {

            }
          } catch (error) {
            toastr.error('Không có quyền truy cập.')
          }
        } else {
          toastr.error('Đăng nhập không thành công')
        }
      } catch (error) {
        toastr.error('Không kết nối với server')
      }
    })
  }

}
</script>
<style scoped>
body {
    background-color: #DADADA;
}

body>.grid {
    height: 100%;
}

.image {
    margin-top: -100px;
}

.column {
    max-width: 500px;
}
</style>

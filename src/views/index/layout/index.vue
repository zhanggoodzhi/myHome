<template>
  <el-container class="container">
    <el-header height="60px" class="header">
      <div class="left">
        <a class="img-wrap">
          <img class="gicon" src="/static/imgs/logo.jpg" alt="">
        </a>
      </div>
      <div class="right">
        <div class="info-wrap">
          <span class="name">
            欢迎： {{this.alias}}
          </span>
          <span class="logout" @click="onLogout">退出</span>
        </div>
      </div>
    </el-header>
    <el-container>
      <el-aside class="menu-wrap" width="200px">
        <el-menu :router="true" :default-active="$route.path" class="menu">
          <el-menu-item index="/home">
            <i class="icon-activity_fill iconfont"></i>
            <span slot="title">首页</span>
          </el-menu-item>
          <el-menu-item index="/note">
            <i class="icon-barrage_fill iconfont"></i>
            <span slot="title">留言({{noteNumber}})</span>
          </el-menu-item>
          <el-menu-item index="/game">
            <i class="el-icon-share"></i>
            <span slot="title">游戏分享</span>
          </el-menu-item>
          <el-menu-item v-show="isAdmin" index="/user">
            <i class="icon-group_fill iconfont"></i>
            <span slot="title">用户管理</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main>
            <el-breadcrumb separator="/" class="bread">
              <el-breadcrumb-item :to="{ path: breadPath }">{{breadName}}</el-breadcrumb-item>
              <el-breadcrumb-item></el-breadcrumb-item>
            </el-breadcrumb>
        <transition name="slide" enter-active-class="animated bounceInLeft" leave-active-class="animated bounceOutRight" mode="out-in">
            <router-view/>
        </transition>
      </el-main>
    </el-container>
    <el-footer class="footer" height="25px">
      <span class="copyright">小志&nbsp;&nbsp;&nbsp;&nbsp;2018</span>
    </el-footer>
  </el-container>
</template>
<script>
import { getAuthData } from "components/utils";
import { createNamespacedHelpers } from "vuex";
const { mapState, mapActions } = createNamespacedHelpers("noteBadge");
export default {
  data() {
    return {
      alias: "",
      breadName: "",
      breadPath: ""
    };
  },
  beforeRouteEnter(to, from, next) {
    console.log("进入");
      next(vm => {
        vm.breadName = to.meta;
        vm.breadPath = to.path;
        console.log(to);
      })
  },
  beforeRouteUpdate(to, from, next) {
    console.log('更新');
    this.breadName = to.meta;
    this.breadPath = to.path;
    next();
  },
  created() {
    const authDataString = localStorage.getItem("authData");
    if (!authDataString) {
      this.$router.replace("/login");
    } else {
      this.alias = JSON.parse(authDataString).alias;
    }
    this.getCount();
  },
  methods: {
    ...mapActions(["getCount"]),
    onLogout() {
      localStorage.setItem("authData", "");
      this.$router.replace("/login");
    }
  },
  computed: {
    ...mapState(["noteNumber"]),
    isAdmin() {
      return getAuthData() && getAuthData().ifAdmin;
    }
  }
};
</script>
<style lang="less" scoped>
.container {
  height: 100vh;
}
.bread {
  margin-bottom: 15px;
}
.menu-wrap {
  border-right: 1px solid #e6e6e6;
  .menu {
    border-right: 0;
    height: 100%;
  }
}

.header {
  border: 0;
  background: #409eff;
  border-bottom: 1px solid #e6e6e6;
  display: flex;
  align-items: center;
  justify-content: center;
  .left {
    .img-wrap {
      margin-left: 22px;
      display: inline-block;
      .gicon {
        width: 40px;
        transition: all 0.2s;
      }
    }
  }
  .right {
    flex: 1;
    text-align: right;
    .info-wrap {
      font-size: 12px;
      .name {
        color: white;
        margin-right: 10px;
      }
      .logout {
        color: white;
        cursor: pointer;
        &:hover {
          color: #f56c6c;
        }
      }
    }
  }
}

.menu-wrap {
  .iconfont {
    margin-right: 5px;
    width: 24px;
    text-align: center;
    font-size: 18px;
    vertical-align: middle;
  }
}

.footer {
  background: #909399;
  display: flex;
  justify-content: center;
  align-items: center;
  .copyright {
    font-size: 14px;
    color: white;
    font-weight: bold;
  }
}

main {
  display: block;
}
</style>

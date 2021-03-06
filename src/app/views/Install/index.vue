<!--
  Copyright (C) 2022 Suwings <Suwings@outlook.com>

  This program is free software: you can redistribute it and/or modify
  it under the terms of the GNU Affero General Public License as published by
  the Free Software Foundation, either version 3 of the License, or
  (at your option) any later version.
  
  According to the AGPL, it is forbidden to delete all copyright notices, 
  and if you modify the source code, you must open source the
  modified source code.

  版权所有 (C) 2022 Suwings <Suwings@outlook.com>

  该程序是免费软件，您可以重新分发和/或修改据 GNU Affero 通用公共许可证的条款，
  由自由软件基金会，许可证的第 3 版，或（由您选择）任何更高版本。

  根据 AGPL 与用户协议，您必须保留所有版权声明，如果修改源代码则必须开源修改后的源代码。
  可以前往 https://mcsmanager.com/ 阅读用户协议，申请闭源开发授权等。
-->

<template>
  <div class="contanier">
    <div class="bg"></div>
    <div class="panel-wrapper" v-if="step == 0">
      <Panel class="panel tc" body-style="padding:40px;">
        <h1 class="title">欢迎使用，MCSManager 管理面板</h1>
        <p>开源，分布式，开箱即用，支持 Minecraft 游戏服务端和所有控制台程序的管理面板</p>
        <div style="margin-top: 48px">
          <el-button type="primary" @click="next" v-loading="isLoading">开始使用</el-button>
        </div>
        <div class="panel-bottom">
          <a href="https://mcsmanager.com/" target="_blank" rel="noopener noreferrer">
            Reference: https://mcsmanager.com/
          </a>
          <br />
          <span>Released under the AGPL-3.0 License.</span>
        </div>
      </Panel>
    </div>

    <div class="panel-wrapper" v-if="step == 1">
      <Panel class="panel" body-style="padding:40px;">
        <h1 class="title">创建一个访问面板的管理员账号</h1>
        <p>用户名支持任何语言，请务必保证您的密码安全。</p>
        <div>
          <el-form ref="form" :model="initUser" :rules="rules" label-width="66px">
            <el-form-item label="用户名" prop="userName">
              <el-input v-model="initUser.userName" />
            </el-form-item>
            <el-form-item label="密码" prop="passWord">
              <el-input v-model="initUser.passWord" />
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="createUser">创建账号</el-button>
            </el-form-item>
          </el-form>
        </div>
      </Panel>
    </div>

    <!-- <div class="panel-wrapper" v-if="step == 2">
      <Panel class="panel" body-style="padding:40px;" v-loading="isLoading">
        <h1 class="title">我们需要一些时间安装依赖程序</h1>
        <p>
          我们将下载约5MB左右的二进制程序辅助 MCSManager
          的运行，为您提供最真实的终端交互功能，这是一个可选功能。
        </p>
        <div style="margin-top: 48px">
          <ItemGroup>
            <el-button type="primary" @click="installLib">安装依赖库</el-button>
            <el-button @click="next">跳过</el-button>
          </ItemGroup>
          <p class="color-gray" style="font-size: 12px">
            <small>如果此安装失败或者跳过，面板依然可以正常使用，只是缺少仿真控制台能力。 </small>
          </p>
        </div>
      </Panel>
    </div> -->

    <div class="panel-wrapper" v-if="step == 2">
      <Panel class="panel" body-style="padding:40px;" v-loading="isLoading">
        <h1 class="title">完成！</h1>
        <p>最后，您是第一次使用此软件吗？</p>
        <ItemGroup>
          <SelectBlock @click="toQuickStart">
            <template #title>首次使用</template>
          </SelectBlock>
        </ItemGroup>
        <ItemGroup>
          <SelectBlock @click="toOverview">
            <template #title>老用户</template>
          </SelectBlock>
        </ItemGroup>
      </Panel>
    </div>
  </div>
</template>

<script>
import SelectBlock from "@/components/SelectBlock";
import Panel from "@/components/Panel";
import { request } from "../../service/protocol";
import { API_PANEL_INSTALL } from "../../service/common";
export default {
  components: { Panel, SelectBlock },
  data: function () {
    return {
      isLoading: false,
      step: 0,
      initUser: {
        userName: "",
        passWord: ""
      },
      rules: {
        userName: { required: true, message: "请输入内容", trigger: "blur" },
        passWord: { required: true, message: "请输入内容", trigger: "blur" }
      }
    };
  },
  methods: {
    next() {
      this.step++;
    },
    async createUser() {
      this.$refs["form"].validate(async (valid) => {
        if (!valid) return;
        try {
          await request({
            method: "POST",
            url: API_PANEL_INSTALL,
            data: {
              username: this.initUser.userName,
              password: this.initUser.passWord
            }
          });
          this.next();
          this.$message({ message: "管理员已创建", type: "success" });
        } catch (err) {
          this.$message({ message: err, type: "error" });
        }
      });
    },
    toQuickStart() {
      window.location.href = "/#/quickstart?from_install=1";
    },
    toOverview() {
      window.location.href = "/#/overview?from_install=1";
    },
    installLib() {
      console.log("install....");
      this.next();
    }
  }
};
</script>

<style scoped>
.bg {
  z-index: 998;
  background-image: linear-gradient(45deg, #e4fff5, #ffeffb);
  position: fixed;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;
}

.panel-wrapper {
  position: fixed;
  z-index: 999;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;

  display: flex;
  align-items: center;
  justify-content: center;

  transition-property: all;
  transition-duration: 1.5s;
  transition-timing-function: cubic-bezier(1, 0.05, 0.84, 0.74);
  transition-delay: 0s;
}

.panel {
  background-color: white;
  min-height: 340px;
  width: 520px;
  transition: all 0.4s;
  position: relative;
}

.panel p {
  font-size: 16px;
}

.tc {
  text-align: center;
}

.panel:hover {
  border: 1px solid #b6bbc2;
  /* width: 540px; */
}

.panel-bottom {
  position: absolute;
  bottom: 0px;
  left: 0px;
  right: 0;
  text-align: center;
  padding-bottom: 20px;
}

.title {
  font-size: 24px;
  margin: 0px 0px 12px 0px;
}
</style>

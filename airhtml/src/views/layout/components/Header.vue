<template>
	<div id="header-style">
		<el-row type="flex" align="middle" class="header-el-row-style">
			<el-col :span="3" align="center">
				<img src="../../../assets/header.png" height="50px" width="50px">
			</el-col>
			<el-col :span="16">
				<h1 class="h1-color">户外环境监测可视化平台V1.0</h1>
			</el-col>
			<el-col :span="5" align="center">
				<el-dropdown trigger="click" @command="Openclick">
					<span class="el-dropdown-link">
						{{headeruser}}
						<i class="el-icon-arrow-down el-icon--right"></i>
					</span>
					<el-dropdown-menu slot="dropdown">
						<el-dropdown-item icon="el-icon-s-custom" command="Openusercenter">账号信息</el-dropdown-item>
						<el-dropdown-item divided icon="el-icon-user" command="Openuseradministrator" v-show="useradministrator_visible">开发者模式</el-dropdown-item>
						<el-dropdown-item divided icon="el-icon-user" command="Opensuperadministrator" v-show="superadministrator_visible">管理员模式</el-dropdown-item>
						<el-dropdown-item divided icon="el-icon-switch-button" command="Openexit">退出登录</el-dropdown-item>
					</el-dropdown-menu>
				</el-dropdown>
			</el-col>
		</el-row>
	</div>
</template>

<script>
import {
  readData, removeData,
  saveData
} from "../../../utils/storageUtils.js"

function remove_all_cached_data() {
  console.log("remove_all_cached_data")
  removeData('currentuser')
  removeData('currentpower')
  removeData('information')
  removeData('equipmentdata')
  removeData('currentequipment')
}

export default {
		setup(props, {
			root
		}) {
			const Openclick = ((command) => {
				if (command == "Openusercenter") {
					root.$router.push({
						path: '/Consolepersonal'
					})
				}
				if (command == "Openuseradministrator") {
					root.$router.push({
						path: '/useradministrator'
					})
				}
				if (command == "Opensuperadministrator") {
					root.$router.push({
						path: '/superadministrator'
					})
				}
				if (command == "Openexit") {

          remove_all_cached_data();
          root.$router.push({
						path: '/login'
					})
				}
			});

			return {
				headeruser: readData('information').nick != null ? readData('information').nick : readData('currentuser'),
				useradministrator_visible: readData('currentpower') == 'rwx' ? 1 : 0,
				superadministrator_visible: readData('currentuser') == 'raoxuefeng@yeah.net' ? 1 : 0,
				Openclick
			}
		},
	}
</script>

<style lang="scss" scoped>
	#header-style {
		position: absolute;
		height: 50px;
		width: 100%;
		background-color: #B3EFCE;
		-webkit-box-shadow: 0 3px 16px 0 rgba(0, 0, 0, .1);
	}

	.header-el-row-style {
		height: 50px;
	}

	.el-dropdown-link {
		cursor: pointer;
		color: #409EFF;
	}

	.el-icon-arrow-down {
		font-size: 12px;
	}

	.h2-color {
		color: #409EFF;
	}
</style>

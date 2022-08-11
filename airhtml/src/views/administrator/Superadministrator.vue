<template>
	<div id="superadministrtor-style">
		<div class="div-stylesuper">
			<el-row type="flex">
				<el-button type="primary" @click="Onquery">检索</el-button>
				<el-button type="primary" @click="Onchangepassword">修改密码</el-button>
				<el-input v-model="queryuser.password" placeholder="请输入密码" v-if='changepassword' maxlength=20 class="el-input-style el-input-style1"
				 show-password clearable />
				<el-button icon="el-icon-finished" v-if='changepassword' @click.native.prevent="Onchange" class="el-input-style1"
				 circle />
			</el-row>
		</div>

		<div class="div-stylesuper">
			<el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange" border>
				<el-table-column type="selection" width="55" />
				<el-table-column prop="mailbox" label="用户id" />
				<el-table-column prop="password" label="密码" />
				<el-table-column prop="power" label="开发者权限" />
				<el-table-column fixed="right" label="操作">
					<template slot-scope="scope">
						<el-button @click.native.prevent="addRow(scope.$index, tableData)" type="text">
							添加权限
						</el-button>
						<el-button @click.native.prevent="delRow(scope.$index, tableData)" type="text">
							删除权限
						</el-button>
					</template>
				</el-table-column>
			</el-table>
		</div>
	</div>
</template>

<script>
	import {
		ref,
		reactive
	} from '@vue/composition-api'
	import {
		validatamailbox,
		stripscript
	} from "../../utils/validata.js"
	import {
		request
	} from "../../utils/request.js"
	export default {
		name: 'superadministrator',
		setup(props, {
			root
		}) {
			const changepassword = ref(false)

			const queryuser = reactive({
				mailbox: '',
				password: '',
			})

			const multipleSelection = reactive([])

			const tableData = reactive([{
				mailbox: "xxx",
				password: "xxx",
				power: "xxx"
			}])

			var tabledata = []

			const Onquery = (() => {
				var data = {
					requirement: 'onquery'
				}
				request({
					url: "/superadministrator",
					data: data
				}).then(res => {
					if (res != false) {
						console.log(res)
						if (res.length < tabledata.length) {
							tabledata.forEach((row, index) => {
								tableData.splice(tabledata.length - index, 1)
							})
						}
						tabledata = res
						tabledata.forEach((row, index) => {
							root.$set(tableData, index, {
								'mailbox': row.mailbox,
								'password': row.password,
								'power': row.power
							})
						})
					} else {
						root.$message({
							showClose: true,
							message: '请求服务器操作失败',
							type: 'warning'
						})
					}
				}, err => {
					root.$message({
						showClose: true,
						message: '请求服务器错误',
						type: 'error'
					})
					console.log("superadministrator-Onquery")
					console.log(err)
					console.log("superadministrator-Onquery")
				})
			})

			const addRow = ((index, row) => {
				var data = {
					requirement: 'addRow',
					mailbox: row[index].mailbox,
					power: "rwx"
				}
				request({
					url: "/superadministrator",
					data: data
				}).then(res => {
					if (res != false) {
						tabledata[index].power = "rwx"
						root.$set(tableData, index, {
							'mailbox': tabledata[index].mailbox,
							'password': tabledata[index].password,
							'power': tabledata[index].power
						})
					} else {
						root.$message({
							showClose: true,
							message: '请求服务器操作失败',
							type: 'warning'
						})
					}
				}, err => {
					root.$message({
						showClose: true,
						message: '请求服务器错误',
						type: 'error'
					})
					console.log("useradministrator-addRow")
					console.log(err)
					console.log("useradministrator-addRow")
				})
			});

			const delRow = ((index, row) => {
				var data = {
					requirement: 'delRow',
					mailbox: row[index].mailbox,
					power: "r-x"
				}
				request({
					url: "/superadministrator",
					data: data
				}).then(res => {
					if (res != false) {
						tabledata[index].power = "r-x"
						root.$set(tableData, index, {
							'mailbox': tabledata[index].mailbox,
							'password': tabledata[index].password,
							'power': tabledata[index].power
						})
					} else {
						root.$message({
							showClose: true,
							message: '请求服务器操作失败',
							type: 'warning'
						})
					}
				}, err => {
					root.$message({
						showClose: true,
						message: '请求服务器错误',
						type: 'error'
					})
					console.log("useradministrator-delRow")
					console.log(err)
					console.log("useradministrator-delRow")
				})
			});

			const Onchangepassword = (() => {
				changepassword.value = !changepassword.value
			})

			const handleSelectionChange = ((row) => {
				multipleSelection.value = row
			})

			const Onchange = (() => {
				console.log(multipleSelection.value[0])
				queryuser.password = stripscript(queryuser.password)
				if (queryuser.password === '') {
					root.$message({
						showClose: true,
						message: '新密码不能为空',
						type: 'error'
					})
				} else if (queryuser.password.length < 8 || queryuser.password.length > 20) {
					root.$message({
						showClose: true,
						message: '新密码为8-20位字母、数字、特殊字符$@%#',
						type: 'warning'
					})
				} else {
					var data = {
						requirement: 'onchange',
						mailbox: multipleSelection.value[0].mailbox,
						password: multipleSelection.value[0].password
					}
					request({
						url: "/superadministrator",
						data: data
					}).then(res => {
						if (res != false) {
							tabledata.forEach((row, index) => {
								if (row.mailbox == multipleSelection.value[0].mailbox) {
									tabledata[index].password = queryuser.password
									root.$set(tableData, index, {
										'mailbox': tabledata[index].mailbox,
										'password': tabledata[index].password,
										'power': tabledata[index].power
									})
								}
							})
							root.$message({
								showClose: true,
								message: '修改密码成功',
								type: 'success'
							})
						} else {
							root.$message({
								showClose: true,
								message: '请求服务器操作失败',
								type: 'warning'
							})
						}
					}, err => {
						root.$message({
							showClose: true,
							message: '请求服务器错误',
							type: 'error'
						})
						console.log("superadministrator-Onchange")
						console.log(err)
						console.log("superadministrator-Onchange")
					})
				}

			})

			return {
				tableData,
				addRow,
				delRow,
				queryuser,
				changepassword,
				Onquery,
				Onchangepassword,
				multipleSelection,
				handleSelectionChange,
				Onchange
			}
		},
    mounted() {
      console.log("!!! mounted !!!")
      this.Onquery()
    }
  }

</script>

<style lang="scss" scoped>
	#superadministrtor-style {
		position: absolute;
		top: 20px;
		left: 20px;
		right: 20px;
		box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1)
	}

	.div-stylesuper {
		margin: 30px;
	}

	.el-col-style11 {
		text-align: center;
	}

	.el-input-style {
		width: 250px;
	}

	.el-input-style1 {
		margin-left: 10px;
	}

	.h3-stylesuper {
		text-align: right;
		margin-right: 10px;
		line-height: 40px;
	}
</style>

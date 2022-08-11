<template>
	<div>
		<baidu-map class="map" :center="center" :zoom="zoom" @ready="handler" :scroll-wheel-zoom="true">
			<bm-geolocation anchor="BMAP_ANCHOR_BOTTOM_RIGHT" :showAddressBar="true" :autoLocation="true"></bm-geolocation>
		</baidu-map>
	</div>
</template>

<script>
	import {
		readData
	} from "../../utils/storageUtils.js"
	import {
		request
	} from "../../utils/request.js"
	var markpoint = []
	export default {
		name: 'equipmentmap',
		setup(props, {
			root
		}) {
			const getPoints = (({
				BMap,
				map
			}, position) => {
				var that = this
				var point = new BMap.Point(position.lng, position.lat)
				var marker = new BMap.Marker(point)
				map.addOverlay(marker)
				var content = "<table>";
				content = content + "<tr><td> 设备地点：" + position.address + " </td></tr>";
				content = content + "<tr><td> 设备id：" + position.tid + " </td></tr>";
				content = content + "<tr><td> 时间：" + position.finalresponsetime + "</td></tr>";
				content += "</table>";
				// 设置提示框的内容
				var infoWindow = new BMap.InfoWindow(content)
				marker.addEventListener('click', function() {
					map.openInfoWindow(infoWindow, point)
				})
			})

			const handler = (({
				BMap,
				map
			}) => {
				var data = {
					mailbox: readData('currentuser')
				}
				request({
					url: "/equipmentmap",
					data: data
				}).then(res => {
					if (res != false) {
						markpoint = res
						// 将标记点 渲染
						markpoint.forEach((item) => {
							getPoints({
									BMap,
									map,
								},
								item)
						})
						map.enableScrollWheelZoom(true)
					} else {
						root.$message({
							showClose: true,
							message: '获取用户设备点失败',
							type: 'error'
						})
					}
				}, err => {
					root.$message({
						showClose: true,
						message: '请求服务器错误',
						type: 'error'
					})
					console.log("equipmentmap")
					console.log(err)
					console.log("equipmentmap")
				})
			})

			return {
				handler,
				center: {
					lng: 110,
					lat: 22
				},
				zoom: 10,
				//标记点
				markpoint: markpoint
			}
		},
		methods: {
			// 关闭信息窗口 @close 自带的方法
			infoWindowClose(marker) {
				this.markpoint[marker].showFlag = false
			},

			// 打开信息框
			infoWindowOpen(marker) {
				this.markpoint[marker].showFlag = true
			}
		},
	}
</script>

<style lang="scss" scoped>
	.map {
		position: absolute;
		width: 100%;
		height: 100%;
	}
</style>

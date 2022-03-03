<!-- drag -->
<template>
	<div class="box" ref="box">
		<div class="left">
			<slot name="drag-left"></slot>
		</div>
		<div class="resize" title="收缩侧边栏">⋮⋮⋮</div>
		<div class="right">
			<slot name="drag-right"></slot>
		</div>
	</div>
</template>
<script>
export default {
	props: {
		//左侧占百分比
		leftWidth: {
			type: String,
			default: 50
		}
	},
	data() {
		return {}
	},
	mounted() {
		this.dragControllerDiv()
	},
	methods: {
		dragControllerDiv() {
			var resize = document.getElementsByClassName('resize')
			var left = document.getElementsByClassName('left')
			var mid = document.getElementsByClassName('right')
			left[0].style.width = this.leftWidth + '%'
			mid[0].style.width = 99.15 - this.leftWidth + '%'
			var box = document.getElementsByClassName('box')
			for (let i = 0; i < resize.length; i++) {
				// 鼠标按下事件
				resize[i].onmousedown = function (e) {
					//颜色改变提醒
					resize[i].style.background = '#818181'
					var startX = e.clientX
					// 鼠标拖动事件
					document.onmousemove = function (e) {
						resize[i].left = startX - 120
						var endX = e.clientX
						var moveLen = resize[i].left + (endX - startX) // （endx-startx）=移动的距离。resize[i].left+移动的距离=左边区域最后的宽度
						var maxT = box[i].clientWidth - resize[i].offsetWidth // 容器宽度 - 左边区域的宽度 = 右边区域的宽度
						if (moveLen < 32) moveLen = 32 // 左边区域的最小宽度为32px
						if (moveLen > maxT - 150) moveLen = maxT - 150 //右边区域最小宽度为150px
						resize[i].style.left = moveLen // 设置左侧区域的宽度
						for (let j = 0; j < left.length; j++) {
							left[j].style.width = (moveLen / document.body.clientWidth) * 100 + '%'
							mid[j].style.width = 99 - (moveLen / document.body.clientWidth) * 100 + '%'
						}
					}
					// 鼠标松开事件
					document.onmouseup = function (evt) {
						//颜色恢复
						resize[i].style.background = '#d6d6d6'
						document.onmousemove = null
						document.onmouseup = null
						resize[i].releaseCapture && resize[i].releaseCapture() //当你不在需要继续获得鼠标消息就要应该调用ReleaseCapture()释放掉
					}
					resize[i].setCapture && resize[i].setCapture() //该函数在属于当前线程的指定窗口里设置鼠标捕获
					return false
				}
			}
			resize[0].style.height = box[0].offsetHeight + 'px'
		}
	}
}
</script>
<style lang="scss" scoped>
.resize {
	cursor: col-resize;
	background-color: #e6e6e6;
	border-radius: 5px;
	padding: 20px 0;
	width: 0.8%;
	height: 100%;
	line-height: 50px;
	font-size: 0.32rem;
	color: rgb(255, 255, 255);
}
/*拖拽区鼠标悬停样式*/
.resize:hover {
	color: #444444;
}

.box {
	display: flex;
	width: 100%;
	height: 100%;
	background-color: #e2e2e265;
}
</style>

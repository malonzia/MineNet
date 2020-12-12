<template>
	<view>
		<view class="header">
			<view class="input-view">
				<uni-icons type="search" size="22" color="#aaff00" />
				<input v-model="search" @keyup.enter='confirm' @input="confirm" confirm-type="search" class="input" type="text" placeholder="输入搜索关键词" @confirm="confirm">
			</view>
		</view>
		<view>
			<!-- <uni-section title="左侧滑出" type="line"></uni-section> -->
			<view class="example-body">
				<!-- <view class="word-btn draw-cotrol-btn" hover-class="word-btn--hover" :hover-start-time="20" :hover-stay-time="70" @click="showDrawer('showLeft')"><text class="word-btn-white">显示Drawer</text></view> -->
				<uni-drawer ref="showLeft" mode="left" :width="200" @change="change($event,'showLeft')">
					<!-- <view class="close">
						<view class="word-btn" hover-class="word-btn--hover" :hover-start-time="20" :hover-stay-time="70" @click="closeDrawer('showLeft')"><text class="word-btn-white">关闭Drawer</text></view>
					</view> -->
				</uni-drawer>
			</view>
		</view>

		<uni-section title="关系网列表" type="line"></uni-section>

		<view>
			<view v-if="searchData.length>0" class="list">
				<view class="flex_col" @longpress="onLongPress" :class="{'active':pickerUserIndex==index}" @tap="openinfo"
				 v-for="(item,index) in searchData" :key="index" :data-index="index" :data-id=item.id>
					<image :src=item.BG mode="aspectFill"></image>
					<view class="flex_grow">
						<view class="flex_col">
							<view class="flex_grow">{{item.name}}</view>
							<!-- <view class="time">{{item.time}}</view> -->
						</view>
						<!-- <view class="info">{{item.synopsis}}</view> -->
					</view>
				</view>
			</view>
			<view v-else class="list">
				<view class="flex_col" @longpress="onLongPress" :class="{'active':pickerUserIndex==index}" @tap="openinfo"
				 v-for="(item,index) in list" :key="index" :data-index="index" :data-id=item.id>
					<image :src=item.BG mode="aspectFill"></image>
					<view class="flex_grow">
						<view class="flex_col">
							<view class="flex_grow">{{item.name}}</view>
							<!-- <view class="time">{{item.time}}</view> -->
						</view>
						<!-- <view class="info">{{item.info}}</view> -->
					</view>
				</view>
			</view>
			<view class="shade" v-show="showShade" @tap="hidePop">
				<view class="pop" :style="popStyle" :class="{'show':showPop}">
					<view v-for="(item,index) in popButton" :key="index" @tap="pickerMenu" :data-index="index">{{item}}</view>

				</view>
			</view>
		</view>


		<view>
			<view class="uni-fab-box">
				<uni-fab ref="fab" :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction"
				 @trigger="trigger" />
			</view>
			 <!-- @fabClick="fabClick" -->
			
		</view>
	</view>

</template>
<script>
	export default {


		data() {
			return {
				//搜索
				search: '',
				searchData: [
					
				],

				userList: [],
				/* 窗口尺寸 */
				winSize: {},
				/* 显示遮罩 */
				showShade: false,
				/* 显示操作弹窗 */
				showPop: false,
				/* 弹窗按钮列表 */
				/*"标为关注", "置顶聊天",*/
				popButton: ["删除该聊天"],
				/* 弹窗定位样式 */
				popStyle: "",
				/* 选择的用户下标 */
				pickerUserIndex: -1,

				//list索引号
				mid: 0,
				//
				netid: 0,

				list: [
					
				],




				showRight: false,
				showLeft: false,
				//悬浮按钮
				title: 'uni-fab',
				directionStr: '垂直',
				horizontal: 'right',
				vertical: 'bottom',
				direction: 'horizontal',
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
				},

				//加号按钮内的选项
				content: [{
						iconPath: '/static/component.png',
						selectedIconPath: '/static/componentHL.png',
						id: 1,
						text: '新建网',
						active: false,

					},
					// {
					// 	iconPath: '/static/api.png',
					// 	selectedIconPath: '/static/apiHL.png',
					// 	text: 'API',
					// 	active: false
					// },
					// {
					// 	iconPath: '/static/template.png',
					// 	selectedIconPath: '/static/templateHL.png',
					// 	id: 2,
					// 	text: '修改关系网',
					// 	active: false,

					// }
				]
			}
		},
		onShow: function() {
			uni.getStorage({
				key: 'list',
				success: (res) => {
					if (res.data.length != 0) {
						this.list = res.data;
					}
					console.log('set');
					console.log(this.list);
					// uni.hideLoading()
				}
			});
		},

		onLoad: function() {
			// uni.showLoading({
			// 	title: "加载中...."
			// })

			// uni.removeStorage({
			//     key: 'list',
			//     success: function (res) {
			//         console.log('success');
			//     }
			// });
			uni.getStorage({
				key: 'list',
				success: (res) => {
					if (res.data.length != 0) {
						this.list = res.data;
					}
					console.log('set');
					console.log(res.data);
					// uni.hideLoading()
				}
			});

			this.getWindowSize();

			// #ifdef H5
			document.onLong = function(e) {
				var e = e || window.event;
				e.preventDefault();
			};
			// #endif

		},

		
		methods: {
			//悬浮按钮
			trigger(e) {
				console.log(e)
				console.log(e.item.id)
				// this.content[e.index].active = !e.item.active
				uni.showModal({
					title: '提示',
					content: `您即将跳转到${e.item.text}`,
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定')
							uni.navigateTo({
								//即将跳转到的页面
								url: '../newNetwork/newNetwork'
							})
						} else if (res.cancel) {
							console.log('用户点击取消')
						}
					}
				})


			},
			fabClick() {
				uni.showToast({
					title: '点击了悬浮按钮',
					icon: 'none'
				})
			},
			switchBtn(hor, ver) {
				if (hor === 0) {
					this.direction = this.direction === 'horizontal' ? 'vertical' : 'horizontal'
					this.directionStr = this.direction === 'horizontal' ? '垂直' : '水平'
				} else {
					this.horizontal = hor
					this.vertical = ver
				}
				this.$forceUpdate()
			},




			//抽屉
			confirm() {
				var search = this.search;
				if (!search) {
					search=' '
				}
				this.searchData = this.list.filter(function(network) {
					// 每一项数据
					// console.log()
					return Object.keys(network).some(function(key) {
						// 每一项数据的参数名
						// console.log(key)
						return (
							String(network[key])
							// toLowerCase() 方法用于把字符串转换为小写。
							.toLowerCase()
							// indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。
							.indexOf(search) > -1
						);
					});
				});
			},


			// 打开窗口
			showDrawer(e) {
				this.$refs[e].open()
			},
			// 关闭窗口
			closeDrawer(e) {
				this.$refs[e].close()
			},
			// 抽屉状态发生变化触发
			change(e, type) {
				console.log((type === 'showLeft' ? '左窗口' : '右窗口') + (e ? '打开' : '关闭'));
				this[type] = e
			},

			//打开关系网
			openinfo(e) {
				console.log(e);
				uni.navigateTo({
					url: '../view-network/view-network?id=' + e.currentTarget.dataset.id + '&netid=' + e.currentTarget.dataset.index,
	
				})
			},
			
			//删除关系网
			/* 列表触摸事件 */
			listTap() {
				/* 因弹出遮罩问题，所以需要在遮罩弹出的情况下阻止列表事件的触发 */
				if (this.showShade) {
					return;
				}

				console.log("列表触摸事件触发")
			},
			/* 获取窗口尺寸 */
			getWindowSize() {
				uni.getSystemInfo({
					success: (res) => {
						this.winSize = {
							"witdh": res.windowWidth,
							"height": res.windowHeight
						}
					}
				})
			},
			/* 长按监听 */
			onLongPress(e) {
				console.log('sus');
				console.log(e);
				let [touches, style, index] = [e.touches[0], "", e.currentTarget.dataset.index];
				this.mid = e.currentTarget.dataset.index;
				this.netid = e.currentTarget.dataset.id;
				console.log('netid' + this.netid);
				/* 因 非H5端不兼容 style 属性绑定 Object ，所以拼接字符 */
				if (touches.clientY > (this.winSize.height / 2)) {
					style = `bottom:${this.winSize.height-touches.clientY}px;`;
				} else {
					style = `top:${touches.clientY}px;`;
				}
				if (touches.clientX > (this.winSize.witdh / 2)) {
					style += `right:${this.winSize.witdh-touches.clientX}px`;
				} else {
					style += `left:${touches.clientX}px`;
				}

				this.popStyle = style;
				this.pickerUserIndex = Number(index);
				this.showShade = true;
				this.$nextTick(() => {
					setTimeout(() => {
						this.showPop = true;
					}, 10);
				});
			},
			/* 隐藏弹窗 */
			hidePop() {
				this.showPop = false;
				this.pickerUserIndex = -1;
				setTimeout(() => {
					this.showShade = false;
				}, 250);
			},
			/* 选择菜单 */
			pickerMenu(e) {
				// 	let index = Number(e.currentTarget.dataset.index);
				// 	console.log(`第${this.pickerUserIndex+1}个用户,第${index+1}个按钮`);
				// 	// 在这里开启你的代码秀

				// 	uni.showToast({
				// 		title: `第${this.pickerUserIndex+1}个用户,第${index+1}个按钮`,
				// 		icon: "none",
				// 		mask: true,
				// 		duration: 600
				// 	});
				if (this.list.length != 0) {
					this.list.splice(this.mid, 1);
				}
				uni.removeStorage({
					key: 'gragh' + this.netid
				});
				uni.setStorage({
					key: 'list',
					data: this.list
				});
				uni.navigateTo({
					url: ''
				})

				/* 
				 因为隐藏弹窗方法中会将当前选择的用户下标还原为-1,
				 如果行的菜单方法存在异步情况，请在隐藏之前将该值保存，或通过参数传入异步函数中
				 */
				this.hidePop();
			}


		},

		onNavigationBarButtonTap(e) {
			if (this.showLeft) {
				this.$refs.showLeft.close()
			} else {
				this.$refs.showLeft.open()
			}
		},
		// app端拦截返回事件 ，仅app端生效
		onBackPress() {
			if (this.showRight || this.showLeft) {
				this.$refs.showLeft.close()
				this.$refs.showRight.close()
				return true
			}
		},


	}
</script>

<style scoped lang="scss">
	@charset "UTF-8";

	/* 头条小程序组件内不能引入字体 */
	/* #ifdef MP-TOUTIAO */
	@font-face {
		font-family: uniicons;
		font-weight: normal;
		font-style: normal;
		src: url("~@/static/uni.ttf") format("truetype");
	}

	/* #endif */
	/* #ifndef APP-NVUE */
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #efeff4;
		min-height: 100%;
		height: auto;
	}

	view {
		font-size: 14px;
		line-height: inherit;
	}

	.example {
		padding: 0 15px 15px;
	}

	.example-info {
		padding: 15px;
		color: #3b4144;
		background: #ffffff;
	}

	.example-body {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: center;
		padding: 0;
		font-size: 14px;
		background-color: #ffffff;
	}

	/* #endif */
	.example {
		padding: 0 15px;
	}

	.example-info {
		/* #ifndef APP-NVUE */
		display: block;
		/* #endif */
		padding: 15px;
		color: #3b4144;
		background-color: #ffffff;
		font-size: 14px;
		line-height: 20px;
	}

	.example-info-text {
		font-size: 14px;
		line-height: 20px;
		color: #3b4144;
	}

	.example-body {
		flex-direction: column;
		padding: 15px;
		background-color: #ffffff;
	}

	.word-btn-white {
		font-size: 18px;
		color: #FFFFFF;
	}

	.word-btn {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: center;
		border-radius: 6px;
		height: 48px;
		margin: 15px;
		background-color: #007AFF;
	}

	.word-btn--hover {
		background-color: #4ca2ff;
	}

	.header {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		padding: 10px 15px;
		align-items: center;
		border-top-width: 1px;
		border-top-color: #f5f5f5;
		border-top-style: solid;
		background-color: #ffffff;
	}

	.input-view {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		align-items: center;
		flex-direction: row;
		background-color: #e7e7e7;
		height: 30px;
		border-radius: 15px;
		padding: 0 10px;
		flex: 1;
		background-color: #f5f5f5;
	}

	.uni-drawer-info {
		background-color: #ffffff;
		padding: 15px;
		padding-top: 5px;
		color: #3b4144;
	}

	.uni-padding-wrap {
		padding: 0 15px;
		line-height: 1.8;
	}

	.input {
		flex: 1;
		padding: 0 5px;
		height: 24px;
		line-height: 24px;
		font-size: 14px;
		background-color: transparent;
	}

	.close {
		padding: 15px;
	}

	.example-body {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		padding: 0;
	}

	.draw-cotrol-btn {
		flex: 1;
	}

	.info {
		padding: 15px;
		color: #666;
	}

	.info-text {
		font-size: 14px;
		color: #666;
	}

	.scroll-view {
		/* #ifndef APP-NVUE */
		width: 100%;
		height: 100%;
		/* #endif */
		flex: 1;
	}

	.scroll-view-box {
		flex: 1;
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
	}

	.info-content {
		padding: 5px 15px;
	}

	.flex_col {
		display: flex;
		flex-direction: row;
		flex-wrap: nowrap;
		justify-content: flex-start;
		align-items: center;
		align-content: center;
	}

	/* 弹性盒子弹性容器 */
	.flex_col .flex_grow {
		width: 0;
		-webkit-box-flex: 1;
		-ms-flex-positive: 1;
		flex-grow: 1;
	}

	.flex_row .flex_grow {
		-webkit-box-flex: 1;
		-ms-flex-positive: 1;
		flex-grow: 1;
	}

	/* 弹性盒子允许换行 */
	.flex_col.flex_wrap {
		-ms-flex-wrap: wrap;
		flex-wrap: wrap;
	}

	/* 列表 */
	.list {
		background-color: #fff;
		font-size: 28upx;
		color: #333;
		user-select: none;
		touch-callout: none;

		&>view {
			padding: 24upx 30upx;
			position: relative;

			&:active,
			&.active {
				background-color: #f3f3f3;
			}

			image {
				height: 80upx;
				width: 80upx;
				border-radius: 4px;
				margin-right: 20upx;
			}

			&>view {
				line-height: 40upx;

				.time,
				.info {
					color: #999;
					font-size: 24upx;
				}

				.time {
					width: 150upx;
					text-align: right;
				}

				.info {
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
				}
			}
		}

		&>view:not(:first-child) {
			margin-top: 1px;

			&::after {
				content: '';
				display: block;
				height: 0;
				border-top: #CCC solid 1px;
				width: 620upx;
				position: absolute;
				top: -1px;
				right: 0;
				transform: scaleY(0.5);
				/* 1px像素 */
			}
		}
	}

	/* 遮罩 */
	.shade {
		position: fixed;
		z-index: 100;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		-webkit-touch-callout: none;

		.pop {
			position: fixed;
			z-index: 101;
			width: 200upx;
			box-sizing: border-box;
			font-size: 28upx;
			text-align: left;
			color: #333;
			background-color: #fff;
			box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
			line-height: 80upx;
			transition: transform 0.15s ease-in-out 0s;
			user-select: none;
			-webkit-touch-callout: none;
			transform: scale(0, 0);

			&.show {
				transform: scale(1, 1);
			}

			&>view {
				padding: 0 20upx;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
				user-select: none;
				-webkit-touch-callout: none;

				&:active {
					background-color: #f3f3f3;
				}
			}
		}
	}
</style>

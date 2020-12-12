<template>
	<view>
		<uni-list class="nodes">
			<!-- <avatar class="ntimg" :value="network.BG" v-model="network.BG" selWidth="200px" selHeight="400upx" @upload="myUpload"
			 :avatarSrc="network.BG" avatarStyle="width: 200upx; height: 200upx; border-radius: 10%;">
			</avatar> -->
			<image style="border-radius: 20%; margin: 0 auto; left: 0; right: 0;" :src=network.BG mode="aspectFill"></image>
			<button type="default" @click="addnodeimg">上传图片</button>

			<view class="input-view">
				<view class="title" style="color: #66AAFF;">名称</view>
				<input v-model="network.name" confirm-type="search" class="input" type="text" placeholder="请输入关系网名称">
			</view>
			<view class="input-view-message">
				<view class="title" style="color: #66AAFF;">简介</view>
				<textarea v-model="network.synopsis" confirm-type="search" class="input-message" type="text" placeholder="请输入简介"
				 auto-height=true maxlength=-1 />
				</view>

		</uni-list>
		<button @click="getdata">确认</button>
	</view>
</template>

<script>
	import avatar from "../../components/yq-avatar/yq-avatar.vue"
	export default {
		data() {
			return {
				url: "../../static/logo.png",
				sysss:{},
				network: {
					name: '',
					BG: "../../static/logo.png",
					id: 0,
					synopsis: ''
				},
				list: [

				],
				id: -1,
			}
		},
		watch:{
			network:'onEditorReady'
		},
		methods: {

			//上传图片
			//上传图片
			addnodeimg(){
				var _this = this
				uni.chooseImage({
				  success: function (res) {
				    var tempFilePaths = res.tempFilePaths;
				    uni.saveFile({
				      tempFilePath: tempFilePaths[0],
				      success: function (res) {
				        var savedFilePath = res.savedFilePath;
						_this.network.BG = savedFilePath
				      }
				    });
				  }
				});
				
				console.log("我是大笨蛋"+this.nodeimg)
			},
			
			
			

			// //图片
			// myUpload(rsp) {
			// 	this.url = rsp.path; //更新头像方式一
			// 	this.network.BG = rsp.path
			// 	//rsp.avatar.imgSrc = rsp.path; //更新头像方式二
				
			// },


			getdata() {
				// uni.removeStorage({
				//     key: 'list',
				//     success: function (res) {
				//         console.log('success');
				//     }
				// });
				
				// try {  
				// uni.clearStorageSync();  
				// } catch (e) {  
				// // error  
				// }
				
				try {
					const value = uni.getStorageSync('list');
					if (value) {
						this.list = value;
					}
				} catch (e) {
					// error
				}
				
				// uni.saveFile({
				//   tempFilePath: this.network.BG,
				//   success: function (res) {
				//     this.network.BG = res.savedFilePath;
				// 	console.log(this.network.BG)
				//   }
				// });

				if ((this.list.length) != 0) {
					console.log('success');
					this.network.id = this.list[this.list.length - 1].id + 1;
				}
				this.list.push(this.network);
				
				try {
						uni.setStorageSync('list', this.list);
				} catch (e) {
					// error
				}
				
				uni.navigateTo({
					// url: '../network/network?id=' + this.network.id
					url: '../index/index'
				});
			},
		},
			
		components: {
			avatar
		},
		
		
	}
</script>

<style>
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
	
	
	
	.ntimg {
		margin: auto;
	}
	
	.nodes {
		background-color: #818181;
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
	
	.input-view-message {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		align-items: flex-start;
		flex-direction: row;
		background-color: #e7e7e7;
		height: 250px;
		border-radius: 15px;
		padding: 0 10px;
		flex: 1;
		background-color: #f5f5f5;
	}
	.input {
		flex: 1;
		padding: 0 5px;
		height: 50px;
		line-height: 24px;
		font-size: 14px;
		background-color: transparent;
	}
	.input-message{
		flex: 1;
		padding: 0 5px;
		height: 20px;
		line-height: 20px;
		size: 10px;
		font-size: 14px;
		background-color: transparent;
	}
</style>

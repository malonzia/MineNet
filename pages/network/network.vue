<template>
	<view class="content">
		<view class="p" :prop='gragh' :change:prop='d3.initEcharts'></view>
		<view class="p" :prop='flag_link' :change:prop='d3.initEcharts'></view>
		<view class="p" :prop='flag_node' :change:prop='d3.initEcharts'></view>
		<view class="p" :prop='flag_update_node' :change:prop='d3.initEcharts'></view>
		<svg></svg>
		<wyb-popup ref="popup" type="center" height="730" width="500" radius="6" :showCloseIcon="false" :scrollY="true">
			<view class="popup-content" style=" padding-top: 30upx; margin: 0 auto; top: 0; bottom: 0;">
				<view class="input-view">
					<view class="title" style="color: #66AAFF;">名称</view>
					<input v-model="name" confirm-type="search" class="input" type="text" placeholder="请输入节点名称">
				</view>
				<view class="input-view-message">
					<view class="title" style="color: #66AAFF;">简介</view>
					<!-- <input v-model="nodemessage" confirm-type="search" class="input-message" type="text" placeholder="请输入简介"> -->
					<textarea v-model="nodemessage" confirm-type="search" class="input-message" type="text" placeholder="请输入简介"
					 auto-height=true maxlength=-1 />
					</view>
				<button type="default" @click="addnodeimg" style="width: 50%; float: left;">上传图片</button>
				<button @click="close" style="width: 50%; float: right;">确认</button>
			</view>
		</wyb-popup>
		<wyb-popup ref="popup1" type="center" height="400" width="500" radius="6" :showCloseIcon="false" :scrollY="true">
			<view class="popup-content">
				关系 <input class="uni-input" v-model="relation" focus placeholder="请输入关系" />
				长度 <input class="uni-input" v-model="value" focus placeholder="请输入长度(点之间斥力,建议小于2)" />
				<button @click="add_link" style="width: 50%; float: left;">确认</button>
				<button @click="delete_link" style="width: 50%; float: right;">删除关系</button>
			</view>
		</wyb-popup>
		<wyb-popup ref="popup2" type="center" height="780" width="500" radius="6" :showCloseIcon="false" :srollY="true">
			<view class="popup-content" >
				<!-- <view class="title">姓名</view>
				<input class="uni-input" v-model="name" focus placeholder="请输入姓名" /> -->
				<view class="input-view">
					<view class="title" style="color: #66AAFF;">名称</view>
					<input v-model="name" confirm-type="search" class="input" type="text" placeholder="请修改节点名称">
				</view>
				<view class="input-view-message">
					<view class="title" style="color: #66AAFF;">简介</view>
					<textarea v-model="nodemessage" confirm-type="search" class="input-message" type="text" placeholder="请修改简介" auto-height=true maxlength=-1 />
				</view>
				<view>
					<button type="default" @click="addnodeimg" style="width: 50%; float: left;">修改图片</button>
					<button @click="click_update_node" style="width: 50%; float: right;">保存</button>
				</view>
				<button @click="delete_node" >删除节点</button>
				
			</view>
			
		</wyb-popup>

		<wyb-popup ref="popup3" type="center" height="530" width="500" radius="6" :showCloseIcon="false" :scrollY="true">
			<view class="popup-content" >
				<!-- 图文卡片模式 -->
				<uni-card style="top: 0; bottom: 0; margin: 0 auto; border-radius: 6;"  v-model="node" :title="node.name" mode="style" :is-shadow="true" :thumbnail="node.nodeimg"
				  note="Tips">
					<h2>简介</h2>
					<p>{{node.nodemessage}}</p>
				</uni-card>
			</view>
		</wyb-popup>

		<uni-fab ref="fab" :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction"
		 @trigger="trigger" />
		 
		  <!-- @fabClick="fabClick" -->
	</view>
</template>

<script>
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	export default {
		components: {
			wybPopup
		},
		data() {
			return {
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
				content: [{
						iconPath: '/static/component.png',
						selectedIconPath: '/static/componentHL.png',
						text: '添加节点',
						active: false
					},
					{
						iconPath: '/static/api.png',
						selectedIconPath: '/static/apiHL.png',
						text: '修改节点',
						active: false
					},
					{
						iconPath: '/static/template.png',
						selectedIconPath: '/static/templateHL.png',
						text: '添加关系',
						active: false
					}
				],

				nodeid: -1,
				name: '',
				nodemessage: '',
				nodeimg:'',

				node: [

				],

				relation: '',
				value: 0,
				id: -1,
				flag_link: false,
				flag_node: false,
				flag_update_node: false,
				_link: {},
				_node: {},
				gragh: {
					nodes: [],
					edges: [],
					windowWidth: 0,
					windowHeight: 0,
				},

			}
		},
		onLoad: function(e) {
			this.id = e.id;
			console.log('onload+++++');
			console.log(e)
		},
		onBackPress() {
			if (this.$refs.fab.isShow) {
				this.$refs.fab.close()
				return true
			}
			return false
		},

		methods: {

			//悬浮
			trigger(e) {
				var _this = this;
				console.log(e)
				this.content[e.index].active = !e.item.active
				uni.showModal({
					title: '提示',
					content: `您${this.content[e.index].active ? '选中了' : '取消了'}${e.item.text}`,
					success: function(res) {
						if (res.confirm) {
							if (e.index == 0) {
								_this.Addnode()
								if (_this.flag_node == true) {
									for (var i = 0; i < _this.content.length; i++) {
										if (i != 0)
											_this.content[i].active = false;
										else
											_this.content[i].active = true;
									}
								} else
									_this.content[0].active = false;
							} else if (e.index == 1) {
								_this.Update_node()
								if (_this.flag_update_node == true) {
									for (var i = 0; i < _this.content.length; i++) {
										if (i != 1)
											_this.content[i].active = false;
										else
											_this.content[i].active = true;
									}
								} else
									_this.content[1].active = false;
							} else {
								_this.Addlink()
								if (_this.flag_link == true) {
									for (var i = 0; i < _this.content.length; i++) {
										if (i != 2)
											_this.content[i].active = false;
										else
											_this.content[i].active = true;
									}
								} else
									_this.content[2].active = false;
							}
							console.log('用户点击确定')
						} else if (res.cancel) {
							console.log('用户点击取消')
							if (e.index == 0) {
								_this.content[0].active = !_this.content[0].active;
							} else if (e.index == 1) {
								_this.content[1].active = !_this.content[1].active;
							} else {
								_this.content[2].active = !_this.content[2].active;
							}
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
					this.directionStr = this.direction === 'horizontal' ? '水平' : '垂直'
				} else {
					this.horizontal = hor
					this.vertical = ver
				}
				this.$forceUpdate()
			},


			open() {
				this.name=null
				this.nodemessage=null
				this.nodeimg=null
				this.$refs.popup.show()
			},
			open1(_link) {
				this.$refs.popup1.show()
				this._link = _link
			},
			open2(e) {
				console.log("open2" + e)
				console.log(this.gragh)
				console.log(this.gragh.nodes[e].id)

				try {
					const value = uni.getStorageSync('graph' + this.id + 'node' + this.gragh.nodes[e].id);
					if (value) {
						this.node = value;
					}
				} catch (e) {
					// error
				}
				console.log(this.node)
				this.$refs.popup3.show()
				// uni.navigateTo({
				// 	url:"../card/card"
				// })
			},
			Update_node() {
				if (this.flag_update_node == false) {
					this.flag_update_node = true;
					this.flag_link = false;
					this.flag_node = false;
				} else
					this.flag_update_node = false;
				console.log(this.flag_update_node)
				this.$refs.popup1.hide();
			},
			
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
						_this.nodeimg = savedFilePath
				      }
				    });
				  }
				});
				
				console.log("我是大笨蛋"+this.nodeimg)
			},
			
			//新建点
			close() {
				var node = {};
				console.log(this.name)
				console.log(this.nodeimg)
				try {
					const value = uni.getStorageSync('gragh' + this.id);
					if (value) {
						this.gragh = value;
					}
				} catch (e) {
					// error
				}
				node['name'] = this.name;
				if (this.gragh.nodes.length != 0) {
					node['id'] = this.gragh.nodes[this.gragh.nodes.length - 1].id + 1
				} else {
					node['id'] = 0
				}
				node['nodemessage'] = this.nodemessage;
				// node[]
				this.gragh.nodes.push(node);
				console.log(this.gragh.nodes)
				uni.setStorage({
					key: 'gragh' + this.id,
					data: this.gragh,
				})
				
				node['nodeimg'] = this.nodeimg
				// console.log('不是大笨蛋'+node['nodeimg'])
				uni.setStorage({
					key: 'graph' + this.id + 'node' + node['id'],
					data: node
				})
				this.$refs.popup.hide();
			},
			
			click_update_node() {
				
				try {
					const value = uni.getStorageSync('gragh' + this.id);
					if (value) {
						this.gragh = value;
					}
				} catch (e) {
					// error
				}
				
				try {
					const value = uni.getStorageSync('graph' + this.id + 'node' + this.gragh.nodes[this._node.index].id);
					if (value) {
						this.node = value;
					}
				} catch (e) {
					// error
				}
				
				this.gragh.nodes[this._node.index].name = this.name;
				this.node.name=this.name;
				this.node.nodemessage=this.nodemessage;
				
				uni.setStorage({
					key: 'gragh' + this.id,
					data: this.gragh,
				})
				this.node.nodeimg = this.nodeimg
				
				uni.setStorage({
					key: 'graph' + this.id + 'node' + this.node.id,
					data: this.node
				})
				this.$refs.popup2.hide();
				
			},
			delete_node() {
				try {
					const value = uni.getStorageSync('gragh' + this.id);
					if (value) {
						this.gragh = value;
					}
				} catch (e) {
					// error
				}
				console.log('this.gragh.edges.length')
				console.log(this.gragh.edges);
				for (var i = 0; i < this.gragh.edges.length; i++) {
					if (this._node.index == this.gragh.edges[i].source || this._node.index == this.gragh.edges[i].target) {
						console.log('this.gragh.edges[i].source')
						console.log(this.gragh.edges[i].source)
						this.gragh.edges.splice(i, 1);
						i = -1;
					}
				}
				console.log(this.gragh.nodes)
				for (var i = 0; i < this.gragh.edges.length; i++) {
					if (this.gragh.edges[i].source > this._node.index)
						this.gragh.edges[i].source = this.gragh.edges[i].source - 1;
					if (this.gragh.edges[i].target > this._node.index)
						this.gragh.edges[i].target = this.gragh.edges[i].target - 1;
				}
				this.gragh.nodes.splice(this._node.index, 1);
				//delete this.gragh.nodes[_node.index];
				uni.setStorage({
					key: 'gragh' + this.id,
					data: this.gragh,
				})
			},
			
			Addnode() {
				if (this.flag_node == false) {
					this.flag_node = true;
					this.flag_link = false;
					this.flag_update_node = false;
				} else
					this.flag_node = false;
				console.log(this.flag_node)
			},
			Addlink() {
				if (this.flag_link == false) {
					this.flag_link = true;
					this.flag_node = false;
					this.flag_update_node = false;
				} else
					this.flag_link = false;
				console.log(this.flag_link)
			},
			setgragh() {
				// uni.removeStorage({
				// 	key:'gragh'+this.id
				// })
				console.log('gragh' + this.id)
				uni.getStorage({
					key: 'gragh' + this.id,
					success: (res) => {
						this.gragh.nodes = res.data.nodes;
						this.gragh.edges = res.data.edges;
					}
				});
				console.log(this.gragh)
				uni.getSystemInfo({
					success: (res) => {
						this.gragh.windowHeight = res.windowHeight;
						this.gragh.windowWidth = res.windowWidth;
					}

				})
			},
			add_link() {
				try {
					const value = uni.getStorageSync('gragh' + this.id);
					if (value) {
						this.gragh = value;
					}
				} catch (e) {
					// error
				}
				var flag = true;
				for (var i = 0; i < this.gragh.edges.length; i++) {
					if (this._link.source == this.gragh.edges[i].source && this._link.target == this.gragh.edges[i].target) {
						this.gragh.edges[i].relation = this.relation;
						this.gragh.edges[i].value = this.value;
						flag = false;
						break;
					}
					if (this._link.source == this.gragh.edges[i].target && this._link.target == this.gragh.edges[i].source) {
						this.gragh.edges[i].relation = this.relation;
						this.gragh.edges[i].value = this.value;
						flag = false;
						break;
					}
				}
				if (flag) {
					this._link.relation = this.relation;
					this._link.value = this.value;
					this.gragh.edges.push(this._link);
				}
				uni.setStorage({
					key: 'gragh' + this.id,
					data: this.gragh,
				})
				
				this.$refs.popup1.hide();
			},
			delete_link() {
				try {
					const value = uni.getStorageSync('gragh' + this.id);
					if (value) {
						this.gragh = value;
						console.log(this.gragh)
					}
				} catch (e) {
					// error
				}
				for (var i = 0; i < this.gragh.edges.length; i++) {
					if (this._link.source == this.gragh.edges[i].source && this._link.target == this.gragh.edges[i].target) {
						this.gragh.edges.splice(i, 1);
						break;
					}
					if (this._link.source == this.gragh.edges[i].target && this._link.target == this.gragh.edges[i].source) {
						this.gragh.edges.splice(i, 1);
						break;
					}
				}
				// uni.setStorage({
				// 	key: 'gragh'+this.id,
				// 	data: this.gragh,
				// })
				console.log(this.gragh)
				try {
					uni.setStorageSync('gragh' + this.id, this.gragh);
					console.log(this.gragh)
				} catch (e) {
					// error
				}
				
				this.$refs.popup2.hide();
			},
			update_node(e) {
				this.$refs.popup2.show();
				console.log(e)
				try {
					const value = uni.getStorageSync('graph' + this.id + 'node' + this.gragh.nodes[e].id);
					if (value) {
						this.node = value;
						console.log(this.node)
					}
				} catch (e) {
					// error
				}
				this.name = this.node.name;
				this.nodemessage = this.node.nodemessage;
				this.nodeimg = this.node.nodeimg;
			}
			
		},
		created() {

		},
		mounted() {
			// 		  this.$nextTick(
			// 		 this.setgragh()
			// )
		},
		beforeMount() {},
		watch: {

		},
		components: {},
	};
</script>
<script module="d3" lang="renderjs">
	import * as d3 from '../../common/js/d3.v5.min.js'; //引入d3
	export default {
		data() {
			return {}
		},
		mounted() {
			// console.log('options.....', getCurrentPages()[0]);
			this.$ownerInstance.callMethod('setgragh');
			this.initEcharts();
		},
		methods: {
			initEcharts() {
				let _this = this;
				var flag_link = this.flag_link;
				var flag_node = this.flag_node;
				var flag_update_node = this.flag_update_node;
				var newObj = "";
				newObj = JSON.stringify(this.gragh);
				var count = 0;
				var delete_node;
				var _link = {};
				var _node = {};
				//准备数据
				var gragh = JSON.parse(newObj);
				var nodes = gragh.nodes;
				var edges = gragh.edges;
				//在浏览器的控制台输出
				console.log(gragh)
				d3.select('svg').selectAll('*').remove();
				var marge = {
					top: 60,
					bottom: 60,
					left: 60,
					right: 60
				}
				var svg = d3.select("svg").attr('width', this.gragh.windowWidth * 4)
					.attr('height', this.gragh.windowHeight * 2)
					.call(d3.zoom().scaleExtent([0.5, 10]).on('zoom', zoomed))
					.on('click', function() {
						if (flag_node)
							_this.$ownerInstance.callMethod('open');
					})
				var width = svg.attr("width")
				var height = svg.attr("height")
				console.log([width, height]);
				var g = svg.append("g")
					.attr("transform", "translate(" + marge.top + "," + marge.left + ")");

				//设置一个color的颜色比例尺，为了让不同的扇形呈现不同的颜色
				var colorScale = d3.scaleOrdinal()
					.domain(d3.range(nodes.length))
					.range(d3.schemeCategory10);

				//新建一个力导向图
				var forceSimulation = d3.forceSimulation()
					.force("link", d3.forceLink())
					.force("charge", d3.forceManyBody())
					.force("center", d3.forceCenter());

				//初始化力导向图，也就是传入数据
				//生成节点数据
				forceSimulation.nodes(nodes)
					.on("tick", ticked); //这个函数很重要，后面给出具体实现和说明
				//生成边数据
				forceSimulation.force("link")
					.links(edges)
					.distance(function(d) { //每一边的长度
						return d.value * 100;
					})
				//设置图形的中心位置	
				forceSimulation.force("center")
					.x(width * (16 / 35))
					.y(height * (1 / 5));
				//有了节点和边的数据后，我们开始绘制
				//绘制边
				var links = g.append("g")
					.selectAll("line")
					.data(edges)
					.enter()
					.append("line")
					.attr("stroke", function(d, i) {
						return colorScale(i);
					})
					.attr("stroke-width", 1);
				var linksText = g.append("g")
					.selectAll("text")
					.data(edges)
					.enter()
					.append("text")
					.text(function(d) {
						return d.relation;
					})
					.attr('fill-opacity', 0)

				//绘制节点
				//老规矩，先为节点和节点上的文字分组
				var gs = g.selectAll(".circleText")
					.data(nodes)
					.enter()
					.append("g")
					.attr("transform", function(d, i) {
						var cirX = d.x;
						var cirY = d.y;
						return "translate(" + cirX + "," + cirY + ")";
					})
					.on('mouseover', function(d, i) {
						//显示连接线上的文字
						linksText.style('fill-opacity', function(edge) {
							if (edge.source === d || edge.target === d) {
								return 1
							}
						})
						//连接线加粗
						links
							.style('stroke-width', function(edge) {
								if (edge.source === d || edge.target === d) {
									return '2px'
								}
							})
							.style('stroke', function(edge) {
								if (edge.source === d || edge.target === d) {
									return '#000'
								}
							})
					})
					.on('mouseout', function(d, i) {
						//隐去连接线上的文字
						linksText.style('fill-opacity', function(edge) {
							if (edge.source === d || edge.target === d) {
								return 0
							}
						})
						//连接线减粗
						links
							.style('stroke-width', function(edge) {
								if (edge.source === d || edge.target === d) {
									return '1px'
								}
							})
							.style('stroke', function(edge) {
								if (edge.source === d || edge.target === d) {
									return '#ddd'
								}
							})
					})
					.on('dblclick', function(d) {
						// _this.$router.push({
						// 	path: '../info/info'
						// })
						console.log(d.index)
						_this.$ownerInstance.callMethod('open2', d.index);
					})
					.on('click', function(d) {
						if (flag_link) {
							if (count == 0) {
								_link['source'] = d.index;
								count = count + 1;
							} else {
								_link['target'] = d.index;
								_link['relation'] = '籍贯';
								_link['value'] = 1;
								//console.log(_this.$data);			
								if (_link.source != _link.target)
									_this.$ownerInstance.callMethod('open1', _link);
								count = 0;
							}
						} else {
							count = 0;
						}
						if (flag_update_node) {
							// _node['index'] = d.index;
							_this.$ownerInstance.callMethod('update_node', d.index);
						}
					})
					.call(d3.drag()
						.on("start", started)
						.on("drag", dragged)
						.on("end", ended)
					);

				//绘制节点
				gs.append("circle")
					.attr("r", 10)
					.attr("fill", function(d, i) {
						return colorScale(i);
					})
				//文字
				gs.append("text")
					.attr("x", -10)
					.attr("y", -20)
					.attr("dy", 10)
					.text(function(d) {
						return d.name;
					})

				function zoomed() {
					//svg.attr('transform','translate(100,100)')
					var transform = d3.zoomTransform(this);
					//svg.attr('transform','translate('+kxy.x+','+kxy.y+')')
					svg.transition()
						.duration(30)
						.ease(d3.easeLinear)
						.attr("transform", "translate(" + transform.x + "," + transform.y + ") scale(" + transform.k + ")")
				}

				function debounce(func, wait) {
					let timer;
					return function() {
						let context = this; // 注意 this 指向
						let args = arguments; // arguments中存着e

						if (timer) clearTimeout(timer);

						timer = setTimeout(() => {
							func.apply(this, args)
						}, wait)
					}
				}

				function ticked() {
					links
						.attr("x1", function(d) {
							return d.source.x;
						})
						.attr("y1", function(d) {
							return d.source.y;
						})
						.attr("x2", function(d) {
							return d.target.x;
						})
						.attr("y2", function(d) {
							return d.target.y;
						});

					linksText
						.attr("x", function(d) {
							return (d.source.x + d.target.x) / 2;
						})
						.attr("y", function(d) {
							return (d.source.y + d.target.y) / 2;
						});

					gs
						.attr("transform", function(d) {
							return "translate(" + d.x + "," + d.y + ")";
						});
				}

				function started(d) {
					if (!d3.event.active) {
						forceSimulation.alphaTarget(0.8).restart();
					}
					d.fx = d.x;
					d.fy = d.y;
				}

				function dragged(d) {
					d.fx = d3.event.x;
					d.fy = d3.event.y;
				}

				function ended(d) {
					if (!d3.event.active) {
						forceSimulation.alphaTarget(0);
					}
					d.fx = null;
					d.fy = null;
				}
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}

	svg {
		touch-action: none;
	}

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
	.ntimg {
		margin: auto;
	}

	.nodes {
		background-color: #818181;
	}
</style>

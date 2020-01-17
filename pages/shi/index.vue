<template>
	<view id="shi">
		<uni-notice-bar @click="details()" class="topBar" :text="jinRiShiCi.content"></uni-notice-bar>
		<uni-search-bar @input="clear" @confirm="search" cancelButton="none"></uni-search-bar>
		<view class="list" @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
			<sib-list ref="sibList" :floterText="over" @isRefresh='isRefresh' @scrolltolowerFn="scrolltolowerFn">
				<uni-list slot="sibScrollList">
					<uni-list-item @click="toDetails(item.id)" :title="item.title" :note="item.poetName" v-for="item in shi" :key="item.id" :show-badge="true"
					 :badgeText="item.point">
					</uni-list-item>
				</uni-list>
			</sib-list>
		</view>
		<uni-popup ref="popup" type="center">
			<view class="topShi">
				<text class="title">{{jinRiShiCi.origin.title}}</text>
				<text class="author">{{jinRiShiCi.origin.dynasty}}---{{jinRiShiCi.origin.author}}</text>
				<text class="content" v-for="(item,index) in jinRiShiCi.origin.content" :key="index">{{item}}</text>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	import jinRiShiCi from '@/utils/jinrishici.js'
	import uniNoticeBar from '@/components/uni-notice-bar/uni-notice-bar.vue'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniSearchBar from '@/components/uni-search-bar/uni-search-bar.vue'
	import uniList from '@/components/uni-list/uni-list.vue'
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	import sibList from '@/components/sib-list/sib-list.vue'
	export default {
		components: {
			uniNoticeBar,
			uniPopup,
			uniSearchBar,
			uniList,
			uniListItem,
			sibList
		},
		data() {
			return {
				jinRiShiCi: {},
				shi: [],
				pages: '',
				page: 1,
				keyWord: '',
				over:'数据加载中...'
			}
		},
		onLoad(options) {
			if(options.name){
				this.keyWord=options.name;
				this.loadData({
				page: 1,
				size: 15,
				keyWord:this.keyWord
			})
			}
		},
		async created() {
			jinRiShiCi.load(res => {
				this.jinRiShiCi = res.data
			})
			this.loadData({
				page: 1,
				size: 15
			})
		},
		methods: {
			details() {
				this.$refs.popup.open()
			},
			loadData(data) {
				const that = this;
				uni.request({
					url: "https://api.cat-shop.penkuoer.com/api/v2/proxy",
					method: 'POST',
					timeout: 8000,
					data: {
						headers: {
							"content-type": "application/x-www-form-urlencoded",
						},
						url: 'http://www.codestock.club/tang/api/poem/getPoemInfo',
						data,
						method: 'POST'
					},
					success(res) {
						that.shi = [...that.shi, ...res.data.list]
						that.pages = res.data.pages
						if(that.pages==that.page){
							that.over='没有更多了'
						}
					},
					fail(err) {
						console.log(err)
					}
				})
				// uni.request({
				// 	url: "http://www.codestock.club/tang/api/poem/getPoemInfo",
				// 	data,
				// 	method: 'POST',
				// 	timeout: 8000,
				// 	header: {
				// 		"content-type": "application/x-www-form-urlencoded"
				// 	},
				// 	success(res) {
				// 		that.shi = [...that.shi, ...res.data.list]
				// 		that.pages = res.data.pages
				// 		if(that.pages==that.page){
				// 			that.over='没有更多了'
				// 		}
				// 	},
				// 	fail(err) {
				// 		console.log(err)
				// 	}
				// })
			},
			loadMore(keyWord) {
				if (this.page < this.pages) {
					this.page += 1;
					this.loadData({
						page: this.page,
						size: 15,
						keyWord
					})
				}
			},
			clear(value) {
				if (value) {
					if (value.value == '') {
						this.page=1;
						this.shi = [];
						this.keyWord = '';
						this.loadData({
							page: 1,
							size: 15,
							keyWord: value.value
						})
					}
				}
			},
			search(value) {
				if (value) {
					if (value.value) {
						this.page=1;
						this.shi = [];
						this.keyWord = value.value;
						this.loadData({
							page: 1,
							size: 15,
							keyWord: value.value
						})
					}
				}
			},
			touchstart: function(e) {
				this.$refs.sibList.refreshStart(e);
			},
			touchmove: function(e) {
				this.$refs.sibList.refreshMove(e);
			},
			touchend: function(e) {
				this.$refs.sibList.refreshEnd(e);
			},
			isRefresh: function() {
				uni.reLaunch({
					url: '/pages/shi/index'
				});
			},
			scrolltolowerFn: function() {
				if (this.page < this.pages) {
					this.loadMore(this.keyWord)
				}else{
					this.over='没有更多了'
				}
			},
			toDetails(id){
				uni.navigateTo({
					url:'./details?id='+id
				})
			}
		}
	}
</script>

<style>
	#shi {
		display: flex;
		flex-direction: column;
		height: 100%;
	}

	.topBar {
		text-align: center;
		margin: 0;
	}

	.list {
		flex: 1;
		overflow: auto;
	}

	text {
		display: block;
	}

	.topShi {
		width: 75%;
		max-height:350px;
		overflow: auto;
		padding: 1em;
		background-color: #fff;
		margin: auto;
		border-radius: 5px;
	}

	.title {
		text-align: center;
	}

	.author {
		text-align: right;
		color: #999;
	}

	.content {
		text-indent: 2em;
	}
</style>

<template>
	<view id="news" @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
		<sib-list ref="sibList" @isRefresh='isRefresh' @scrolltolowerFn="scrolltolowerFn">
			<uni-list slot="sibScrollList">
				<uni-list-item @click="toDetails(item.docid)" :key="item.docid" v-for="item in list" :title="item.title" :note="item.time"></uni-list-item>
			</uni-list>
		</sib-list>
	</view>
</template>

<script>
	import uniList from "@/components/uni-list/uni-list.vue"
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	import sibList from '@/components/sib-list/sib-list.vue'
	export default {
		components: {
			uniList,
			uniListItem,
			sibList
		},
		data() {
			return {
				page: 1,
				list: []
			}
		},
		created() {
			this.loadData(1)
		},
		methods: {
			loadData(page) {
				const that = this;
				uni.request({
					url: "https://api.cat-shop.penkuoer.com/api/v2/proxy",
					method: 'GET',
					timeout: 8000,
					data: {
						url: "http://v1.alapi.cn/api/new/toutiao?start=" + page + "&num=15",
						method: 'GET'
					},
					success(res) {
						if(that.list.length==0){
							that.list=res.data.data
						}else{
							let obj=res.data.data;
							let arr =[];
							for(let i=(page-1)*15;i<page*15-1;i++){
								arr.push(obj[i])
							}
							let result= arr.filter((v)=>{return v!==undefined})
							that.list=[...that.list,...result]
						}
					},
					fail(err) {
						console.log(err)
					}
				})
				// uni.request({
				// 	url: "http://v1.alapi.cn/api/new/toutiao?start=" + page + "&num=15",
				// 	timeout: 8000,
				// 	success(res) {
				// 		if(that.list.length==0){
				// 			that.list=res.data.data
				// 		}else{
				// 			let obj=res.data.data;
				// 			let arr =[];
				// 			for(let i=(page-1)*15;i<page*15-1;i++){
				// 				arr.push(obj[i])
				// 			}
				// 			let result= arr.filter((v)=>{return v!==undefined})
				// 			that.list=[...that.list,...result]
				// 		}
				// 	},
				// 	fail(err) {
				// 		console.log(err)
				// 	}
				// })
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
					url: '/pages/news/index'
				});
			},
			scrolltolowerFn: function() {
				this.page+=1;
				this.loadData(this.page);
			},
			toDetails(id) {
				uni.navigateTo({
					url: './details?id=' + id
				})
			}
		}
	}
</script>

<style>
	#news {
		height: 100%;
	}

</style>

<template>
	<view id="important">
		<picker mode="date" @change="change"><text class="choose">选择日期：{{date}}</text></picker>
		<uni-list class="list">
			<uni-list-item @click="toDetails(item.id)" :key="item.id" v-for="item in list" :title="item.title"></uni-list-item>
		</uni-list>
	</view>
</template>

<script>
	import uniList from '@/components/uni-list/uni-list.vue'
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	export default {
		components: {
			uniList,
			uniListItem
		},
		data() {
			const currentDate = this.getDate({
				format: true
			})
			return {
				date: currentDate,
				list: []
			}
		},
		created() {
			let result = this.getMAD(this.date)
			this.loadData(result[1], result[2])
		},
		methods: {
			change(e) {
				this.date = e.detail.value
				let result = this.getMAD(this.date)
				this.loadData(result[1], result[2])
			},
			getMAD(date) {
				return date.split('-')
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			loadData(month, day) {
				const that = this;
				uni.request({
					url: 'http://www.jiahengfei.cn:33550/port/history?dispose=easy&key=jiahengfei&month=' + month + '&day=' + day,
					success(res) {
						that.list = res.data.data
					},
					fail(err) {
						console.log(err)
					}
				})
			},
			toDetails(id){
				console.log(id)
				uni.navigateTo({
					url:'./details?id='+id
				})
			}
		}
	}
</script>

<style>
	#important {
		height: 100%;
		display: flex;
		flex-direction: column;
	}

	.choose {
		display: block;
		text-align: center;
		padding: 0.5em;
	}
	.list{
		flex:1;
		overflow: auto;
	}
</style>

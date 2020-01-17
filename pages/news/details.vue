<template>
	<view v-show="loadEnd" id="newsDetails">
		<text class="title">{{details.title}}</text>
		<text class="from">{{details.source}}</text>
		<rich-text class="content" :nodes="details.body"></rich-text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				details: '',
				loadEnd: false
			}
		},
		onLoad(options) {
			this.loadData(options.id)
		},
		methods: {
			loadData(id) {
				const that = this;
				uni.request({
					url: "https://api.cat-shop.penkuoer.com/api/v2/proxy",
					method: 'GET',
					timeout: 8000,
					data: {
						url: 'http://v1.alapi.cn/api/new/detail?docid=' + id,
						method: 'GET'
					},
					success(res) {
						that.details = res.data.data[id];
						that.loadEnd = true;
					},
					fail(err) {
						console.log(err)
					}
				})
				// uni.request({
				// 	url: 'http://v1.alapi.cn/api/new/detail?docid=' + id,
				// 	timeout: 8000,
				// 	success(res) {
				// 		that.details = res.data.data[id];
				// 		that.loadEnd = true;
				// 	},
				// 	fail(err) {
				// 		console.log(err)
				// 	}
				// })
			}
		}
	}
</script>

<style>
	#newsDetails {
		padding: 0 1em;
		height: 100%;
		overflow: auto;
	}

	text {
		display: block
	}

	.title {
		font-weight: bold;
		text-align: center;
		padding: 1em 1em 0.5em;
	}

	.from {
		text-align: right;
		padding-right: 0.5em;
		color: #666;
	}

	.content {
		display:block;
		padding: 0.5em 0 1em;
	}
</style>

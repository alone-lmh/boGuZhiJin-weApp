<template>
	<view v-show="loadEnd" id="shiDetails">
		<text class="title">{{details.title}}</text>
		<text class="author" @click="find(details.poet.name)">{{details.poet.name}}</text>
		<text class="content">{{details.poem}}</text>
		<view>
			<text class="other">声调：</text>
			<text class="content">{{details.strains}}</text>
		</view>
		<view>
			<text class="other">诗人生平：</text>
			<text class="content">{{details.poet.description}}</text>
		</view>
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
					method: 'POST',
					timeout: 8000,
					data: {
						url: "http://www.codestock.club/tang/api/poem/getPoemById/" + id
					},
					success(res) {
						that.details = res.data;
						that.loadEnd = true;
					},
					fail(err) {
						console.log(err)
					}
				})
				// uni.request({
				// 	url: "http://www.codestock.club/tang/api/poem/getPoemById/" + id,
				// 	timeout: 8000,
				// 	success(res) {
				// 		that.details = res.data;
				// 		that.loadEnd = true;
				// 	},
				// 	fail(err) {
				// 		console.log(err)
				// 	}
				// })
			},
			find(name) {
				uni.reLaunch({
					url: '/pages/shi/index?name=' + name
				});
			}
		}
	}
</script>

<style>
	#shiDetails {
		height: 100%;
		background-image: url("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1579259152652&di=4dcb99e48c3f07ec3a11187c333e386e&imgtype=0&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201505%2F01%2F20150501223032_XAtCi.jpeg");
		background-size: 100% 100%;
		padding:0 0.5em;
		overflow: auto;
	}

	text {
		display: block;
		padding: 0.5em 1em;
	}

	.title,
	.author {
		text-align: center;
	}

	.title {
		padding-bottom: 0;
	}

	.author {
		color: #666;
	}

	.content {
		text-indent: 2em;
	}

	.other {
		font-weight: bold;
		padding-bottom: 0;
		color: #666666;
	}
</style>

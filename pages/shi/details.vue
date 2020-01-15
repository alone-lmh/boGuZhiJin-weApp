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
					url: "http://www.codestock.club/tang/api/poem/getPoemById/" + id,
					timeout: 5000,
					success(res) {
						that.details = res.data;
						that.loadEnd = true;
					},
					fail(err) {
						console.log(err)
					}
				})
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
		background-image: url("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1579063894132&di=2e9a140e13e98600feda9702cade7eb4&imgtype=0&src=http%3A%2F%2Fpic79.nipic.com%2Ffile%2F20151005%2F21022608_164420192000_2.jpg");
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

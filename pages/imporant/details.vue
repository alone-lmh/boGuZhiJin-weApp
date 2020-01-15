<template>
	<view v-show="loadEnd" id="details">
		<text class="title">{{details.title}}</text>
		<text class="date">{{details.lunar}}</text>
		<cover-image :src="details.pic" alt="http://img.ibabyzone.cn/album/16/38/84/4263200421252874.gif"></cover-image>
		<text class="content">{{details.des}}</text>
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
					url: 'http://www.jiahengfei.cn:33550/port/history?dispose=select&key=jiahengfei&id=' + id,
					success(res) {
						that.details = res.data.data;
						that.loadEnd = true;
					},
					fail(err) {
						console.log(err)
					}
				})
			}
		}
	}
</script>

<style>
	#details {
		height: 100%;
		background-image: url("http://img3.imgtn.bdimg.com/it/u=1755359665,1374319544&fm=26&gp=0.jpg");
		background-size: 100% 100%;
		overflow: auto;
	}

	text {
		display: block;
		padding: 0.5em;
	}

	.title {
		text-align: center;
		padding-top: 1.5em;
	}

	.date {
		text-align: right;
		padding-top: 0;
		color: #666;
	}

	.content {
		text-indent: 2em;
		padding-bottom: 1.5em;
	}
</style>

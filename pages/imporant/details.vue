<template>
	<view v-show="loadEnd" id="importantDetails">
		<text class="title">{{details.title}}</text>
		<text class="date">{{details.lunar}}</text>
		<cover-image :src="details.pic" ></cover-image>
		<rich-text class="content">{{details.des}}</rich-text>
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
					timeout: 8000,
					success(res) {
						that.details = res.data.data;
						if(!that.details.pic){
							that.details.pic='https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=606394262,1008040175&fm=26&gp=0.jpg'
						}
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
	#importantDetails {
		height: 100%;
		background-image: url("http://img3.imgtn.bdimg.com/it/u=1755359665,1374319544&fm=26&gp=0.jpg");
		background-size: 100% 100%;
		overflow: auto;
		padding:0 0.5em;
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
		display:block;
		text-indent: 2em;
		padding: 0.5em 0 1.5em;
	}
</style>

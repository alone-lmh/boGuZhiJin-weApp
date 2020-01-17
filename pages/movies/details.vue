<template>
	<view class="movieDetails">
		<view v-if="notSoon">
			<text class="title">{{details.title}}</text>
			<cover-image :src="details.images.small"></cover-image>
			<text class="tip">标签：</text>
			<robby-tags :value="details.tags"></robby-tags>
			<text class="tip">剧情简介：</text>
			<text class="content">{{details.summary}}</text>
			<view>
				<text class="tip">主演：</text>
				<list class="flex">
					<cell v-for="item in details.casts" :key="item.id">
						<cover-image :src="item.avatars.small"></cover-image>
						<text class="fname">{{item.name}}</text>
					</cell>
				</list>
				<text class="tip">编剧：</text>
				<list class="flex">
					<cell v-for="item in details.writers" :key="item.id">
						<cover-image :src="item.avatars.small"></cover-image>
						<text class="fname">{{item.name}}</text>
					</cell>
				</list>
				<text class="tip">导演：</text>
				<list class="flex">
					<cell v-for="item in details.directors" :key="item.id">
						<cover-image :src="item.avatars.small"></cover-image>
						<text class="fname">{{item.name}}</text>
					</cell>
				</list>
			</view>
			<view>
				<text class="tip">影评：</text>
				<list>
					<cell class="pannel" v-for="item in details.popular_reviews" :key="item.id">
						<cover-image :src="item.author.avatar"></cover-image>
						<text class="pname">{{item.author.name}}</text>
						<text class="ptitle">{{item.title}}</text>
						<text class="pcontent">{{item.summary}}</text>
					</cell>
				</list>
			</view>
		</view>
		<view v-if="isSoon">
			<text class="title">{{details.title}}</text>
			<cover-image :src="details.images.small"></cover-image>
			<text class="tip">标签：</text>
			<robby-tags :value="details.tags"></robby-tags>
			<text class="tip">剧情简介：</text>
			<text class="content">{{details.summary}}</text>
			<view>
				<text class="tip">主演：</text>
				<robby-tags :value="casts"></robby-tags>
				<text class="tip">编剧：</text>
				<robby-tags :value="writers"></robby-tags>
				<text class="tip">导演：</text>
				<robby-tags :value="directors"></robby-tags>
			</view>
		</view>
	</view>
</template>

<script>
	import uniTag from "@/components/uni-tag/uni-tag.vue"
	import robbyTags from '@/components/robby-tags/robby-tags.vue'
	export default {
		components: {
			uniTag,
			robbyTags
		},
		data() {
			return {
				details: '',
				comment: '',
				isSoon: false,
				notSoon: false,
				casts: [],
				writers: [],
				directors: []
			}
		},
		onLoad(options) {
			this.loadData(options.id);
		},
		methods: {
			loadData(id) {
				const that = this;
				uni.request({
					url: 'https://douban.uieee.com/v2/movie/subject/' + id,
					timeout: 8000,
					header: {
						"Content-Type": "json"
					},
					success(res) {
						that.details = res.data;
						if (res.data.rating.average) {
							that.notSoon = true;
						} else {
							that.isSoon = true;
							for(let i=0;i<res.data.casts.length;i++){
								that.casts.push(res.data.casts[i].name)
							};
							for(let i=0;i<res.data.writers.length;i++){
								that.writers.push(res.data.writers[i].name)
							};
							for(let i=0;i<res.data.directors.length;i++){
								that.directors.push(res.data.directors[i].name)
							};
						}
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
	.movieDetails {
		padding: 0.5em 0;
	}

	text {
		display: block
	}

	.flex {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}

	.flex cell {
		width: 30%;
	}

	cover-image {
		height: auto;
	}

	.fname {
		width: 100%;
		overflow: hidden;
		text-overflow: ellipsis;
		text-align: center;
		white-space: nowrap;
	}

	.title {
		text-align: center;
		font-weight: bold;
		padding: 0.5em;
	}

	.tip {
		color: #333;
		font-weight: bold;
		padding: 0.5em 0.5em 0.2em;
	}

	.name {
		text-align: center;
		width: 100%;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.content {
		text-indent: 2em;
		padding: 0 0.6em;
	}

	.pannel {
		display: flex;
		flex-wrap: wrap;
		margin-bottom: 0.6em;
		padding: 0.5em;
		background: #fafafa;
	}

	.pannel cover-image {
		width: 2em;
		border-radius: 1em;
		margin-left: 2em;
	}

	.pname {
		line-height: 2em;
		margin-left: 1em;
	}

	.ptitle {
		text-align: center;
		color: #333;
		font-weight: bold;
		width: 100%;
		padding-top:0.5em;
	}

	.pcontent {
		padding:0.5em 0;
		width: 100%;
		text-indent: 2em;
		overflow: hidden;
	}
</style>

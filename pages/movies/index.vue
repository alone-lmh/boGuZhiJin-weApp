<template>
	<view id="movies">
		<uni-segmented-control class="top" :current="current" :values="items" @clickItem="onClickItem" style-type="text"
		 active-color="#4cd964"></uni-segmented-control>
		<view class="content">
			<view v-show="current === 0">
				<view @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
					<sib-list ref="sibList" :floterText="over" @isRefresh='isRefresh' @scrolltolowerFn="scrolltolowerFn">
						<block slot="sibScrollList">
							<view @click="toDetails(item.id)" class="cell" v-for="item in showHot" :key="item.id">
								<cover-image :src="item.images.small"></cover-image>
								<view class="content">
									<text class="other">{{item.title}}</text>
									<text class="other">评分：{{item.rating.average}}</text>
									<text class="other">类型：{{item.genres[0]}}</text>
									<text class="other">时长：{{item.durations[0]}}</text>
								</view>
							</view>
						</block>
					</sib-list>
				</view>
			</view>
			<view v-show="current === 1">
				<view @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
					<sib-list :floterText="over" ref="sibList" @isRefresh='isRefresh' @scrolltolowerFn="scrolltolowerFn">
						<block slot="sibScrollList">
							<view @click="toDetails(item.id)" class="cell" v-for="item in showSoon" :key="item.id">
								<cover-image :src="item.images.small"></cover-image>
								<view class="content">
									<text class="other">{{item.title}}</text>
									<text class="other">类型：{{item.genres[0]}}</text>
									<text class="other">导演：{{item.directors[0].name}}</text>
									<text class="other">上映时间：{{item.mainland_pubdate}}</text>
								</view>
							</view>
						</block>
					</sib-list>
				</view>
			</view>
			<view v-show="current === 2">
				<view @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
					<sib-list :floterText="over" ref="sibList" @isRefresh='isRefresh' @scrolltolowerFn="scrolltolowerFn">
						<block slot="sibScrollList">
							<view @click="toDetails(item.id)" class="cell" v-for="item in top250" :key="item.id">
								<cover-image :src="item.images.small"></cover-image>
								<view class="content">
									<text class="other">{{item.title}}</text>
									<text class="other">评分：{{item.rating.average}}</text>
									<text class="other">类型：{{item.genres[0]}}</text>
									<text class="other">时长：{{item.durations[0]}}</text>
								</view>
							</view>
						</block>
					</sib-list>
				</view>
			</view>
			<view v-show="current === 3">
				<view @click="toDetails(item.id)" class="cell" v-for="item in best" :key="item.id">
					<cover-image :src="item.subject.images.small"></cover-image>
					<view class="content">
						<text class="other">{{item.subject.title}}</text>
						<text class="other">评分：{{item.subject.rating.average}}</text>
						<text class="other">类型：{{item.subject.genres[0]}}</text>
						<text class="other">时长：{{item.subject.durations[0]}}</text>
					</view>
				</view>
			</view>
			<view v-show="current === 4">
				<view @click="toDetails(item.id)" class="cell" v-for="item in much" :key="item.id">
					<cover-image :src="item.subject.images.small"></cover-image>
					<view class="content">
						<text class="other">{{item.subject.title}}</text>
						<text class="other">评分：{{item.subject.rating.average}}</text>
						<text class="other">类型：{{item.subject.genres[0]}}</text>
						<text class="other">时长：{{item.subject.durations[0]}}</text>
					</view>
				</view>
			</view>
			<view v-show="current === 5">
				<view @click="toDetails(item.id)" class="cell" v-for="item in newMovies" :key="item.id">
					<cover-image :src="item.images.small"></cover-image>
					<view class="content">
						<text class="other">{{item.title}}</text>
						<text class="other">评分：{{item.rating.average}}</text>
						<text class="other">类型：{{item.genres[0]}}</text>
						<text class="other">时长：{{item.durations[0]}}</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniSegmentedControl from "@/components/uni-segmented-control/uni-segmented-control.vue";
	import sibList from '@/components/sib-list/sib-list.vue'
	export default {
		components: {
			uniSegmentedControl,
			sibList
		},
		data() {
			return {
				items: ['正在热映', '即将上映', 'Top250', '口碑榜', '票房榜', '新片榜'],
				current: 0,
				showHot: [],
				hotTotal: 0,
				hotPage: 0,
				showSoon: [],
				soonTotal: 0,
				soonPage: 0,
				top250: [],
				topTotal: 0,
				topPage: 0,
				best: [],
				much: [],
				newMovies: [],
				over:'没有更多了'
			}
		},
		created() {
			this.getShowHot(0);
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
					switch (e.currentIndex) {
						case 0:
							this.showHot.length == 0 ? this.getShowHot(0) : '';
							break;
						case 1:
							this.showSoon.length == 0 ? this.getShowSoon(0) : '';
							break;
						case 2:
							this.top250.length == 0 ? this.getTop250(0) : '';
							break;
						case 3:
							this.best.length == 0 ? this.getBest() : '';
							break;
						case 4:
							this.much.length == 0 ? this.getMuch() : '';
							break;
						case 5:
							this.newMovies.length == 0 ? this.getNewMovies() : '';
							break;
					}
				}
			},
			getShowHot(page) {
				const that = this;
				//获取正在热映的电影
				uni.request({
					url: "https://douban.uieee.com/v2/movie/in_theaters?start="+page+"&count=10",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.showHot = [...that.showHot,...res.data.subjects];
						that.hotTotal = res.data.total;
					},
					fail(err) {
						console.log(err)
					}
				});
			},
			getShowSoon(page) {
				const that = this;
				//获取即将上映的电影
				uni.request({
					url: "https://douban.uieee.com/v2/movie/coming_soon?start="+page+"&count=10",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.showSoon = [...that.showSoon,...res.data.subjects];
						that.soonTotal = res.data.total;
					},
					fail(err) {
						console.log(err)
					}
				});
			},
			getTop250(page) {
				const that = this;
				//获取Top250
				uni.request({
					url: "https://douban.uieee.com/v2/movie/top250?start="+page+"&count=10",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.top250 = [...that.top250,...res.data.subjects];
						that.topTotal = res.data.total;
					},
					fail(err) {
						console.log(err)
					}
				});
			},
			getBest() {
				const that = this;
				//获取本周口碑榜
				uni.request({
					url: "https://douban.uieee.com/v2/movie/weekly?apikey=0df993c66c0c636e29ecbb5344252a4a",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.best = res.data.subjects;
					},
					fail(err) {
						console.log(err)
					}
				});
			},
			getMuch() {
				const that = this;
				//获取北美票房榜
				uni.request({
					url: "https://douban.uieee.com/v2/movie/us_box?apikey=0df993c66c0c636e29ecbb5344252a4a",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.much = res.data.subjects;
					},
					fail(err) {
						console.log(err)
					}
				});
			},
			getNewMovies() {
				const that = this;
				//获取新片榜
				uni.request({
					url: "https://douban.uieee.com/v2/movie/new_movies?apikey=0df993c66c0c636e29ecbb5344252a4a",
					timeout: 8000,
					header:{
					    "Content-Type":"json"
					},
					success(res) {
						that.newMovies = res.data.subjects;
					},
					fail(err) {
						console.log(err)
					}
				});
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
					url: '/pages/movies/index'
				});
			},
			scrolltolowerFn: function() {
				switch (this.current) {
					case 0:
						this.hotPage += 1;
						(this.hotPage) * 10 <= this.hotTotal ? this.getShowHot(this.hotPage) : '';
						break;
					case 1:
						this.soonPage += 1;
						(this.soonPage) * 10 <= this.soonTotal ? this.getShowSoon(this.soonPage) : '';
						break;
					case 2:
						this.topPage += 1;
						(this.topPage) * 10 <= this.topTotal ? this.getTop250(this.topPage) : '';
						break;
				}
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
	#movies {
		height: 100%;
		display: flex;
		flex-direction: column;
	}

	.top text{
		font-size: 24rpx!important;
	}

	.content {
		flex: 1;
		overflow: auto;
	}

	text {
		display: block
	}

	.cell {
		display: flex;
		padding: 0.3em 1em;
		border-bottom: 1px #eee solid;
	}

	cover-image {
		width: 25%;
	}

	.cell .content {
		padding-left: 1em;
	}

	.content text {
		padding-bottom: 0.5em;
	}

	.other {
		overflow: hidden;
		width: 100%;
		white-space: nowrap;
		text-overflow: ellipsis;
	}
</style>

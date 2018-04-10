<template>
	<div class="ratings" ref="ratingsWrapper">
		<div class="ratings_content">
			<div class="rating_score">
				<div class="score_left">
					<h2 class="score">{{seller.score}}</h2>
					<p class="text">综合评分</p>
					<p class="height_other clearfix">高于周边商家{{seller.rankRate}}%</p>
				</div>
				<div class="score_right">
					<div class="score_content">
						<div class="service_score">
							<span class="text">服务态度</span>
							<star :size="36" :score="seller.serviceScore"></star>
							<span class="score">{{seller.serviceScore}}</span>
						</div>
						<div class="service_score">
							<span class="text">商品评分</span>
							<star :size="36" :score="seller.foodScore"></star>
							<span class="score">{{seller.foodScore}}</span>
						</div>
						<div class="service_score">
							<span class="text">送达时间</span>
							<span class="time">{{seller.deliveryTime}}分钟</span>
						</div>
					</div>

				</div>
			</div>
			<split></split>
			<rating-select :ratings="ratings" @ratingtype="typeChange" @onlycontent="onlyChange" :type="selectType" :only="onlyContent"></rating-select>
			<ul class="rating_list" v-show="ratings&&ratings.length">
				<li v-for="item in ratings" class="rating border-1px" v-if="needShow(item.rateType,item.text)">
					<div class="img_box">
						<img :src="item.avatar" alt="" width="100%" height="100%" />
					</div>
					<div class="content">
						<div class="top">
							<p class="name">{{item.username}}</p>
							<span class="time">{{item.rateTime | formatDate}}</span>
						</div>

						<div class="score_time">
							<star :size="24" :score="item.score"></star>
							<span class="arrive_time" v-show="item.deliveryTime">{{item.deliveryTime}}分钟送达</span>
						</div>
						<p class="text" v-show="item.text">{{item.text}}</p>
						<div class="foods">
							<i :class="praise[item.rateType]" class="icon"></i>
							<ul class="recommend_list" v-show="item.recommend&&item.recommend.length">
								<li v-for="recommend in item.recommend" class="recommend">{{recommend}}</li>
							</ul>
						</div>

					</div>

				</li>
			</ul>
			<div class="no_rating" v-show="!ratings||!ratings.length">暂无评价</div>
		</div>
	</div>
</template>

<script>
	import BScroll from "better-scroll";
	import { formatDate } from 'common/js/common.js';
	import star from "components/star/star";
	import split from "components/split/split";
	import ratingSelect from "components/ratingSelect/ratingSelect";
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				ratings: [],
				selectType: ALL,
				onlyContent: false,
				praise: ["icon-thumb_up", "icon-thumb_down"]
			}
		},
		created() {
			this.$http.get('/api/ratings', '', {
				emulateJSON: true
			}).then((res) => {
				if(res.body.errno == 0) {
					this.ratings = res.body.data;
					this.$nextTick(() => {
						this.initScroll();
					})

				}
			})
		},
		methods: {
			initScroll() {
				this.scroll = new BScroll(this.$refs.ratingsWrapper, {
					click: true
				});
			},
			typeChange(type) {
				this.selectType = type;
				this.$nextTick(() => {
					this.scroll.refresh();
				});

			},
			onlyChange(onlyContent) {
				this.onlyContent = onlyContent;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			},
			needShow(type, text) {
				if(this.onlyContent && !text) {
					return false;
				}
				if(this.selectType === ALL) {
					return true;
				} else {
					return this.selectType === type;
				}

			}
		},
		filters: {
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date, 'yyyy-MM-dd hh:mm:ss');
			}
		},
		components: {
			star,
			split,
			ratingSelect
		}
	};
</script>

<style scoped="" lang="scss">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.ratings {
		position: fixed;
		width: 100%;
		top: 8.8rem;
		bottom: 0;
		left: 0;
		overflow: hidden;
		.ratings_content {
			width: 100%;
			.rating_score {
				display: flex;
				padding: 0.9rem 0;
				.score_left {
					flex: 0 0 35%;
					width: 35%;
					text-align: center;
					border-right: 1px solid rgba(7, 17, 27, 0.1);
					.score {
						line-height: 1.4rem;
						font-size: 1.2rem;
						color: rgb(255, 153, 0);
						margin-bottom: 0.3rem;
					}
					.text {
						margin-bottom: 0.4rem;
						font-size: 0.6rem;
						color: rgb(7, 17, 27);
					}
					.height_other {
						vertical-align: top;
						font-size: 0.5rem;
						color: #999;
					}
				}
				.score_right {
					flex: 1;
					display: flex;
					justify-content: center;
					line-height: 0.9rem;
					.service_score {
						display: flex;
						margin-bottom: 0.4rem;
						font-size: 0.6rem;
						&:last-of-type {
							margin-bottom: 0;
						}
						.text {
							margin-right: 0.6rem;
							color: rgb(7, 17, 27);
						}
						.score {
							margin-left: 0.6rem;
							color: rgb(255, 153, 0);
						}
						.time {
							color: rgb(147, 153, 159);
						}
					}
				}
			}
			.ratingSelect {
				padding: 0 0.9rem;
			}
			.rating_list {
				padding: 0 0.9rem;
				.rating {
					display: flex;
					padding: 0.9rem 0.9rem 0.5rem 0;
					@include border-1px(rgba(7, 17, 27, 0.1));
					&:last-of-type {
						&:after {
							border: 0;
						}
					}
					.img_box {
						flex: 0 0 1.4rem;
						display: block;
						width: 1.4rem;
						height: 1.4rem;
						margin-right: 0.6rem;
						img {
							border-radius: 50%;
						}
					}
					.content {
						flex: 1;
						.top {
							display: flex;
							justify-content: space-between;
							width: 100%;
							font-size: 0.5rem;
							line-height: 0.6rem;
							.name {
								color: rgb(7, 17, 27);
							}
							.time {
								color: rgb(147, 153, 159);
							}
						}
						.score_time {
							display: flex;
							width: 100%;
							margin: 0.2rem 0.3rem 0.3rem 0;
							line-height: 0.6rem;
							.arrive_time {
								margin-left: 0.3rem;
								font-size: 0.5rem;
								font-weight: 200;
								color: rgb(147, 153, 159);
							}
						}
						.text {
							margin-bottom: 0.4rem;
							font-size: 0.6rem;
							line-height: 0.9rem;
							color: rgb(7, 17, 27);
						}
						.foods {
							display: flex;
							.icon {
								font-size: 0.6rem;
								line-height: 0.8rem;
								&.icon-thumb_up {
									color: rgb(0, 160, 220);
								}
								&.icon-thumb_down {
									color: rgb(183, 187, 191);
								}
							}
							.recommend_list {
								display: flex;
								flex-wrap: wrap;
								.recommend {
									max-width: 4rem;
									padding: 0 0.3rem;
									margin: 0 0 0.4rem 0.4rem;
									font-size: 0.45rem;
									line-height: 0.8rem;
									color: rgb(147, 153, 159);
									border: 1px solid rgba(7, 17, 27, 0.1);
									border-radius: 2px;
									overflow: hidden;
									text-overflow: ellipsis;
									white-space: nowrap;
								}
							}
						}
					}
				}
			}
		}
	}
</style>
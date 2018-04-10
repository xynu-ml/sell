<template>
	<transition name="move">
		<div class="food" v-show="showFlag" ref="foodWrapper">
			<div>
				<div class="food_head border-1px">
					<img :src="food.image" alt="" class="food_img" />
					<div class="back icon-arrow_lift" @click="hide"></div>
					<div class="head_content">
						<h3 class="name">{{food.name}}</h3>
						<div class="extra">
							<span class="month_num">月售{{food.sellCount}}份</span><span class="ratings">好评率{{food.rating}}%</span>
						</div>
						<div class="price_box">
							<span class="now">￥<span>{{food.price}}</span></span><span class="old" v-show="food.oldPrice">￥<span>{{food.oldPrice}}</span></span>
						</div>
						<car-ctrl :food="food" v-show="food.count>0"></car-ctrl>
						<transition name="fade" mode="out-in">
							<div class="add_car" v-show="!food.count||food.count==0" @click="add($event)">加入购物车</div>
						</transition>

					</div>

				</div>
				<div class="food_instruct" v-show="food.info">
					<h3 class="title">商品介绍</h3>
					<p class="description">{{food.info}}</p>
				</div>
				<div class="food_ratings">
					<h3 class="title">商品评价</h3>
					<rating-select @ratingtype="typeChange" @onlycontent="onlyChange" :ratings="food.ratings" :type="selectType" :desc="desc" :only="onlyContent"></rating-select>
					<ul class="rating_list" v-show="food.ratings&&food.ratings.length">
						<li v-for="item in food.ratings" class="rating border-1px" v-show="needShow(item.rateType,item.text)">
							<div class="top">
								<span class="time">{{item.rateTime | formatDate}}</span>
								<div class="user_info">
									<span class="user">{{item.username}}</span>
									<img :src="item.avatar" alt="" class="user_img" />
								</div>

							</div>
							<div class="content" v-show="item.text">
								<i class="icon" :class="praise[item.rateType]"></i><span class="text">{{item.text}}</span>
							</div>

						</li>
					</ul>
					<div class="no_rating" v-show="!food.ratings||!food.ratings.length">暂无评价</div>
				</div>
			</div>

		</div>
	</transition>
</template>

<script>
	import Vue from "vue";
	import { formatDate } from 'common/js/common.js';
	import BScroll from "better-scroll";
	import carCtrl from "components/carCtrl/carCtrl";
	import ratingSelect from "components/ratingSelect/ratingSelect";
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		props: {
			food: {
				type: Object
			}
		},
		data() {
			return {
				showFlag: false,
				selectType: ALL,
				desc: {
					all: "全部",
					positive: "推荐",
					negative: "吐槽"
				},
				onlyContent: false,
				praise: ["icon-thumb_up", "icon-thumb_down"]
			}

		},
		methods: {
			show() {
				this.showFlag = true;
				this.$nextTick(() => {
					if(!this.scroll) {
						this.scroll = new BScroll(this.$refs.foodWrapper, {
							click: true
						});
					} else {
						this.scroll.refresh();
					}

				})
			},
			hide() {
				this.showFlag = false;
			},
			add(event) {
				if(!event._constructed) {
					return;
				}
				Vue.set(this.food, "count", 1);
				this.$emit('addCart', event.target);
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
			carCtrl,
			ratingSelect
		}
	};
</script>

<style scoped="" lang="scss">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.food {
		position: fixed;
		top: 0;
		left: 0;
		bottom: 2.3rem;
		width: 100%;
		background: #f3f5f7;
		overflow: hidden;
		&.move-enter-active,
		&.move-leave-active {
			transition: all 0.2s linear;
			transform: translate3d(0, 0, 0);
		}
		&.move-enter,
		&.move-leave-active {
			transform: translate3d(100%, 0, 0);
		}
		.food_head {
			margin-bottom: 0.8rem;
			background: #fff;
			border-bottom: 1px solid rgba(7, 17, 27, 0.1);
			.food_img {
				width: 100%;
				max-height: 18.25rem;
			}
			.back {
				position: fixed;
				top: 0.4rem;
				left: 0.4rem;
				padding: 0.3rem;
				color: #fff;
				vertical-align: top;
				border-radius: 50%;
				background: rgba(0, 0, 0, 0.5);
			}
			.head_content {
				position: relative;
				padding: 0.9rem;
				.name {
					margin-bottom: 0.4rem;
					font-size: 0.7rem;
					line-height: 0.7rem;
					font-weight: 700;
					color: rgb(7, 17, 27);
				}
				.extra {
					margin-bottom: 0.9rem;
					font-size: 0.5rem;
					color: rgb(147, 153, 159);
					.rating {
						margin-left: 0.6rem;
					}
				}
				.price_box {
					font-size: 0.5rem;
					line-height: 1.2rem;
					.now {
						color: rgb(240, 20, 20);
						span {
							font-size: 0.7rem;
							font-weight: 700;
						}
					}
					.old {
						color: rgb(147, 153, 159);
						margin-left: 0.5rem;
						text-decoration: line-through;
						span {
							font-weight: 700;
						}
					}
				}
				.add_car,
				.car_ctrl {
					display: inline-block;
					position: absolute;
					right: 0.9rem;
					color: #fff;
				}
				.add_car {
					bottom: 1rem;
					width: 3.7rem;
					height: 1.2rem;
					background: rgb(0, 160, 220);
					line-height: 1.2rem;
					border-radius: 0.6rem;
					font-size: 0.5rem;
					text-align: center;
					transition: all 0.2s;
					&.fade-enter,
					&.fade-leave-active {
						opacity: 1;
					}
					&.fade-enter,
					&.fade-leave-active {
						opacity: 0;
					}
				}
				.car_ctrl {
					bottom: 0.5rem;
				}
			}
		}
		.food_instruct {
			padding: 0.9rem;
			background: #fff;
			border-top: 1px solid rgba(7, 17, 27, 0.1);
			border-bottom: 1px solid rgba(7, 17, 27, 0.1);
			.title {
				font-size: 0.7rem;
			}
			.description {
				margin: 0.3rem 0.4rem 0 0.4rem;
				font-size: 0.6rem;
				font-weight: 200;
				color: rgb(147, 153, 159);
				line-height: 1.2rem;
			}
		}
		.food_ratings {
			padding: 0.9rem;
			margin-top: 0.8rem;
			background: #fff;
			border-top: 1px solid rgba(7, 17, 27, 0.1);
			.title {
				font-size: 0.7rem;
			}
			.rating_list {
				.rating {
					padding: 0.8rem 0;
					@include border-1px(rgba(7, 17, 27, 0.1));
					&:last-of-type {
						&:after {
							border: 0;
						}
					}
					.top {
						display: flex;
						justify-content: space-between;
						width: 100%;
						color: rgb(147, 153, 159);
						font-size: 0.5rem;
						line-height: 0.6rem;
						margin-bottom: 0.3rem;
						.user_info {
							.user_img {
								width: 0.6rem;
								height: 0.6rem;
								margin-left: 0.3rem;
								border-radius: 50%;
							}
						}
					}
					.content {
						font-size: 0.6rem;
						color: rgb(7, 17, 27);
						line-height: 0.8rem;
						.icon {
							display: inline-block;
							font-size: 0.6rem;
							line-height: 1.2rem;
							margin-right: 0.2rem;
							&.icon-thumb_up {
								color: rgb(0, 160, 220);
							}
							&.icon-thumb_down {
								color: rgb(147, 153, 159);
							}
						}
					}
				}
			}
			.no_rating {
				line-height: 2rem;
				font-size: 0.6rem;
				text-align: center;
			}
		}
	}
</style>
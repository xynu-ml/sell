<template>
	<div>
         <div class="goods">
			<div class="goods_left" ref="menuWrapper">
				<ul>
					<li class="item" v-for="(good,index) in goods" :class="{'current':currIndex===index}" @click="currActive(index,$event)">
						<span class="text border-1px">
						<span class="icon" v-if="good.type>0" :class="classMap[good.type]"></span>{{good.name}}
						</span>
					</li>
				</ul>
			</div>
			<div class="goods_right" ref="foodsWrapper">
				<ul>
					<li v-for="item in goods" class="item item-hook">
						<h3>{{item.name}}</h3>
						<ul>
							<li class="food border-1px" v-for="food in item.foods" @click="foodSelect(food,$event)">
								<div class="img_box" v-if="food.icon">
									<img :src="food.icon" alt="" width="100%" />
								</div>
								<div class="content">
									<div class="title">{{food.name}}</div>
									<p class="description" v-if="food.description">{{food.description}}</p>
									<div class="extra">
										<span>月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
									</div>
									<div class="bottom">
										<span class="price">￥<span>{{food.price}}</span></span><span class="old_price" v-if="food.oldPrice">￥<span>{{food.oldPrice}}</span></span>
										<div class="count">
											<span class="minus"></span>
											<span></span>
											<span class="add"></span>
										</div>
										<div class="car_ctrl_box">
											<car-ctrl :food="food" @addCart="_drop"></car-ctrl>
										</div>
									</div>
								</div>
							</li>
						</ul>
					</li>
				</ul>
			</div>
			<car :select-foods="select" ref="car" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></car>

		</div>
	    <food :food="selectFood" ref="food" @addCart="_drop"></food>
	</div>
</template>

<script>
	import BScroll from "better-scroll";
	import car from "components/car/car";
	import carCtrl from "components/carCtrl/carCtrl";
	import food from "components/food/food";
	export default {
		props: {
			seller: {
				type: Object
			}
		},
	    data() {
			return {
				goods: [],
				classMap: ["decrease", "discount", "spcial", "invioce", "guarantee"],
				foodListsHeight: [],
				scrollY: 0,
				selectFood: {}
			}
		},
		created() {
			this.$http.get('/api/goods', '', {
				emulateJSON: true
			}).then((res) => {
				if(res.body.errno == 0) {
					this.goods = res.body.data;
					this.$nextTick(() => {
						this._initScroll();
						this.calculateHeight();
					})
				}
			})

		},
		computed: {
			currIndex() {
				for(let i = 0; i < this.foodListsHeight.length; i++) {

					let height1 = this.foodListsHeight[i];
					let height2 = this.foodListsHeight[i + 1];
					if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
						return i;
					}
				}
				return 0;
			},
			select() {
				let foods = [];
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if(food.count) {
							foods.push(food);
						}

					})
				})
				return foods;
			}
		},

		methods: {
			_initScroll() {
				this.meunScroll = new BScroll(this.$refs.menuWrapper, {
					click: true
				});
				this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
					click: true,
					probeType: 3
				});
				this.foodsScroll.on("scroll", (pos) => {
					this.scrollY = Math.abs(Math.round(pos.y));
				})
			},
			calculateHeight() {
				let height = 0;
				this.foodListsHeight.push(height);
				let foodLists = this.$refs.foodsWrapper.getElementsByClassName("item-hook");
				for(let i = 0; i < foodLists.length; i++) {
					height += foodLists[i].clientHeight;
					this.foodListsHeight.push(height);
				}
			},
			currActive(index, event) {
				if(!event._constructed) {
					return;
				}
				let foodLists = this.$refs.foodsWrapper.getElementsByClassName("item-hook");
				this.foodsScroll.scrollToElement(foodLists[index], 300);

			},
			_drop(target) {
				this.$nextTick(() => {
					this.$refs.car.drop(target);
				})

			},
			foodSelect(food,event){
				if(!event._constructed) {
					return;
				}
				this.selectFood=food;
				this.$refs.food.show();
				
			}

		},
		components: {
			car,
			carCtrl,
			food
		}
	};
</script>

<style lang="scss" scoped>
	@import '../../common/sass/index.scss';
	.goods {
		display: flex;
		position: absolute;
		top: 8.8rem;
		bottom: 2.3rem;
		overflow: hidden;
		.goods_left {
			flex: 0 0 4rem;
			width: 4rem;
			background: #f3f5f7;
			overflow: hidden;
			ul {
				.item {
					display: table;
					padding: 0 0.6rem;
					height: 2.7rem;
					line-height: 0.7rem;
					margin: auto;
					&:last-child {
						.text {
							&:after {
								border: none;
							}
						}
					}
					&.current {
						position: relative;
						background: #fff;
						margin-top: -0.05rem;
						.text {
							font-weight: 600;
							&:after {
								border: 0;
							}
						}
					}
					.text {
						display: table-cell;
						vertical-align: middle;
						width: 2.8rem;
						font-size: 0.6rem;
						color: rgb(240, 20, 20);
						font-weight: 200;
						@include border-1px(rgba(7, 17, 27, 0.1));
						.icon {
							display: inline-block;
							vertical-align: top;
							width: 0.6rem;
							height: 0.6rem;
							margin-right: 0.1rem;
							background-repeat: no-repeat;
							background-size: 0.6rem;
							&.decrease {
								@include bg-image("decrease_3");
							}
							&.discount {
								@include bg-image("discount_3");
							}
							&.decrease {
								@include bg-image("decrease_3");
							}
							&.spcial {
								@include bg-image("special_3");
							}
							&.invioce {
								@include bg-image("invoice_3");
							}
							&.guarantee {
								@include bg-image("guarantee_3");
							}
						}
					}
				}
			}
		}
		.goods_right {
			flex: 1;
			background: #f3f5f7;
			overflow: hidden;
			&>ul {
				background: #fff;
				.item {
					&>h3 {
						border-left: 3px solid #d9dde1;
						padding-left: 0.7rem;
						font-size: 0.6rem;
						color: rgb(147, 153, 159);
						background: #f3f5f7;
						line-height: 1.3rem;
					}
					.food {
						display: flex;
						position: relative;
						padding: 0.9rem 0;
						margin: 0 0.9rem;
						@include border-1px(rgba(7, 17, 27, 0.1));
						&:last-child {
							&:after {
								border: 0;
							}
						}
						.img_box {
							flex: 0 0 2.85rem;
							width: 2.85rem;
							height: 2.85rem;
						}
						.content {
							flex: 1;
							margin-left: 0.5rem;
							.title {
								margin: 0.1rem 0 0.4rem;
								font-size: 0.7rem;
								color: rgb(7, 17, 27);
								line-height: 0.7rem;
							}
							.description {
								font-size: 0.5rem;
								color: rgb(147, 153, 159);
								line-height: 0.6rem;
							}
							.extra {
								margin-top: 0.4rem;
								font-size: 0.5rem;
								color: rgb(147, 153, 159);
								span {
									margin-right: 0.6rem;
									&:last-child {
										margin-right: 0;
									}
								}
							}
							.bottom {
								font-size: 0.5rem;
								font-weight: normal;
								line-height: 1.2rem;
								.price {
									margin-right: 0.4rem;
									color: rgb(240, 20, 20);
									span {
										font-size: 0.7rem;
										font-weight: 700;
									}
								}
								.old_price {
									font-size: 0.5rem;
									color: rgb(147, 153, 159);
									text-decoration: line-through;
									span {
										font-weight: 700;
									}
								}
								.car_ctrl_box {
									position: absolute;
									bottom: 0.4rem;
									right: -0.3rem;
								}
							}
						}
					}
				}
			}
		}
	}
</style>
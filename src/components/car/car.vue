<template>
	<div class="shop_car">
		<div class="content">
			<div class="content_left" @click="onOff">
				<div class="car_box" :class="{heightLight:totalCount>0}">
					<i class="icon-shopping_cart" :class="{heightLight:totalCount>0}"></i>
					<span class="count" v-if="totalCount>0">{{totalCount}}</span>
				</div>
				<div class="price" :class="{heightLight:totalPrice>0}">￥{{totalPrice}}</div><span class="send_pay">另需配送费￥<span>{{deliveryPrice}}</span>元</span>
			</div>
			<div class="content_right" :class="{heightLight:totalPrice>=minPrice}" @click.stop.prevent="pay">{{disPrice}}</div>
		</div>
		<div class="ball_box">
			<div v-for="ball in balls">
				<transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
					<div class="ball" v-show="ball.show">
						<div class="inner inner-hook"></div>
					</div>
				</transition>
			</div>
		</div>
		<transition name="fade">
			<div class="mask" v-show="listShow" @click="hideList"></div>
		</transition>
		
		<transition name="fold">
			<div class="food_list" v-show="listShow">
				<div class="list_head border-1px">
					<h3 class="title">购物车</h3>
					<span class="clean_car" @click="cleanCar">清空</span>
				</div>
				<div class="list_content" ref="listContent">
					<ul>
						<li v-for="food in selectFoods" class="food_item">
							<span class="name">{{food.name}}</span>
							<div class="food_right">
								<div class="price_box">￥<span class="price">{{food.price}}</span></div>
								<car-ctrl :food="food"></car-ctrl>
							</div>

						</li>
					</ul>
				</div>

			</div>
		</transition>

	</div>
</template>

<script>
	import BScroll from "better-scroll";
	import carCtrl from "components/carCtrl/carCtrl";
	export default {
		props: {
			selectFoods: {
				type: Array,
				default(){
					return [];
				}
			},
			deliveryPrice: {
				type: Number
			},
			minPrice: {
				type: Number
			}
		},
		data() {
			return {
				balls: [{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					}
				],
				dropBall: [],
				fold: true

			}
		},
		computed: {
			totalPrice() {
				let totalPrice = 0;
				this.selectFoods.forEach((food) => {
					totalPrice += food.price * food.count
				})
				return totalPrice;
			},
			totalCount() {
				let count = 0;
				this.selectFoods.forEach((food) => {
					count += food.count;
				})
				return count;

			},
			disPrice() {

				if(this.totalPrice == 0) {
					return `￥${this.minPrice}元起送`;
				} else if(this.totalPrice < this.minPrice) {
					let disPrice = this.minPrice - this.totalPrice;
					return `还差￥${disPrice}元起送`;;

				} else {
					return '去结算';
				}

			},
			listShow() {
				if(!this.totalCount) {
					this.fold = true;
					return false;
				}
				let show = !this.fold;
				if(show) {
					this.$nextTick(() => {
						if(!this.scroll) {
							this.scroll = new BScroll(this.$refs.listContent, {
								click: true
							});
						} else {
							this.scroll.refresh();
						}

					})
				}
				return show;
			}
		},
		methods: {
			drop(el) {
				for(let i = 0; i < this.balls.length; i++) {
					let ball = this.balls[i];
					if(!ball.show) {
						ball.show = true;
						ball.el = el;
						this.dropBall.push(ball);
						return;
					}
				}
			},
			beforeDrop(el) {
				let count = this.balls.length;
				while(count--) {
					let ball = this.balls[count];
					if(ball.show) {
						let rect = ball.el.getBoundingClientRect();
						let x = rect.left - 32;
						let y = -(window.innerHeight - rect.top - 40);
						el.style.display = "";
						el.style.webkitTransform = `translate3d(0,${y}px,0)`;
						el.style.transform = `translate3d(0,${y}px,0)`;
						let inner = el.getElementsByClassName("inner-hook")[0];
						inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
						inner.style.transform = `translate3d(${x}px,0,0)`;
					}
				}
			},
			dropping(el, done) {
				let rf = el.offsetHeight;
				this.$nextTick(() => {
					el.style.webkitTransform = 'translate3d(0,0,0)';
					el.style.transform = 'translate3d(0,0,0)';
					let inner = el.getElementsByClassName("inner-hook")[0];
					inner.style.webkitTransform = 'translate3d(0,0,0)';
					inner.style.transform = 'translate3d(0,0,0)';
					el.addEventListener('transitionend', done);
				})
			},
			afterDrop(el) {
				let ball = this.dropBall.shift();
				if(ball) {
					ball.show = false;
					el.style.display = "none";
				}
			},
			onOff() {
				if(this.totalCount) {
					this.fold = !this.fold;
				}

			},
			cleanCar() {
				this.selectFoods.forEach((food) => {
					food.count = 0;
				})
			},
			hideList(){
				this.fold=true;
			},
			pay(){
				if(this.totalPrice<this.minPrice){
					return ;
				}
				alert(`需支付${this.totalPrice+this.deliveryPrice}元`)
			}
		},
		components: {
			carCtrl
		}
	};
</script>

<style scoped lang="scss">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.shop_car {
		display: flex;
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 10;
		width: 100%;
		height: 2.3rem;
		.content {
			position: relative;
			width: 100%;
			display: flex;
			z-index:100;
			.content_left {
				flex: 1;
				background: #141d27;
				.car_box {
					position: relative;
					display: inline-block;
					left: 0.6rem;
					bottom: 0.7rem;
					width: 2.2rem;
					height: 2.2rem;
					margin-right: 0.4rem;
					border: 0.3rem solid #141d27;
					border-radius: 50%;
					background: #2b333b;
					&.heightLight {
						background: rgb(0, 160, 220);
					}
					.icon-shopping_cart {
						display: inline-block;
						color: #80858a;
						font-size: 1.2rem;
						line-height: 2.2rem;
						margin: 0 0.6rem;
						&.heightLight {
							color: #fff;
						}
					}
					.count {
						position: absolute;
						top: -0.2rem;
						right: -0.5rem;
						display: block;
						width: 1.2rem;
						height: 0.8rem;
						vertical-align: top;
						line-height: 0.8rem;
						font-size: 0.45rem;
						text-align: center;
						color: #fff;
						background: rgb(240, 20, 20);
						border-radius: 10px;
					}
				}
				.price,
				.send_pay {
					position: relative;
					top: 0.6rem;
					display: inline-block;
					vertical-align: top;
					font-size: 0.8rem;
					line-height: 1.2rem;
					color: rgba(255, 255, 255, 0.4);
				}
				.price {
					margin-right: 0.6rem;
					padding-right: 0.6rem;
					border-right: 1px solid rgba(255, 255, 255, 0.1);
					font-weight: 700;
					&.heightLight {
						color: #fff;
					}
				}
				.send_pay {
					font-size: 0.5rem;
				}
			}
			.content_right {
				flex: 0 0 5.25rem;
				height: 100%;
				width: 5.25rem;
				line-height: 2.3rem;
				font-size: 0.6rem;
				font-weight: 700;
				color: rgba(255, 255, 255, 0.4);
				text-align: center;
				background: #2b333b;
				&.heightLight {
					background: #00b43c;
					color: #fff;
				}
			}
		}
		.ball_box {
			.ball {
				position: fixed;
				left: 1.6rem;
				bottom: 1.1rem;
				z-index: 200;
				transition: all 0.4s cubic-bezier(.89, .09, .74, .49);
				.inner {
					width: 0.8rem;
					height: 0.8rem;
					border-radius: 50%;
					background: rgb(0, 160, 220);
					transition: all 0.4s linear;
				}
			}
		}
		.mask {
			position: fixed;
			top: 0;
			bottom: 0;
			width: 100%;
			min-height: 100%;
			backdrop-filter: blur(10px);
			background: rgba(7, 17, 27, 0.6);
			z-index:30;
			&.fade-enter-active, &.fade-leave-active{
				transition: all 0.5s;
			}
			&.fade-enter, &.fade-leave-active{
				opacity: 0;
                background: rgba(7, 17, 27, 0);
			}
		}
		.food_list {
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			background: #fff;
			z-index: 40;
			transform: translate3d(0, -100%, 0);
			&.fold-enter-active,
			&.fold-leave-active {
				transition: all 0.5s
			}
			&.fold-enter,
			&.fold-leave-active {
				transform: translate3d(0, 0, 0);
			}
			.list_head {
				display: flex;
				width: 100%;
				height: 2rem;
				line-height: 2rem;
				justify-content: space-between;
				background: #f3f5f7;
				border-bottom: 2px solid rgba(7, 17, 27, 0.1);
				.title {
					padding-left: 0.9rem;
					font-size: 0.7rem;
					color: rgb(7, 17, 27);
					font-weight: 200;
				}
				.clean_car {
					padding-right: 1.2rem;
					font-size: 0.6rem;
					color: rgb(0, 160, 220);
				}
			}
			.list_content {
				max-height: 15.25rem;
				overflow: hidden;
				ul {
					
					.food_item {
						display: flex;
						padding: 0.6rem 0.9rem;
						justify-content: space-between;
						height: 1.2rem;
						line-height: 1.2rem;
						@include border-1px(rgba(7, 17, 27, 0.1));
						.name {
							font-size: 0.7rem;
							color: rgb(7, 17, 27);
						}
						.food_right {
							display: flex;
							justify-content: space-between;
							.price_box {
								margin: 0 0.3rem 0 0.9rem;
								font-size: 0.5rem;
								color: rgb(240, 20, 20);
								.price {
									font-size: 0.7rem;
									font-weight: 700;
								}
							}
							.car_ctrl {
								position: relative;
								top: -0.3rem;
							}
						}
					}
				}
			}
		}
	}
</style>
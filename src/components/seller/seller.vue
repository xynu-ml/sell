<template>
	<div>
		<div class="seller" ref="seller">
			<div class="seller_content">
				<div class="seller_score">
					<h3 class="name">{{seller.name}}</h3>
					<div class="star_box border-1px">
						<star :size="36" :score="seller.score"></star>
						<span class="rank">({{seller.ratingCount}})</span>
						<span class="sale_count">月售{{seller.sellCount}}单</span>
					</div>
					<ul class="other_info">
						<li class="info">
							<span class="text">起送价</span>
							<p class="price_box"><span class="price">{{seller.minPrice}}</span>元</p>
						</li>
						<li class="info">
							<span class="text">商家配送</span>
							<p class="price_box"><span class="price">{{seller.deliveryPrice}}</span>元</p>
						</li>
						<li class="info">
							<span class="text">平均配送时间</span>
							<p class="price_box"><span class="price">{{seller.deliveryTime}}</span>分钟</p>
						</li>

					</ul>
				</div>
				<div class="fav" @click="favClick($event)" >
					<i class="icon icon-favorite" :class="{active:favFlag}"></i>
					<p class="text">{{fav}}</p>
				</div>
				<split></split>
				<div class="seller_actives" >
					<h3 class="title">公告与活动</h3>
					<p class="bulletin">{{seller.bulletin}}</p>
					<ul class="supports">
						<li class="support" v-for="support in seller.supports">
							<i class="icon" :class="iconArr[support.type]"></i><span class="text">{{support.description}}</span>
						</li>
					</ul>
				</div>
				<split></split>
				<div class="seller_pic">
					<h3 class="title">商家实景</h3>
					<div class="pics_wrapper" ref="picsWrapper">
						<ul class="pic_list" ref="picList">
							<li v-for="pic in seller.pics" class="pic">
								<img :src="pic" alt="" width="100%" height="100%" />
							</li>
						</ul>
					</div>
				</div>
				<split></split>
				<div class="seller_info">
					<h3 class="title">商家信息</h3>
					<ul class="info_list">
						<li class="info" v-for="info in seller.infos">{{info}}</li>
					</ul>
				</div>

			</div>
		</div>
	</div>
</template>

<script>
	import BScroll from "better-scroll";
	import star from "components/star/star";
	import split from "components/split/split";
	import car from "components/car/car";
	export default {

		props: {
			seller: {
				type: Object
			},
			foods: {
				type: Array,
				default() {
					return [];
				}
			}
		},
		data() {
			return {
				iconArr: ["decrease", "discount", "spcial", "invioce", "guarantee"],
				favFlag:false
			}
		},
		created() {
			this.$nextTick(() => {
				this.initScroll();
				this.initPics();
			})
		},
		mounted(){
			this.$nextTick(() => {
				this.initScroll();
				this.initPics();
			})
		},
		computed: {
           fav(){
           	   if(!this.favFlag){
           	   	return "收藏";
           	   }else{
           	   	return "已收藏";
           	   }
           	    
           }
		},
		methods: {
			initScroll() {
				if(!this.scroll) {
					this.scroll = new BScroll(this.$refs.seller, {
						click: true
					});
				} else {
					this.scroll.refresh();
				}
			},
			initPics() {
				if(!this.seller || !this.seller.pics) {
					return;
				}
				
                let picWidth = 120;
				let picMargin = 6;
				let picsWidth = (picWidth + picMargin) * this.seller.pics.length - picMargin;
				this.$refs.picList.style.width = picsWidth + "px";
				if(!this.picsScroll) {
					this.picsScroll = new BScroll(this.$refs.picsWrapper, {
						scrollX: true,
						eventPassthrough: 'vertical'
					})
				} else {
					this.picsScroll.refresh();
				}
			},
		    favClick(event){
		    	if(!event._constructed) {
					return;
				}
		    	this.favFlag=!this.favFlag;
		    }
		},
		components: {
			star,
			split,
			car
		},
		watch: {
			'seller' () {
				this.$nextTick(() => {
					this.initScroll();
					this.initPics();
				})
			}
		},
	};
</script>

<style lang="scss" scoped="">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.seller {
		position: fixed;
		top: 8.8rem;
		bottom: 0;
		left: 0;
		width: 100%;
		overflow: hidden;
		.seller_content {
			width: 100%;
			.seller_score {
				padding: 0.9rem;
				.name {
					font-size: 0.7rem;
					line-height: 0.7rem;
					color: rgb(7, 17, 27);
				}
				.star_box {
					display: flex;
					line-height: 0.9rem;
					padding: 0.4rem 0 0.9rem;
					font-size: 0.5rem;
					color: rgb(77, 85, 93);
					@include border-1px(rgba(7, 17, 27, 0.1)) .rank {
						margin-left: 0.4rem;
					}
					.sale_count {
						margin-left: 0.6rem;
					}
				}
				.other_info {
					display: flex;
					margin-top: 0.9rem;
					.info {
						flex: 1;
						text-align: center;
						border-right: 1px solid rgba(7, 17, 27, 0.1);
						box-sizing: border-box;
						&:last-of-type {
							border: 0;
						}
						.text {
							display: block;
							padding-bottom: 0.2rem;
							font-size: 0.5rem;
							color: rgb(147, 153, 159);
						}
						.price_box {
							font-size: 0.5rem;
							color: rgb(7, 17, 27);
							.price {
								font-size: 1.2rem;
								font-weight: 400;
								line-height: 1.2rem;
							}
						}
					}
				}
			}
			.fav{
				width:2.5rem;
				position:absolute;
				top:0.5rem;
				right:0.9rem;
				text-align: center;
				.icon{
					display: inline-block;
					font-size:1.2rem;
					line-height: 1.2rem;
					color:#ddd;
					&.active{
						color:rgb(240,20,20);
					}
					
				}
				.text{
					font-size:0.5rem;
					line-height: 0.5rem;
					color:rgb(77,85,93);
				}
				
			}
			.seller_actives {
				padding: 0.9rem 0.9rem 0;
				.title {
					font-size: 0.7rem;
					color: rgb(7, 17, 27);
				}
				.bulletin {
					padding: 0 0.6rem;
					margin-top: 0.4rem;
					line-height: 1.2rem;
					font-size: 0.6rem;
					font-weight: 200;
					color: rgb(240, 20, 20);
				}
				.supports {
					margin-top: 0.8rem;
					.support {
						padding: 0.8rem 0.6rem;
						line-height: 0.8rem;
						border-top: 1px solid rgba(7, 17, 27, 0.1);
						.icon {
							display: inline-block;
							width: 0.8rem;
							height: 0.8rem;
							background-size: 0.8rem;
							background-repeat: no-repeat;
							&.decrease {
								@include bg-image("decrease_4");
							}
							&.discount {
								@include bg-image("discount_4");
							}
							&.spcial {
								@include bg-image("special_4");
							}
							&.invioce {
								@include bg-image("invoice_4");
							}
							&.guarantee {
								@include bg-image("guarantee_4");
							}
						}
						.text {
							display: inline-block;
							vertical-align: top;
							margin-left: 0.3rem;
							font-size: 0.6rem;
							font-weight: 200;
							color: rgb(7, 17, 27);
						}
					}
				}
			}
			.seller_pic {
				padding: 0.9rem;
				.title {
					font-size: 0.7rem;
					color: rgb(7, 17, 27);
				}
				.pics_wrapper {
					width: 100%;
					margin-top: 0.6rem;
					overflow: hidden;
					white-space: nowrap;
					.pic_list {
						font-size: 0;
						.pic {
							display: inline-block;
							width: 120px;
							height: 90px;
							margin-right: 6px;
							&:last-child {
								margin-right: 0;
							}
						}
					}
				}
			}
			.seller_info {
				padding: 0.9rem 0.9rem 0;
				.title {
					font-size: 0.7rem;
					color: rgb(7, 17, 27);
				}
				.info_list {
					margin-top: 0.6rem;
					.info {
						padding: 0.8rem 0.6rem;
						font-size: 0.6rem;
						font-weight: 200;
						color: rgb(7, 17, 27);
						line-height: 0.8rem;
						border-top: 1px solid rgba(7, 17, 27, 0.1);
					}
				}
			}
		}
	}
</style>
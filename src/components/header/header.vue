<template>
	<div class="header">
		<div class="header_content">
			<div class="content_left">
				<img :src="seller.avatar" alt="" />
			</div>
			<div class="content_right">
				<div class="title">
					<span class="band"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
				<div v-if="seller.supports" class="actives">
					<span class="icon"></span>
					<span class="text">{{seller.supports[0].description}}</span>
					<span class="actives_num" @click="showDetail">{{seller.supports.length}}个 <i class="icon-keyboard_arrow_right"></i></span>
				</div>
			</div>
		</div>
		<div class="header_notice" @click="showDetail">
			<span class="notice_icon"></span><span class="notice_text">{{seller.bulletin}}</span><i class="icon-keyboard_arrow_right"></i>
		</div>
		<div class="header_bg">
			<img :src="seller.avatar" alt="" width="100%" />
		</div>
		<transition name="fade">
			<div class="head_detail" v-show="detailShow" >
				<div class="detail_main clearfix">
					<div class="detail_content">
						<h2 class="detail_title">{{seller.name}}</h2>
						<star :size="48" :score="seller.score"></star>
						<div class="detail_discount" v-if="seller.supports">
							<div class="discount_header">
								<div class="line"></div>
								<div class="text">优惠信息</div>
								<div class="line"></div>
							</div>
							<ul class="discount_info">
								<li v-for="item in seller.supports">
									<span class="icon" :class="classMap[item.type]"></span>
									<span class="description">{{item.description}}</span>
								</li>
							</ul>

						</div>
						<div class="detail_discount" v-if="seller.bulletin">
							<div class="discount_header">
								<div class="line"></div>
								<div class="text">商家公告</div>
								<div class="line"></div>
							</div>
							<p class="detail_bulletin">{{seller.bulletin}}</p>

						</div>

					</div>
				</div>
				<div class="detail_close ">
					<i class="icon-close" @click="showDetail"></i>
				</div>
			</div>
		</transition>

	</div>
</template>

<script>
	import star from "components/star/star"

	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				detailShow: false,
				classMap: ["decrease", "discount", "spcial", "invioce", "guarantee"]
			}
		},
		methods: {
			showDetail() {
				this.detailShow = !this.detailShow;
			}
		},
		components: {
			star
		}
	};
</script>

<style scoped lang="scss">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.header {
		position: relative;
		background: rgba(7, 17, 27, 0.5);
		overflow: hidden;
		.decrease {
			@include bg-image("decrease_2");
		}
		.discount {
			@include bg-image("discount_2");
		}
		.spcial {
			@include bg-image("discount_2");
		}
		.invioce {
			@include bg-image("special_2");
		}
		.guarantee {
			@include bg-image("special_2");
		}
		.header_content {
			display: flex;
			padding: 1.2rem 0.6rem 0.9rem 1.2rem;
			.content_left {
				img {
					width: 3.2rem;
					height: 3.2rem;
					border-radius: 0.1rem;
				}
			}
			.content_right {
				display: inline-block;
				color: #fff;
				margin-left: 0.8rem;
				.title {
					margin: 0.1rem 0 0.4rem 0;
					overflow: hidden;
					span {
						float: left;
						font-size: 0.8rem;
						&.band {
							width: 1.5rem;
							height: 0.9rem;
							margin-right: 0.3rem;
							@include bg-image("brand");
							background-size: 1.5rem 0.9rem;
							background-repeat: no-repeat;
						}
						&.name {
							font-weight: bold;
							line-height: 0.9rem;
						}
					}
				}
				.description {
					font-size: 0.6rem;
					font-weight: 200;
					margin-bottom: 0.5rem;
				}
				.actives {
					width: 90%;
					margin-bottom: 0.1rem;
					overflow: hidden;
					span {
						float: left;
						&.icon {
							width: 0.6rem;
							height: 0.6rem;
							margin-right: 0.2rem;
							@include bg-image("decrease_1");
							background-size: 0.6rem;
							background-repeat: no-repeat;
						}
						&.text {
							font-size: 0.5rem;
							font-weight: 200;
						}
						&.actives_num {
							float: right;
							position: absolute;
							right: 0;
							font-size: 0.5rem;
							padding: 0.35rem 0.4rem;
							background: rgba(0, 0, 0, 0.2);
							margin-right: 0.6rem;
							border-radius: 0.7rem;
							line-height: 0.6rem;
							transform: translateY(-28%);
							.icon-keyboard_arrow_right {
								display: inline-block;
								vertical-align: top;
								line-height: 0.6rem;
							}
						}
					}
				}
			}
		}
		.header_notice {
			position: relative;
			padding: 0 1.2rem 0 0.6rem;
			;
			background: rgba(7, 17, 27, 0.2);
			height: 1.4rem;
			line-height: 1.4rem;
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;
			font-size: 0.5rem;
			color: #fff;
			.notice_icon {
				display: inline-block;
				vertical-align: top;
				width: 1.1rem;
				height: 0.6rem;
				margin-top: 0.4rem;
				margin-right: 0.2rem;
				@include bg-image("bulletin");
				background-repeat: no-repeat;
				background-size: 1.1rem 0.6rem;
			}
			.notice_text {
				font-weight: 200;
				vertical-align: top;
			}
			.icon-keyboard_arrow_right {
				position: absolute;
				right: 0.6rem;
				bottom: 0.4rem;
				vertical-align: top;
			}
		}
		.header_bg {
			width: 100%;
			height: 100%;
			position: absolute;
			overflow: hidden;
			top: 0;
			left: 0;
			z-index: -1;
			filter: blur(0.5rem);
		}
		.head_detail {
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			overflow: auto;
			transition: all 0.5s;
			background: rgba(7, 17, 27, 0.8);
			&.fade-enter-active, &.fade-leave-active{
				opacity: 1;
				background: rgba(7, 17, 27, 0.8);
			}
			&.fade-enter, &.fade-leave-active{
				opacity: 0;
				background: rgba(7, 17, 27, 0);
			}
			z-index: 100;
			color: #fff;
			.detail_main {
				width: 100%;
				min-height: 100%;
				.detail_content {
					margin-top: 3.2rem;
					padding-bottom: 3.2rem;
					.detail_title {
						font-size: 0.8rem;
						line-height: 0.8rem;
						font-weight: 700;
						text-align: center;
					}
					.star {
						margin: 0.8rem auto 0;
						line-height: 1.2rem;
						text-align: center;
					}
					.detail_discount {
						color: #fff;
						width: 80%;
						margin: 1.4rem auto 0;
						.discount_header {
							display: flex;
							line-height: 1.4rem;
							.line {
								flex: 1;
								position: relative;
								top: 0.7rem;
								height: 0.05rem;
								background: rgba(255, 255, 255, 0.2);
							}
							.text {
								font-size: 0.7rem;
								padding: 0 0.6rem;
								font-weight: 700;
							}
						}
						.discount_info {
							margin-top: 0.6rem;
							li {
								margin-top: 0.6rem;
								font-size: 0;
								.icon {
									display: inline-block;
									width: 0.8rem;
									height: 0.8rem;
									margin-right: 0.3rem;
									background-size: 0.8rem;
									background-repeat: no-repeat;
								}
								.description {
									font-size: 0.6rem;
									line-height: 0.8rem;
									font-weight: 200;
									vertical-align: top;
								}
							}
						}
						.detail_bulletin {
							padding: 0 0.6rem;
							font-size: 0.6rem;
							font-weight: 200;
							line-height: 1.2rem;
						}
					}
				}
			}
			.detail_close {
				position: relative;
				margin-top: -3.2rem;
				text-align: center;
				clear: both;
				font-size: 1.6rem;
				color: rgba(255, 255, 255, 0.5);
			}
		}
	}
</style>
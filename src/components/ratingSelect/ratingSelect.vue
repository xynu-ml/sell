<template>
	<div class="ratingSelect">
		<ul class="select_type">
			<li class="all" :class="{active:selectType===2}" @click="select(2,$event)">{{desc.all}}<span>{{ratings.length}}</span></li>
			<li class="positive" :class="{active:selectType===0}" @click="select(0,$event)">{{desc.positive}}<span>{{positive.length}}</span></li>
			<li class="negative" :class="{active:selectType===1}" @click="select(1,$event)">{{desc.negative}}<span>{{negative.length}}</span></li>
		</ul>
		<div class="only_content border-1px" @click="toggleContent($event)" :class="{on:onlyContent}">
			<i class="icon-check_circle"></i><span class="text">只看有内容的评价</span>
		</div>
</div>
</template>

<script>
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		props: {
			ratings: {
				type: Array,
				default() {
					return [];
				}
			},
			type: {
				type: Number,
				default: ALL
			},
			only: {
				type: Boolean,
				default: false
			},
			desc: {
				type: Object,
				default() {
					return {
						all: "全部",
						positive: "满意",
						negative: "不满意"
					}
				}
			}
		},
		data() {
			return {
				selectType: this.type,
				onlyContent: this.only,

			}
		},
		watch: {
			type(val) {
				this.selectType = val;
			},
			only(val) {
				this.onlyContent = val;
			}
		},
		computed: {
			positive() {
				return this.ratings.filter((rating) => {
					return rating.rateType === POSITIVE;
				})

			},
			negative() {
				return this.ratings.filter((rating) => {
					return rating.rateType === NEGATIVE;
				})

			}
		},
		methods: {
			select(type, event) {
//				console.log(11)
				if(!event._constructed) {
					return;
				}
				
				this.selectType = type;
				this.$emit("ratingtype", type);
			},
			toggleContent(event) {
				if(!event._constructed) {
					return;
				}
				this.onlyContent = !this.onlyContent;
				this.$emit("onlycontent", this.onlyContent);
			}

		}
	};
</script>

<style lang="scss" scoped="">
	@import '../../common/sass/index.scss';
	@import '../../common/sass/icon.css';
	.ratingSelect {
		.select_type {
			padding: 0.9rem 0;
			display: flex;
			@include border-1px(rgba(7, 17, 27, 0.1));
			li {
				padding: 0.4rem 0.6rem;
				border-radius: 0.1rem;
				margin-right: 0.4rem;
				font-size: 0.6rem;
				line-height: 0.8rem;
				color: rgb(77, 85, 93);
				span {
					font-size: 0.4rem;
					margin-left: 0.2rem;
				}
			}
			.all,
			.positive {
				background: rgba(0, 160, 220, 0.2);
				&.active {
					color: #fff;
					background: rgb(0, 160, 220);
				}
			}
			.negative {
				background: rgba(77, 85, 93, 0.2);
				&.active {
					color: #fff;
					background: rgb(77, 85, 93);
				}
			}
		}
		.only_content {
			padding: 0.6rem;
			line-height: 1.2rem;
			@include border-1px(rgba(7, 17, 27, 0.1));
			color: rgb(147, 153, 159);
			&.on {
				.icon-check_circle {
					color: #00c850;
				}
			}
			.icon {
				font-size: 1.2rem;
			}
			.text {
				vertical-align: top;
				margin-left: 0.2rem;
				font-size: 0.6rem;
			}
		}
	}
</style>
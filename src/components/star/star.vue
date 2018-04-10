<template>
	<div class="star" :class="starType">
		<div class="star-item" v-for="itemClass in itemClasses" :class="itemClass"></div>
	</div>
</template>
<script>
	const length = 5;
	const starOn = "on";
	const starHalf = "half";
	const starOff = "off";
	export default {
		props: {
			size: {
				type: Number
			},
			score: {
				type: Number
			}
		},
		computed: {
			starType() {

				return "star-" + this.size;
			},
			itemClasses() {
				let result = [];
				let score = Math.round(this.score);
				let isHalf = score % 1 != 0;
				let scoreInt = parseInt(score);
				for(let i = 0; i < scoreInt; i++) {
					result.push(starOn);
				}
				if(isHalf) {
					result.push(starHalf);
				}
				while(result.length < length) {
					result.push(starOff);
				}
				return result;
			}
		}
	};
</script>

<style scoped lang="scss" >
	@import '../../common/sass/index.scss';
	.star {
		.star-item {
			display: inline-block;
			vertical-align:top;
			background-repeat: no-repeat;
			
		}
		&.star-48{
			.star-item{
				width: 1rem;
				height: 1rem;
				background-size: 1rem;
				margin-right: 1.1rem;
				&:last-of-type {
					margin-right: 0;
				}
			}
			.on{
				@include bg-image("star48_on");
			}
			.half{
				@include bg-image("star48_half");
			}
			.off{
				@include bg-image("star48_off");
			}
		}
		&.star-36{
			.star-item{
				width: 0.75rem;
				height: 0.75rem;
				background-size: 0.75rem;
				margin-right: 0.3rem;
				&:last-of-type {
					margin-right: 0;
				}
			}
			.on{
				@include bg-image("star36_on");
			}
			.half{
				@include bg-image("star36_half");
			}
			.off{
				@include bg-image("star36_off");
			}
		}
		&.star-24{
			.star-item{
				width: 0.5rem;
				height: 0.5rem;
				background-size: 0.5rem;
				margin-right:0.2rem;
				&:last-of-type {
					margin-right: 0;
				}
			}
			.on{
				@include bg-image("star24_on");
			}
			.half{
				@include bg-image("star24_half");
			}
			.off{
				@include bg-image("star24_off");
			}
		}
	}
</style>
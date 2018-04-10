<template>
	<div class="car_ctrl">
		<transition name="move">
			<div class="car_decrease" v-show="food.count>0" @click.stop.prevent="mix($event)" transition="move">
			    <span class="icon-remove_circle_outline"></span>
			</div>
		</transition>
		
		<div class="car_count" v-show="food.count>0">{{food.count}}</div>
		<div class="car_add icon-add_circle" @click.stop.prevent="add($event)"></div>
	</div>
</template>

<script>
	import Vue from 'vue';
	export default{
		props:{
			food:{
				type:Object
			}
		},
		methods:{
			add(event){
				if(!event._constructed){
					return ;
				}
				if(!this.food.count){
					Vue.set(this.food,"count",1);
				}else{
					this.food.count++;
				}
				this.$emit('addCart', event.target);
				
			},
			mix(event){
				if(!event._constructed){
					return ;
				}
				if(this.food.count){
				  this.food.count--;
				}
			}
		}
	}
</script>

<style scoped="" lang="scss">
@import '../../common/sass/index.scss';
@import '../../common/sass/icon.css';
.car_ctrl{
	font-size:0;
	div{
		display: inline-block;
		color:rgb(0,160,220);
		
	}
	.car_decrease,.car_add{
		padding:0.3rem;
		font-size:1.2rem;
		line-height: 1.2rem;
		
	}
	.car_decrease{
		transition: all 0.3s linear;
		.icon-remove_circle_outline{
			display:inline-block;
			transition: all 0.3s linear;
		}
		&.move-transition{
			opacity: 1;
			transform: translate3d(0,0,0);
			.icon-remove_circle_outline{
				transform: rotate(0);
			}
		}
		&.move-enter, &.move-leave{
			opacity: 0;
			transform: translate3d(1.2rem,0,0);
			.icon-remove_circle_outline{
				transform: rotate(180deg);
			}
		}
	}
	.car_count{
		vertical-align: top;
		font-size:0.5rem;
		margin-top:0.3rem;
		line-height: 1.2rem;
		color:rgb(147,153,159);
	}
}
</style>
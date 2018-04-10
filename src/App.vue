<template>
	<div id="app">
		<v-header :seller='seller'></v-header>
		<div class="tab border-1px">
			<div class="tab-item">
				<router-link to="/goods">商品</router-link>
			</div>
			<div class="tab-item">
				<router-link to="/ratings" :seller="seller">评价</router-link>
			</div>
			<div class="tab-item">
				<router-link to="/seller" :seller="seller">商家</router-link>
			</div>
		</div>
		<keep-alive>
			<router-view :seller="seller"></router-view>
		</keep-alive>

	</div>
</template>

<script>
	import Header from './components/header/header'
	export default {
		data() {
			return {
				seller: {}
			}
		},
		created() {
			this.$http.get('/api/seller', '', {
				emulateJSON: true
			}).then((res) => {
				if(res.body.errno == 0) {
					this.seller = res.body.data;
				}
			})
		},
		components: {
			'v-header': Header
		},
		name: 'App'
	}
</script>

<style lang="scss">
	@import 'common/sass/index.scss';
	#app .tab {
		display: flex;
		height: 2rem;
		line-height: 2rem;
		@include border-1px(rgba(7,17,27,0.1));
		.tab-item {
			flex: 1;
			text-align: center;
			a {
				display: block;
				font-size: 0.7rem;
				color: rgb(77, 85, 93);
				&.active {
					color: rgb(240, 20, 20);
				}
			}
		}
	}
</style>
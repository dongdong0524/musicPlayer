<template>
	<transition name="slide-right">
		<div class="content">
			<div class="content-wrapper" v-if='bookAvlable'>
				<div class="content-item"  v-if='bookAvlable'  v-for='(item,index) in navigation.toc'
					 :key='index' @click='jumpTo(item.href)'>
					<span class="text">{{item.label}}</span>
				</div>
			</div>
			<div class="empty" v-else>加载中...</div>
		</div>
	</transition>
	
</template>

<script>
export default{
	props:{
		ifShowContent:Boolean,
		navigation:Object,
		bookAvlable:Boolean
	},
	methods:{
		jumpTo(href){
			this.$emit('jumpTo',href);
		}
	}
		
}
</script>

<style lang='scss' scoped>
@import '../assets/styles/global.scss';
.content{
	    position: absolute;
	    top: 0;
	    left: 0;
	    z-index: 104;
	    background: #fff;
	    width: 80%;
	    height: 100%;
	.content-wrapper{
	    width: 100%;
	    height: 100%;
		.content-item{
			padding:px2rem(20) px2rem(20);
			border-bottom: px2rem(1) solid #f4f4f4;
			cursor:pointer;
			.text {
	          font-size: px2rem(16);
	          color: #333;
	        }
		}
	}
	.empty {
      width: 100%;
      height: 100%;
      @include center;
      font-size: px2rem(16);
      color: #333;
    }
	
}

</style>
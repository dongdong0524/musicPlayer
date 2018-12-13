<template>
	<div class='menu-bar'>
		<transition name='slide-up'>
			<div class="menu-wrapper" :class="{'hide-boxShadow':isSettingShow || !title}" v-show="title">
				<div class="icon-wrapper">
					<span class="icon-menu icon" @click="showSetting(3)"></span>
				</div>
				<div class="icon-wrapper">
					<span class="icon-progress icon" @click="showSetting(2)"></span>
				</div>
				<div class="icon-wrapper">
					<span class="icon-bright icon" @click="showSetting(1)"></span>
				</div>
				<div class="icon-wrapper">
					<span class="icon-a icon" @click="showSetting(0)">A</span>
				</div>
			</div>
		</transition>
		<transition name='slide-up'>
			<div class="setting-wrapper"   v-show="isSettingShow" ref="setting" >
				<div class="setting-font-size" v-if="showTag===0">
					<div class="preview" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
					<div class='select'>
						<div class="select-wrapper" @click="setFontSize(item.fontSize)"  v-for="(item,index) in fontSizeList" :key="index">
							<div class="line"></div>
							<div class="point-wrapper" v-show="defaultSize===item.fontSize">
								<div class="point">
									<div class="small-point"></div>
								</div>
							</div>
							<div class="line"></div>
						</div>
					</div>
					<div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
				</div>
				
				<div class="setting-theme" v-else-if="showTag===1">
					<div class="setting-theme-item" @click="setThemeFn(index)" v-for="(item,index) in themeList" :key="index">
						<div class="preview" :style="{background:item.style.body.background}"
							:class="{'border':item.style.body.background==='#fff'}"
							></div>
						<div class="text" :class="{'selectCheck':index===defaultTheme}">{{item.name}}</div>
					</div>
				</div>
				
				<div class="setting-progress" v-else-if="showTag===2">
					<div class="progress-wrapper">
						<input type="change" class="progress" max="100" min="0" :value="progress" step='1' ref='progress'
							 @change="onProgressChange($event.target.value)"  :disabled="!bookAvlable"
							 @input="onProgressInput($event.target.value)"
							/>
					</div>
					<div class="text-wrapper">
						<span>{{bookAvlable?progress+'%':'加载中...'}}</span>
					</div>
				</div>
				
				
			</div>
		</transition>
		<content-view  :ifShowContent='ifShowContent'
			v-show='ifShowContent' :navigation='navigation'  :bookAvlable='bookAvlable' @jumpTo='jumpTo'></content-view>
		<transition name='fade'>
			<div class="content-mask"
				v-show='ifShowContent' @click='hideContent'>
			</div>
			
		</transition>
		
		
		
	</div>	
</template>

<script>
import ContentView from '@/components/Content'	
	
	
export default{
	components:{
		ContentView
	},
	props:{
		title:{
			type:Boolean,
			default:false 
		},
		fontSizeList:{
			type:Array
		},
		defaultSize:Number,
		themeList:Array,
		defaultTheme:Number,
		bookAvlable:{
			type:Boolean,
			default:false
		},
		navigation:Object
		
	},
	data(){
		return{
			isSettingShow:false,
			showTag:0,
			progress:0,
			ifShowContent:false
		}
	},
	methods:{
		//跳转目录
		jumpTo(target){
			this.$emit('jumpTo',target);
		},
		//隐藏目录
		hideContent(){
			this.ifShowContent=false;
		},
		
		/*进度条*/
		onProgressInput(progress){
			this.progress=progress;
			this.$refs.progress.style.backgroundSize=`${this.progress}% 100%`;
		},
		/*进度条变化*/
		onProgressChange(progress){
			this.$emit('onProgressChange',progress);
		},
		/*展示底部菜单*/
		showSetting(tag){
			this.showTag=tag;  //置
			if(tag===3){  //目录单独判断
				this.isSettingShow=false; //底部菜单隐藏
				this.ifShowContent=true;  //展示左边目录层
			}else{
				this.isSettingShow=true;  //底部菜单显示
			}
		},
		/*隐藏底部菜单*/
		hideSetting(){
			this.isSettingShow=false;
		},
		//传递给父组件操作，因为要用父组件的一些东西
		setFontSize(fontSize){
			this.$emit("setFontSize",fontSize);
			
		},
		//传递给父组件操作  父组件监听setTheme
		setThemeFn(index){
			this.$emit("setTheme",index);
		}
		
		
	}
	
	
}
	
	
</script>

<style lang="scss" scoped>
@import '../assets/styles/global.scss';
/*2种方法选择第一个带边框  CSS伪类  和 动态绑定样式判断*/
.border{
	border:1px solid deeppink;
}
input[type=range]{ 
		 margin-top: 8px;
	         outline: none; 
		 -webkit-appearance: none;/*清除系统默认样式*/  
		 width:56% !important;  
		 background: -webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd;  
	         background-size: 30% 100%;/*设置左右宽度比例*/  
		height: 3px;/*横条的高度*/  
}
input[type=range]::-webkit-slider-thumb {  
	 -webkit-appearance: none;/*清除系统默认样式*/  
	height:16px;/*拖动块高度*/  
    width: 16px;/*拖动块宽度*/  
    background: #fff;/*拖动块背景*/  
	border-radius: 50%; /*外观设置为圆形*/  
	border: solid 1px #ddd; /*设置边框*/  
}
.menu-bar{
	.menu-wrapper{
		position:absolute;
		bottom:0;
		left:0;
		z-index:101;
		width:100%;
		height:px2rem(48);
		background:white;
		display:flex;
		box-shadow:0 px2rem(-8) px2rem(8)  rgba(0,0,0,0.15);
		
		&.hide-boxShadow{
			box-shadow: none;
		}	
		.icon-wrapper{
			flex:1;
			@include center;
			.icon-progress{
				font-size:px2rem(24);
			}
			.icon-bright{
				font-size:px2rem(24);
			}
			
		}
	/*动画效果*/
	/*会把效果包裹到transition标签的下一个div中，所以加个&标示menu-wrapper同级*/
	}
	
	.setting-wrapper{
		position:absolute;
		bottom: px2rem(48);
		left:0;
		z-index: 101;
		width:100%;
		height:px2rem(60);
		box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,0.15);
		background: #fff;
		/*设置字体*/
		.setting-font-size{
			display:flex;
			height:100%;
			.select{
				display:flex;
				flex:1;
				.select-wrapper{
					flex:1;
					align-items:center;
					display:flex;
					&:first-child{
						.line{
							:first-child{
								border-top:none;
							}
						}
						
					}
					&:last-child{
						.line{
							:last-child{
								border-top:none;
							}
						}
						
					}
					.line{
						flex:1;
						height:0;
						border-top:px2rem(1) solid #ccc;
					}
					
				}
			}
			
			
			.point-wrapper{
				flex:0 0 0;
				width:0;
				height:px2rem(7);
				border-left:px2rem(1) solid #ccc;
				position:relative;
				.point{
					position:absolute;
					top:px2rem(-8);
					left:px2rem(-8);
					height:px2rem(20);
					width:px2rem(20);
					border-radius:50%;
					background:#fff;
					border:px2rem(1) solid #ccc;
					box-shadow:0 px2rem(4) px2rem(4) #ccc;
					@include center;
					.small-point{
						height:px2rem(5);
						width:px2rem(5);
						border-radius:50%;
						background:#333;
						
					}
				}
				
				
				
			}
			.preview{
				flex:0 0 px2rem(40);
				@include center;
			}
			
			
			
		}
		/*设置主题*/
		.setting-theme{
			height:100%;
			display:flex;
			.setting-theme-item{
				flex:1;
				display:flex;
				flex-direction:column;
				padding:px2rem(5);
				box-sizing:border-box;
				.preview{
					flex:1;
				}
				.text{
					flex:0 0 px2rem(20);
					font-size:px2rem(14);
					text-align:center;
					color:#ddd;
				}
				.selectCheck{
					color:#333;
				}
			
				&.setting-theme-item:first-child{
					.preview{
						border:1px solid #ccc;
					}
				}	
				
			}

			
		}
		/*设置进度*/
		.setting-progress{
			position:relative;
			width:100%;
			height:100%;
			.progress-wrapper{
				width:100%;
				height:100%;
				@include center;
				padding:0 px2rem(30);
				box-sizing:border-box;
				.progress{
					width: 100%;
		            -webkit-appearance: none;
		            height: px2rem(2);
		            background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
		            background-size: 0 100%;
					&:focus {
		              outline: none;
		            }
					&::-webkit-slider-thumb {
		              -webkit-appearance: none;
		              height: px2rem(20);
		              width: px2rem(20);
		              border-radius: 50%;
		              background: white;
		              box-shadow: 0 4px 4px 0 rgba(0, 0, 0, .15);
		              border: px2rem(1) solid #ddd;
					}
				}
			}
			.text-wrapper{
				position: absolute;
		          left: 0;
		          bottom: 0;
		          width: 100%;
		          color: #333;
		          font-size: px2rem(12);
		          text-align: center;
			}
			
		}
		
		
	}
	
	/*目录结构*/
	.content-mask{
		position:absolute;
		top:0;
		left:0;
		z-index:101;
		display:flex;
		width:100%;
		height:100%;
		background:rgba(51,51,51,.8);
	}	
	
	
	
}





</style>
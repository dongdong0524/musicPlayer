<template>
	<div  class='ebook'>
		<titlebar :title="ifTitleAndMenuShow"></titlebar>
		<div class="read-wrapper">
			<div id="read"></div>
			<div class="mask">
				<div class="left" @click="prevPage" ></div>
				<div class="center" @click='toggleTitleMenu'></div>
				<div class="right" @click='nextPage'></div>
			</div>
		</div>
		<!--子组件传递过来setTheme ，父组件接受后面的setTheme-->
		<menubar :title="ifTitleAndMenuShow" ref="menuBar" 
				:fontSizeList="fontSizeList" :defaultSize="defaultSize" @setFontSize="setFontSizeDetail"
				:themeList="themeList" :defaultTheme="defaultTheme"    @setTheme='setThemeDetail'
				:bookAvlable="bookAvlable" @onProgressChange="onProgressChange"
				:navigation='navigation'  @jumpTo='jumpTo'
				
			></menubar>
	</div>
</template>
 
<script>
import Epub  from   'epubjs'
import titlebar	from '@/components/TitleBar'
import menubar	from     '@/components/Menu'


const url='/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.ePub = Epub
export default{
	data(){
		return {/*初始化默认值*/
			ifTitleAndMenuShow:false,  /*初始化默认隐藏*/
			fontSizeList:[{fontSize:12},{fontSize:14},{fontSize:16},{fontSize:18},{fontSize:20},{fontSize:22},{fontSize:24}
			],
			defaultSize:16,
			defaultTheme:0, /*默认主题*/
			themeList:[{name:'default',style:{body:{'color':'#000','background':'#fff'}}},
			{name:'eye',style:{body:{'color':'#000','background':'#ceebad'}}},
			{name:'night',style:{body:{'color':'#fff','background':'#000'}}},
			{name:'gold',style:{body:{'color':'#000','background':'rgb(241,236,226)'}}}
			],
			bookAvlable:false,  /*默认是否禁用*/
			navigation:{}
		}
	},
	methods:{
		/*目录跳转*/
		jumpTo(href){
			this.rendition.display(href);
			this.hideTitleAndMenu();
		},
		/*进度条*/
		onProgressChange(progress){
			const percentage = progress / 100 ;
			const location = percentage > 0 ?this.locations.cfiFromPercentage(percentage):0;
			this.rendition.display(location)
		},
		
		//父组件监听setTheme 方法 具体实现 主题详细 
		setThemeDetail(index){
			this.themes.select(this.themeList[index].name);
			this.defaultTheme=index;
		},//设置主题
		registerTheme(){//遍历主题
			this.themeList.forEach(theme=>{//
				this.themes.register(theme.name,theme.style)
				
			})
		},
		
		//父组件监听子组件事件,做具体的操作
		setFontSizeDetail(fontSize){
			//先传默认值
			this.defaultSize=fontSize;
			//设置字体
			if(this.themes){
				this.themes.fontSize(fontSize+'px');
			}
		},
		//隐藏title和底部菜单
		hideTitleAndMenu(){
			this.ifTitleAndMenuShow=false;
			this.$refs.menuBar.hideSetting();  //隐藏底部menu
			this.$refs.menuBar.hideContent();  //隐藏目录
		},
		
		//显示菜单
		toggleTitleMenu(){
			/*具体实现变量*/
			this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
			this.$refs.menuBar.hideSetting();
		},
		
		//上一页
		prevPage(){
			if(this.rendition){
				this.rendition.prev();
			}
		},
		//下一页
		nextPage(){
			if(this.rendition){
				this.rendition.next();
			}
		},
		//电子书解析和渲染
		showEpub(){
			//生存Ebook对象
			this.book = new Epub(url)
			//生成Rendition
			this.rendition=this.book.renderTo('read',{
				width:window.innerWidth,
				height:window.innerHeight
			})
			//显示
			this.rendition.display();
			console.log(this.book)
			//获取主题对象
			this.themes=this.rendition.themes;
			//设置默认字体  调取方法
			this.setFontSizeDetail(this.defaultSize);
			
			this.registerTheme();
			this.setThemeDetail(this.defaultTheme);
			//console.log(this.book.locations);
			//通过epubjs的钩子函数来实现
			this.book.ready.then(()=>{
				//目录
				this.navigation=this.book.navigation;
				console.log('=='+this.navigation);
				return this.book.locations.generate();  /*必须返回*/
			}).then(result=>{
				this.locations=this.book.locations; /*成功后获取位置*/
				this.bookAvlable=true;    /*状态完成可以操作*/
			})
			
			
		}
		
	},/*生命周期 创建时候*/
	created:function(){
		this.showEpub();
	},/*注册组件*/
	components:{
		titlebar,
		menubar
	}
}
</script>

<style   lang="scss" scoped>
/**/
	@import 'assets/styles/global.scss';
	.ebook{
		position:relative;
		.read-wrapper{
			.mask{
				position:absolute;
				width:100%;
				height:100%;
				top:0;
				left:0;
				z-index:100;
				display:flex;
				.left{
					flex:0 0 px2rem(100);
				}
				.center{
					flex:1;
				}
				.right{
					flex:0 0 px2rem(100);
				}
				
			}
		}
		
	} 
</style>
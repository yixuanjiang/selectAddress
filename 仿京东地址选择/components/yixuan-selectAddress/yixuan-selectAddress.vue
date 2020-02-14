<template>
	<view class="wrapper" v-show="isShowMask">
		<transition name="content">
			<view class="content_view" v-show="isShow">
				<view class="title_view">
					<view class="title">请选择所在地区</view>
					<view class="close_view" @click="hidden">
						<icon class="close_icon" :type="'clear'" size="26"/>
					</view>
				</view>
				<view class="select_top">
					<view class="select_top_item" ref="select_top_item" v-for="(item,index) in dataList" :key="index" @click="select_top_item_click(index)">
						<text>{{item}}</text>
					</view>
					<view class="indicator" ref="indicator"></view>
				</view>
				<swiper class="swiper" :current="currentIndex" @change="swiperChange">
					<swiper-item v-for="(swiper_item,swiper_index) in dataList" :key="swiper_index">
						<view class="swiper-item">
							<scroll-view class="scroll-view-item" scroll-y="true">
								<view class="address_item" v-for="(item,index) in cityAreaArray[swiper_index]" :key="index" @click="address_item_click(swiper_index,index)">
									<image v-if="selectIndexArr[swiper_index] === index" class="address_item_icon" src="../../static/yixuan-selectAddress/gou.png" mode=""></image>
									{{item.name}}
								</view>
							</scroll-view>
						</view>
					</swiper-item>
				</swiper>
			</view>
		</transition>
		<view class="mask" @click="hidden" v-show="isShowMask"></view>
	</view>
</template>

<script>
	import cityData from '../../static/yixuan-selectAddress/city.json'
	export default {
		data() {
			return {
				isShow: false,
				isShowMask: false,
				dataList: ['请选择'],
				currentIndex: 0,
				cityData: {},
				cityAreaArray: [],
				selectIndexArr: []
			};
		},
		methods:{
			show(){
				this.isShow = true
				this.isShowMask = true
			},
			hidden() {
				this.isShow = false
				setTimeout(() => { 
					this.isShowMask = false
					}, 500);
			},
			select_top_item_click(index) {
				this.currentIndex = index
				
				this.changeIndicator(index)
				
			},
			swiperChange(event){
				let index = event.detail.current
				this.currentIndex = index
				this.changeIndicator(index)
			},
			changeIndicator(index){
				let itemWidth = this.$refs.select_top_item[index].$children[0].$el.offsetWidth
				if (itemWidth > 80){
					itemWidth = 80
				}
				let itemCenterX = 10 + index * 80 + itemWidth / 2
				let indicatorWidth = this.$refs.indicator.$el.offsetWidth
				this.$refs.indicator.$el.style.left = itemCenterX - indicatorWidth / 2 + 'px'
			},
			address_item_click(swiper_index,index){
				// console.log(swiper_index,index)
				this.selectIndexArr.splice(swiper_index, 5,index)
				
				//判断当前是否为最下一级
				if (swiper_index === 0){//第一级
				
					let currentObj = this.cityData[index]
					let city = currentObj.name
					console.log(city)
					this.dataList.splice(swiper_index,5,city)
					this.dataList.splice(swiper_index + 1,0,'请选择')
					this.cityAreaArray.splice(swiper_index + 1, 1,currentObj["city"])
					
					
					
					setTimeout(() => {
						this.currentIndex = 1
						this.changeIndicator(1)
						}, 50);
					
		
				}else {
					let currentAreaArray = this.cityAreaArray[swiper_index]
					let currentObj = currentAreaArray[index]
					let area = currentObj["area"]
					console.log(currentAreaArray)
					if (area !== undefined){
						
						let city = currentObj.name
			
						this.dataList.splice(swiper_index,5,city)
						this.dataList.splice(swiper_index + 1,0,'请选择')
						this.cityAreaArray.splice(swiper_index + 1,1,currentObj["area"])
						
						setTimeout(() => {
							this.currentIndex = swiper_index + 1
							this.changeIndicator(swiper_index + 1)
							}, 50);
						
						
					}else{//是最下一级
						let city = currentObj.name
						this.dataList.splice(swiper_index,1,city)
						
						//选择成功返回数据
						this.$emit("selectAddress", this.dataList.join(''))
						
						this.isShow = false
						setTimeout(() => { 
							this.isShowMask = false
							}, 500);
					}
					
				}
				
			}
		},
		created() {
			this.cityData = cityData
			this.cityAreaArray.push(cityData)
		},
		mounted() {
			this.changeIndicator(0)
		}
	}
</script>

<style lang="scss">
// 不换行
@mixin no-wrap(){
	text-overflow: ellipsis;
	overflow: hidden;
	white-space: nowrap;
}
.wrapper{
	z-index: 1999;
	position: absolute;
	top: -44px;
	left: 0;
	bottom: 0;
	right: 0;
	.content_view{
		z-index: 999;
		background: white;
		position: absolute;
		height: 80%;
		left: 0;
		bottom: 0;
		right: 0;
		border-top-left-radius: 20px;
		border-top-right-radius: 20px;
		.title_view{
			height: 12%;
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 0 $uni-spacing-row-sm;
			.title{
				font-size: uni-font-size-sm;
			}
			.close_view{
				height: 60px;
				width: 60px;
				display: flex;
				justify-content: center;
				align-items: center;
			}
		}
		.select_top{
			height: 8%;
			display: flex;
			justify-content: start;
			align-items: center;
			padding: 10px;
			position: relative;
			box-sizing: border-box;
			.select_top_item{
				width: 80px;
				font-size: 14px;
				@include no-wrap();
			}
			.indicator{
				position: absolute;
				width: 30px;
				height: 2px;
				background: $uni-color-error;
				left: 10px;
				bottom: 0;
				transition: left 0.5s ease;
			}
		}
		.swiper{
			height: 80%;
			position: relative;
			left: 0;
			top: 0;
			bottom: 0;
			right: 0;
			.swiper-item{
				height: 100%;
				.scroll-view-item{
					height: 100%;
					padding: 0 10px;
					.address_item{
						padding: 5px 0;
						font-size: 14px;
						display: flex;
						align-items: center;
						.address_item_icon{
							width: 20px;
							height: 20px;
							margin-right: 10px;
						}
					}
				}
			}
		}
	}
	.mask{
		position: absolute;
		top: 0;
		left: 0;
		bottom: 0;
		right: 0;
		background: $uni-text-color-grey;
		opacity: 0.7;
	}
}

.content-enter{
  transform: translateY(100%);
}
.content-enter-to{
  transform: translateY(0%);
}
.content-enter-active{
  transition: transform 0.5s;
}
.content-leave{
  transform: translateY(0%);
}
.content-leave-to{
  transform: translateY(100%);
}
.content-leave-active{
  transition: transform 0.5s;
}

</style>

<template>
	<view class="content">
		<view class='top_btn'>
			<image @click="back()" class='back' style="width: 36rpx;height: 36rpx;" src="../../static/images/back.png"
				mode="widthFix"></image>
			<view class='title'>
				<text class="active" v-if="type=='add'" >新增收货地址</text>
				<text class="active" v-if="type=='edit'" >编辑收货地址</text>
			</view>
			<image v-if="type=='edit'" src="../../static/images/add_icon.png" style="width: 84rpx;height:26rpx;"></image>
		</view>
		
		<view class='address_box'>
			<form @submit="formSubmit" @reset="formReset">
				<view class='box'>
					<view class="item">
						<view class="list">
							<view>
								<text>收货人</text>
							</view>
							<view class='right'>
								<input style="width: 100%;" type="text" placeholder-class="placeholder" placeholder="请输入收货人姓名" v-model="address.name" />
								<image src="../../static/images/more_icon.png" style="width: 14rpx;height: 24rpx;margin-left: 15rpx;"></image>
							</view>
						</view>
						<view class="list">
							<view>
								<text>手机号</text>
							</view>
							<view class='right'>
								<input style="width: 100%;" type="text" placeholder-class="placeholder" placeholder="请输入手机号" v-model="address.phone" />
								<text style="margin-left: 20rpx;">+86</text>
								<image src="../../static/images/more_icon.png" style="width: 14rpx;height: 24rpx;margin-left: 15rpx;"></image>
							</view>
						</view>
						<picker class="pickerList" mode="multiSelector" @click="open_pic" :range="newProvinceDataList" :value="multiIndex" @change="pickerChange" @columnchange="pickerColumnchange">
							<view class="list">
								<view>
									<text>所在地区</text>
								</view>
								<view class='right'>
									<text class='placeholder' v-if="address.region==''">省、市、区</text>
									<text v-else>{{address.province}} {{address.city}} {{address.region}}</text>
									<image src="../../static/images/more_icon.png" style="width: 14rpx;height: 24rpx;margin-left: 15rpx;"></image>
								</view>
							</view>
						</picker>
						<view class="list">
							<view>
								<text>详细地址</text>
							</view>
							<view class='right'>
								<input style="width: 100%;" type="text" placeholder-class="placeholder" placeholder="小区楼栋/乡村名称" v-model="address.address" />
								<image src="../../static/images/more_icon.png" style="width: 14rpx;height: 24rpx;margin-left: 15rpx;"></image>
							</view>
						</view>
						<view class="list">
							<view>
								<text>邮编</text>
							</view>
							<view class='right'>
								<input style="width: 100%;" type="text" placeholder-class="placeholder" placeholder="邮编" v-model="address.code" />
								<image src="../../static/images/more_icon.png" style="width: 14rpx;height: 24rpx;margin-left: 15rpx;"></image>
							</view>
						</view>
					</view>
					<view class='item'>
						<view class="list" style="line-height: 44rpx;padding: 32rpx 30rpx;">
							<view>
								<view>设为默认地址</view>
								<view style="font-size: 28rpx;font-family: PingFang SC;font-weight: 500;color: #708099;margin-top: 8rpx;">提醒：下单会优先使用该地址</view>
							</view>
							<view class='right'>
								<image @click="change_default()" :src="address.is_default?'../../static/images/open_switch.png':'../../static/images/close_switch.png'" style="width: 88rpx;height: 44rpx;"></image>
							</view>
						</view>
					</view>
				</view>
				<view class='ok' :style="'opacity:'+(address.name&&address.phone&&address.province&&address.address?'1':'0.5')" @click="comfirm_give()">
					<text>保存</text>
				</view>
			</form>
		</view>
	</view>
</template>

<script>
	import provinceData from '../../static/provinceData.js';
	export default {
		data() {
			return {
				type:'add',
				address:{
					name:'',
					phone:'',
					province:'',
					city:'',
					region:'',
					address:'',
					code:'',
					is_default:false
				},
				oldpProvinceDataList: provinceData.data,
				newProvinceDataList:[
					[],[],[]
				],
				multiIndex: [0, 0, 0],
				select:'',
				is_change_picker:false,
			}
		},
		onLoad(options) {
			this.type = options.type?options.type:'add';
			if(this.type=='add'){
				//组装第一级列表
				for(let i=0; i<this.oldpProvinceDataList.length; i++){
					this.newProvinceDataList[0].push(this.oldpProvinceDataList[i].name);
				}
				//组装第二级列表
				for(let i=0; i<this.oldpProvinceDataList[0].city.length; i++){
					this.newProvinceDataList[1].push(this.oldpProvinceDataList[0].city[i].name);
				}
				//组装第三级列表
				for(let i=0; i<this.oldpProvinceDataList[0].city[0].area.length; i++){
					this.newProvinceDataList[2].push(this.oldpProvinceDataList[0].city[0].area[i]);
				}
			}else{
				this.city_init()
			}
		},
		methods: {
			
			back() {
				var pages = getCurrentPages();
				if(pages.length>1){
					for(var i = 2;i<pages.length;i++){
						var prePage = pages[pages.length - i]
						console.log(i,prePage.route)
						if(prePage.route=='pages/setup/update_password'){
							uni.navigateBack({
								delta:i-1
							})
							break;
						}
					}
				}else{
					uni.navigateBack()
				}
			},
			formSubmit(){
				console.log('提交')
			},
			formReset(){
				console.log('重置')
			},
			//切换默认
			change_default(){
				this.address.is_default = this.address.is_default?false:true;
			},
			city_init(){
				if(this.address.province!=''){
					this.newProvinceDataList = [[],[],[]];
					//组装第一级列表
					for(let i=0; i<this.oldpProvinceDataList.length; i++){
						this.newProvinceDataList[0].push(this.oldpProvinceDataList[i].name);
					}
					//获取用户当前第一级所在下标
					var index1 = this.newProvinceDataList[0].findIndex(value=>value == this.address.province)
					//组装第二级列表
					for(let i=0; i<this.oldpProvinceDataList[index1].city.length; i++){
						this.newProvinceDataList[1].push(this.oldpProvinceDataList[index1].city[i].name);
					}
					//获取用户当前第二级所在下标
					var index2 = this.newProvinceDataList[1].findIndex(value=>value == this.address.city)
					//组装第三级列表
					for(let i=0; i<this.oldpProvinceDataList[index1].city[index2].area.length; i++){
						this.newProvinceDataList[2].push(this.oldpProvinceDataList[index1].city[index2].area[i]);
					}
					//获取用户当前第三级所在下标
					var index3 = this.newProvinceDataList[2].findIndex(value=>value == this.address.region)
					this.multiIndex = [index1,index2,index3];
				}
			},
			open_pic(){
				this.is_change_picker && this.city_init()
				this.is_change_picker=false
			},
			// 省市区确认事件
			pickerChange(e){
				this.multiIndex = e.detail.value;
				// console.log(this.multiIndex)
				
				this.address.province = this.newProvinceDataList[0][this.multiIndex[0]]
				this.address.city = this.newProvinceDataList[1][this.multiIndex[1]]
				this.address.region = this.newProvinceDataList[2][this.multiIndex[2]]
			},
			pickerColumnchange(e){
				this.is_change_picker = true;
				// 第几列滑动
				// console.log(e.detail.column);
				// 第几列滑动的下标
				// console.log(e.detail.value)
				// 第一列滑动
				if(e.detail.column === 0){
					this.multiIndex[0] =  e.detail.value
					// console.log('第一列滑动');
					// this.newProvinceDataList[1] = [];
					this.newProvinceDataList[1] = this.oldpProvinceDataList[this.multiIndex[0]].city.map((item,index)=>{
						// console.log(item)
						return item.name
					})
					// console.log(this.multiIndex)
					if(this.oldpProvinceDataList[this.multiIndex[0]].city.length === 1){
						this.newProvinceDataList[2] = this.oldpProvinceDataList[this.multiIndex[0]].city[0].area.map((item,index)=>{
							// console.log(item)
							return item
						})
					}else{
						this.newProvinceDataList[2] = this.oldpProvinceDataList[this.multiIndex[0]].city[this.multiIndex[1]].area.map((item,index)=>{
							// console.log(item)
							return item
						})
					}
					// 第一列滑动  第二列 和第三列 都变为第一个
					this.multiIndex.splice(1, 1, 0)
					this.multiIndex.splice(2, 1, 0)
				}
				// 第二列滑动
				if(e.detail.column === 1){
					this.multiIndex[1] =  e.detail.value
					// console.log('第二列滑动');
					// console.log(this.multiIndex)
					this.newProvinceDataList[2] = this.oldpProvinceDataList[this.multiIndex[0]].city[this.multiIndex[1]].area.map((item,index)=>{
						// console.log(item)
						return item
					})
					// 第二列 滑动 第三列 变成第一个
					this.multiIndex.splice(2, 1, 0)
				}
				// 第三列滑动
				if(e.detail.column === 2){
					this.multiIndex[2] =  e.detail.value
					// console.log('第三列滑动')
					// console.log(this.multiIndex)
				}
			}
		}
	}
</script>

<style>
	.content {
		color: #fff;
		padding: 0 52rpx;
		padding-top: calc(var(--status-bar-height) + 108rpx);
		overflow: hidden;
		height: calc(100vh - calc(var(--status-bar-height) + 108rpx));
		/* background: #0F1A26; */
		background: url(../../static/images/login_bg.png) no-repeat;
		background-size: 100% 100%;
		background-position: left top;
		font-family: PingFang SC;
	}
	
	.top_btn {
		display: flex;
		position: fixed;
		width: calc(100% - 64rpx);
		top: 0;
		left: 0;
		justify-content: space-between;
		z-index: 3;
		align-items: center;
		color: #fff;
		padding: calc(var(--status-bar-height) + 20rpx) 32rpx 0 32rpx;
		line-height: 88rpx;
		height: 88rpx;
	}
	.title{
		position: absolute;
		margin: auto;
		left: 0;right: 0;
		text-align: center;
		width: fit-content;
	}
	.title text{
		margin: 0 26rpx;
		font-size: 28rpx;
		color: #A3BBE0;
		font-weight: 500;
	}
	.title .active{
		color: #fff;
		font-size: 32rpx;
		font-weight: bold;
	}
	
	.box{
		margin-top: 43rpx;
	}
	.item{
		background-color: #142333;
		margin-top: 32rpx;
		overflow: hidden;
		border-radius: 24rpx;
	}
	.list{
		padding: 0 26rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		line-height: 112rpx;
		color: #fff;
		font-weight: 500;
		font-size: 32rpx;
	}
	
	.right{
		display: flex;
		align-items: center;
		color: #A3BBE0;
		font-size: 28rpx;
		/* width: 500rpx; */
		justify-content: right;
		text-align: right;
	}
	.placeholder{
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #708099;
	}
	.ok{
		height: 88rpx;
		background: linear-gradient(90deg, #8B3AD7, #1A4BD9);
		border-radius: 44rpx;
		text-align: center;
		line-height: 88rpx;
		font-size: 32rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		margin: auto;
		margin-top: 280rpx;
	}
</style>
